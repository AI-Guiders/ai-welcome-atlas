---
type: Knowledge
title: 'Radzen Blazor — канонический проход по всему стеку'
description: 'Полный проход: архитектура по исходникам, все семейства компонентов по докам (~470 URL), паттерны данных, ловушки. Radzen.Blazor v.x (master, .NET 8/9/10). Собран тремя агентами (доки-ядро, полный обход, исходники), синтез 2026-11-08.'
tags: [radzen, blazor, software-dotnet-csharp, kb, canon]
language: ru-RU
world: software
subworld: dotnet-csharp
---
# Radzen Blazor — канонический проход по всему стеку

**Что это:** библиотека UI-компонентов `Radzen.Blazor` (NuGet), 155+ компонентов, исходники MIT (github.com/radzenhq/radzen-blazor). Поддержка .NET 8/9/10, Server/WASM/InteractiveAuto, CSP. Платная подписка Pro/Team = support SLA + premium-темы + scaffolding/Studio, НЕ функциональность ядра.

**Метод сбора:** доки blazor.radzen.com (инвентарь по sitemap.xml ~470 URL, все страницы DataGrid-раздела + все семейства; не открылся только /aichat — таймаут ×3) + исходники master (RadzenDataGrid.razor.cs 4954 строки, QueryableExtension, PropertyAccess, DropDownBase, Radzen.Blazor.js 10 181 строка, DialogService, базовые классы). Глубокий разбор DataGrid — в [`kb-blazor-radzen-datagrid-fundamentals.md`](kb-blazor-radzen-datagrid-fundamentals.md); здесь — полный стек.

## 1. Установка и каркас

```
dotnet add package Radzen.Blazor
```
1. `_Imports.razor`: `@using Radzen` + `@using Radzen.Blazor`.
2. `App.razor <head>`: `<RadzenTheme Theme="material" />`.
3. `Program.cs`: `builder.Services.AddRadzenComponents();`
4. После последнего `<script>`: `<script src="_content/Radzen.Blazor/Radzen.Blazor.js?v=@(typeof(Radzen.Colors).Assembly.GetName().Version)"></script>` — один JS-файл на всё.
5. В `MainLayout`: `<RadzenComponents @rendermode="InteractiveAuto" />` — хост Dialog/Notification/ContextMenu/Tooltip сервисов; render mode тот же, что у приложения.

**Render modes:** интерактивным компонентам нужен `@rendermode` (InteractiveServer/Auto/WASM). Static SSR без ограничений — только layout-компоненты без событий (Card/Stack/Row…). **RadzenChart — исключение: SVG рендерится на C#, работает под static SSR без JS.**

## 2. Архитектура (по исходникам)

- **`RadzenComponent : ComponentBase, IDisposable`** — корень: `Attributes` (`CaptureUnmatchedValues` — произвольные html-атрибуты), `UniqueID` (base64-фрагмент GUID), `Visible` (невидимый не рендерится вовсе; `OnBecameInvisible` чистит JS-обработчики), каскадные `Culture/UICulture`, `Localize(key)`, `Debounce(fn, 500)`. Разметка — в `.razor` наследников; ручной RenderTreeBuilder — точечно (например, ячейки грида ради стабильных sequence numbers).
- **`FormComponent<T>`**: `Name`, `Disabled`, `Value/ValueChanged` (`@bind-Value`), дополнительный `Change`-колбэк; интеграция с EditContext (`FieldIdentifier`), `rz-state-empty`. Событийный порядок: `ValueChanged` → `EditContext.NotifyFieldChanged` → `Change`.
- **`PagedDataBoundComponent<T>`**: `Count`, `LoadData`, `Reload()`.
- **`DropDownBase<T>`** — база dropdown/multiselect/autocomplete: JS `Radzen.togglePopup`, фильтрация (дебаунс `FilterDelay`), `SelectItem` → ValueChanged/Change, полная клавиатура (стрелки, type-ahead, Ctrl+A, Esc).
- **JS-мост `Radzen.Blazor.js`**: попапы (smart-position, перенос в `document.body`, стек вложенных, закрытие по mousedown-вне/resize), drag&drop (grid reorder, Gantt), resize колонок, frozen-позиции через CSS-переменные `--rz-grid-frozen-left-N`, клавиатурный фокус, mask/numeric-инпуты, tabs-overflow, upload XHR. Клипборд-хелпера как API нет.
- **Темы**: SCSS-исходники в `Radzen.Blazor/themes/` (MIT), CSS-переменные `--rz-*`; swatches/premium — PRO. Рантайм: `ThemeService` (`SetTheme`, `SetRightToLeft`, `SetWcag`, `ThemeChanged`, cookie-персистенция). WCAG-AA наборы `[theme]-wcag.css` бесплатны.
- **Локализация**: `RadzenStrings.resx` (embedded), ключи `ComponentName_PropertyName`; встроены en/de/fr/es/it/ja/pt. Способы: DI `ILocalizer` / satellite-assembly (свой resx с `LogicalName="Radzen.Blazor.RadzenStrings"`) / пер-инстансные текстовые свойства (высший приоритет). Сервиса «LocalizationService» нет.

## 3. Паттерны данных (общие для всех)

- **In-memory**: `Data` = `IEnumerable<T>` — сортировка/фильтрация/пейджинг компонентом (грид: через собственный expression-движок поверх System.Linq.Dynamic.Core).
- **IQueryable**: операции транслируются в SQL провайдером (EF). `FilterCaseSensitivity.CaseInsensitive` для in-memory даёт OrdinalIgnoreCase-сравнения, для EF — `ToLower` (OrdinalIgnoreCase провайдер не переварит — код выбирает путь сам).
- **LoadData (серверный)**: `LoadDataArgs { Skip, Top, OrderBy (строка), Filter (строка) }`; обработчик даёт и `Data`, и `Count`. Паттерн: `query.Where(grid.ColumnsCollection)` → `OrderBy(args.OrderBy)` → `Count()` → `Skip/Take`. **Prerendering-ловушка**: LoadData не срабатывает в статический prerender — грузить первую страницу в `OnInitializedAsync` + `PersistentComponentState` (иначе пустой HTML). Относится и к DropDown/AutoComplete/ListBox/DataList.
- **События**: инпуты — `ValueChanged`/`Change` (Blur по умолчанию; `Immediate="true"` = на каждый символ). Грид — `Sort/Page/Filter/RowUpdate/RowCreate/Group/ColumnReorder(ed/ing)/Render`.

## 4. Семейство таблиц и данных

- **RadzenDataGrid** — ядро; полный разбор в DataGrid-файле. Кратко: FilterMode `Simple|SimpleWithMenu|Advanced|CheckBoxList` (дефолт **Advanced**), сортировка/мультисортировка/`SortComparer`, группировка `Groups` (GroupDescriptor) + `GroupHeaderTemplate/GroupFooterTemplate`, `AllowVirtualization`, frozen (`Frozen` + `FrozenPosition Left/Right`), колонки reorder/picker (`PickedColumnsChanged`), editing (`EditMode Single/Multiple`, incell, `EditRow/InsertRow/UpdateRow/CancelEditRow`), selection (Single/Multiple/cell, `@bind-Value` на `IList<T>`), иерархия через `Template` с вложенными гридами (`ExpandMode`, `ExpandRow`), `Settings` (save/restore page/sort/filter), **экспорт CSV/XLSX целиком в браузере** (`ExportToExcelAsync/ExportToCsvAsync`, chunked для LoadData; колонки с Template — через `ExportValue`+`FormatString`). PDF-экспорта нет.
- **RadzenTable** — статическая HTML-таблица. **RadzenDataList** — список с `Template` (+virtualization/paging). **RadzenDataFilter** — конструктор фильтров (`RadzenDataFilterProperty`, `ViewChanged(IQueryable<T>)`).
- **RadzenPager** (`Count/PageSize/PageChanged`), **RadzenPickList** (`@bind-Source/@bind-Target`), **RadzenSpreadsheet** (Workbook/формулы/XLSX — open source).
- **RadzenTree**: inline-элементы или `Data` + `RadzenTreeLevel` (`TextProperty/ChildrenProperty/HasChildren` — ленивая загрузка в `Expand`); selection `@bind-Value`, чекбоксы `@bind-CheckedValues` (tri-state родителям — вручную); drag&drop встроенного нет.
- **RadzenGantt**: плоский `Data` + `IdProperty/ParentIdProperty` (иерархия сама), зависимости `GanttDependency<T>`, `RadzenGanttColumn`, drag/resize, critical path, baselines.

## 5. Charts (SVG на C#, static SSR ок)

- Паттерн: `<RadzenChart>` → серии → оси. Серия: `Data`, `CategoryProperty` (X: string/date/number, иначе индекс), `ValueProperty` (Y: только числовые). Доступ к данным — скомпилированные делегаты `PropertyAccess` с кэшем.
- Серии: Line/Spline/Step, Area (+Range/Step/Stacked/100%), Bar/Column (+Range/Stacked/100%/Histogram/Waterfall), Pie/Donut (`Fills`, `TitleTemplate` в центре), Funnel/Pyramid, Scatter/Bubble (`CategoryProperty`=X!), Candlestick/OHLC (`Bull/Bear`), BoxPlot (квартили считать самому), Bullet, Heatmap (`ColorRange`), Contour, Treemap, Sankey, Sparkline, Spider/Radar. Тренды: `RadzenSeriesTrendLine/PolynomialTrendLine/MovingAverageLine/MeanLine`, `ReferenceLine/ReferenceBand`.
- Оси: `RadzenValueAxis/RadzenCategoryAxis` (`Min/Max/Step/FormatString/Formatter`, `LabelRotation/LabelAutoRotation`, `Logarithmic`, `Inverted`, `CrossesAt`); мультиоси: `Name`+`ValueAxisName`; crosshair — per-axis. Легенда `RadzenLegend` (`Visible=false`, `ItemTemplate`), тултипы per-series `TooltipTemplate` или глобальный `RadzenChartTooltipOptions` (`Shared/Split`).
- Interaction: `AllowZoom/AllowPan/@bind-Zoom/ViewChange/ResetZoom()`, `SyncGroup` (синхрон нескольких чартов), `AnimateDataUpdates`, `SeriesClick` (drilldown вручную). Live: таймер + `InvokeAsync(StateHasChanged)`, список пересоздавать каждый тик.
- Гейджи: Arc/Linear/Radial — `Scale` + `ScaleValue` (несколько указателей ок).
- Ловушка: nullable double в ValueProperty — Radzen компилирует геттер с `.Value`, первый null убивает circuit на легенде/тултипе (DashSpec: маппить null → 0).

## 6. Scheduler

- Виды-дети: `RadzenDayView/WeekView/MonthView/MultiDayView/AgendaView/YearView/YearPlannerView/YearTimelineView`; `SelectedIndex` — стартовый.
- `TItem/Data` + `StartProperty/EndProperty/TextProperty/AllDayProperty` (класса RadzenAppointment нет — модель пользователя).
- События: `SlotSelect/AppointmentSelect/DaySelect/MonthSelect/AppointmentMove/AppointmentResize/AppointmentMouseEnter/Leave`. Кастомизация: `SlotRender/AppointmentRender` через `args.Attributes`; **`StateHasChanged` в `AppointmentRender` — бесконечный цикл (запрещено доками)**.
- Ресурсы: `RadzenSchedulerResource`; у видов `GroupByResource/GroupBy/GroupOrientation`. Обновление: `scheduler.Reload()`.

## 7. Формы и инпуты

Единый паттерн: `@bind-Value` либо `Value`+`Change`; `Name` + `<RadzenLabel Component="...">`; `InputSize`. `RadzenTemplateForm` (`Data` или свой EditContext; `Submit/InvalidSubmit`; `Action/Method` для нативного submit) + валидаторы (`Component="InputName"`, `Text`, `Popup`): Required/Compare/Email/Length/NumericRange/Regex/DataAnnotation/Custom (`Validator="@(...) => bool"`).

- Текст: TextBox (`Immediate`), TextArea, Password, Mask (`Mask="(***) ***-****"`, литералы маски остаются в значении).
- Числа/даты: Numeric (`Min/Max/Step/Format`, nullable для очистки), **DatePicker** (`Multiple` → `IEnumerable<DateTime?>`, `ShowTime/TimeOnly`, `ParseInput` — свои форматы, `DateRender` (args.Disabled), `Inline`, `AllowClear`, DateOnly/TimeOnly поддержаны; обновление на blur без `Immediate`), DateRange (`DateRange` Start/End), TimeSpanPicker (`FieldPrecision`).
- Списки: **Dropdown** (`TextProperty/ValueProperty`, `AllowClear`, `Template/HeaderTemplate/FooterTemplate/ValueTemplate`, **фильтрация case-sensitive по умолчанию** — `FilterCaseSensitivity`, virtualization+LoadData, группировка встроенной нет — header-элементы + `ItemRender`), DropDownDataGrid, AutoComplete (`MinLength`, `LoadData(args.Filter)`), ListBox, CheckBoxList (`AllowSelectAll`), SelectBar, RadioButtonList.
- Булевы: CheckBox (`TriState` → `bool?`), Switch, Slider (`Range=true` → `IEnumerable<int>`), Rating (`Stars`, не Max).
- Файлы: **FileInput** (base64/byte[] в модель — НЕ стриминг, большие файлы раздувают запрос), **Upload** (`Url/Multiple/Auto`+`Upload()`, `Stream=true` → только один файл, `MaxFileSize/AllowedExtensions` — расширения не проверяют MIME, валидировать на сервере; события `Change/Progress/Rejected`).
- Кнопки: Button (`ButtonStyle/Variant/Shade/IsBusy`), ToggleButton, SplitButton (item==null = основная), Fab/FabMenu.
- Rich: HtmlEditor (`UploadUrl` иначе base64, `ExecuteCommandAsync`, кастом-тулзы), MarkdownEditor, Markdown-рендер (`AllowHtml`), SignaturePad, SecurityCode (OTP), VirtualKeyboard.
- AI: AIPrompt/InlineAIPrompt (one-shot, `Context/Output/Suggestions/Commands` через IChatClient; памяти и tools нет), SmartPasteButton (буфер → модель по `[Description]`), AIChat, SpeechToTextButton (браузерное распознавание).

## 8. Layout и навигация

- `RadzenLayout` = Header+Sidebar+Body+Footer (`@bind-Expanded`, `Responsive=false`, overlay — своими CSS).
- `RadzenStack` (flex: `Orientation/Gap/JustifyContent/AlignItems`), Row/Column (12-колоночная сетка), Card (`Variant`), Splitter (`Pane: Size/Min/Max/Collapsible`, события `Collapse/Expand/Resize` отменяемые), Tabs (`RenderMode Server|Client` — Server рендерит только активный, `Overflow` Wrap/Scroll/Collapse, `AllowReorder`), Accordion (`Multiple`), Steps (`CanChange`+`args.PreventDefault()` — блок смены шага), Breadcrumb (только статические items), Panel, TileLayout (dashboard-виджеты), Carousel, ToC (по CSS-селектору), Skeleton.
- Меню: Menu (вложенность, `Flyout`), PanelMenu (вертикальный, `DisplayStyle`), ProfileMenu (аватар+dropdown, item может постить HTML-форму с antiforgery).
- CSS-утилиты `rz-*`: borders/breakpoints/display/overflow/ripple/shadows/sizing/spacing — целые страницы-справочники.

## 9. Сервисы UI: Dialog / Notification / Tooltip / ContextMenu

- **DialogService**: `OpenAsync<T>(title, Dictionary параметров, DialogOptions)` → `Task<dynamic>`; inline `OpenAsync(title, ds => @<RenderFragment>)`; `OpenSideAsync` (панели); `Confirm/Alert`. DialogOptions: `Width/Height/Resizable/Draggable/CloseDialogOnEsc/CloseDialogOnOverlayClick/CanClose` (блокирует X/overlay/ESC, но программный `Close()` всегда проходит). Механика (исходники): TaskCompletionSource + стек диалогов (не очередь — закрывается верхний); рендерит хост-компонент `RadzenDialog`, подписанный на `OnOpen`. Каскадный `Dialog` меняет свойства на лету; `ds.Refresh()`.
- **NotificationService**: `Notify(NotificationMessage)` — `Severity/Summary/Detail/Duration (мс)/Click/CloseOnClick/Payload/ShowProgress/SummaryContent/DetailContent`; рендерит `RadzenNotifications`; авто-скрытие на стороне компонента. Очереди/лимита нет.
- **TooltipService**: `Open(ElementReference, text|RenderFragment, TooltipOptions)` — `Position/Duration (null = до клика)/Delay`.
- **ContextMenuService**: `Open(MouseEventArgs, List<ContextMenuItem>, handler)`; закрывается вручную `Close()` (сервис сам не закрывается); `ContextMenuOptions.CloseOnScroll`. **Blazor-события не несут DOM-таргет** — резолв «по какому элементу кликнули» делай в рендер-замыкании (если рисуешь сам) или через JS.

## 10. Прочее

- GoogleMap (`ApiKey` обязательна, маркеры, `MapClick/MarkerClick`), Login (`Login/Register/ResetPassword`), SSRS Viewer (`UseProxy`), QRCode/Barcode, Gravatar, Image, Icon (Material Symbols по умолчанию), Timeline, DropZone, PickList.
- Document processing (Radzen.Documents): XLSX/CSV. PDF нет.
- Accessibility: WAI-ARIA, WCAG 2.2 заявлены; WCAG-AA темы бесплатны; финальная доступность — на разработчике.

## 11. Сводные ловушки (все — из доков или исходников, если не указано иное)

1. FilterCaseSensitivity.Default = case-sensitive Contains для in-memory (грид И дропдауны).
2. DateTime-фильтр «равно» сравнивает timestamp; день-диапазон — через `FilterTemplate` + `SetCustomFilterExpressionAsync` (боевая проверка DashSpec).
3. Нативные механизмы грида (sort/filter/group) — expression по членам TItem; обёртки без свойств ломаются (боевая).
4. Prerender + LoadData = пустой статический HTML; лечится OnInitializedAsync + PersistentComponentState.
5. `StateHasChanged` в `AppointmentRender` — infinite loop.
6. Upload `Stream=true` — только один файл; AllowedExtensions не проверяют MIME.
7. CheckBoxList по collection-свойству требует `Type=typeof(IEnumerable<...>)` + `FilterOperator.In`.
8. Nullable double в ValueProperty серии — circuit-убийца (боевая).
9. API-асимметрии: нет `DeleteRow`/`ExpandAll`/sync-экспорта у грида (`CollapseAll`/`CollapseRows` есть); Dock-компоненты нет; PDF-экспорта нет.
10. Grid-level `HeaderTemplate` НЕ заменяет `<thead>` — рендерится блоком над таблицей (`rz-custom-header`); column-level заменяет только span заголовка (sort-иконки и фильтр живут рядом — детали в DataGrid-файле).

## 12. Источники

- Доки: blazor.radzen.com (sitemap.xml; get-started, themes, theme-service, localization, accessibility, support; весь datagrid-раздел ~70 стр.; весь charts-раздел ~45 стр.; scheduler, gantt, все инпуты, layout, dialog/notification/tooltip/contextmenu, прочее; API-справка docs/api/Radzen.Blazor.RadzenDataGrid-1). Не открылся: /aichat.
- Исходники master: Radzen.Blazor/{RadzenComponent, FormComponent, PagedDataBoundComponent, DropDownBase, RadzenDataGrid(.razor/.cs), RadzenDataGridColumn, RadzenDataGridHeaderCell, QueryableExtension, PropertyAccess, FilterMode, FilterCaseSensitivity, CartesianSeries, RadzenChart, RadzenScheduler, DialogService, NotificationService}, wwwroot/Radzen.Blazor.js, themes/*.scss.
- Боевые проверки: открытый репозиторий DashSpec (ADR-0124, events table), 2026-11-08.
