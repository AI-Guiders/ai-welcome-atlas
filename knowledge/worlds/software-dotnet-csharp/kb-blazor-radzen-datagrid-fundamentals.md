---
type: Knowledge
title: 'Radzen Blazor DataGrid — глубокий проход'
description: 'Внутренняя механика грида по исходникам + боевые ловечки DashSpec. Radzen.Blazor v.x (master), проверено 2026-11-08. Полный стек — в kb-blazor-radzen-fundamentals.md.'
tags: [radzen, blazor, datagrid, software-dotnet-csharp, kb]
language: ru-RU
world: software
subworld: dotnet-csharp
---
# Radzen Blazor DataGrid — глубокий проход

Спутник [`kb-blazor-radzen-fundamentals.md`](kb-blazor-radzen-fundamentals.md) (весь стек). Здесь — только DataGrid: пайплайн данных, expression-механика, фильтрация, группировка, ловушки. Утверждения проверены исходниками (master: `RadzenDataGrid.razor.cs` 4954 строки, `QueryableExtension.cs` 2196, `RadzenDataGridColumn.razor.cs` 2100, `RadzenDataGridHeaderCell.razor`) и/или живым гридом открытого репозитория DashSpec (2026-11-08).

## 1. Пайплайн данных (View)

`RadzenDataGrid<TItem> : PagedDataBoundComponent<TItem>` (generic, `[DynamicallyAccessedMembers]` + `[CascadingTypeParameter]` — trim/AOT-дружелюбен).

`View` (public, «Data with sorting, filtering and paging applied»):
1. `GetOrderBy()` — строка сортировки из коллекции `Sorts` (SortDescriptor).
2. **LoadData.HasDelegate** → грид сам ничего не делает: данные + `Count` даёт обработчик (`LoadDataArgs { Skip, Top, OrderBy, Filter }`; `Filter`-строка через `Query.ToFilterString()/ToODataFilterString()`).
3. In-memory: `base.View.Where<TItem>(allColumns)` (собственный expression-фильтр, см. §2) → `OrderBy` через **System.Linq.Dynamic.Core** (NuGet, НЕ внутренний форк) либо `OrderByComparers` при кастомном `SortComparer`. Ветка `TItem == object` — каст к типу первого элемента.
4. `Count` in-memory = `view.Count()` (с коррекцией страницы); при virtualization — ItemsProvider.
5. `PagedView` = View.Skip/Take (если paging и НЕ LoadData); `GroupedPagedView` = постраничный view → `GroupByMany(Groups.Select(g => g.Property))` — рекурсивная группировка, `GroupResult.Subgroups` = «Then by».
6. `QueryOnlyVisibleColumns` — проекция только видимых свойств.

Иерархия: `GetSelfRefView` — дети фильтруются отдельно, вставляются после родителя (`DataGridChildData`, self-referencing по ParentProperty).

## 2. Expression-механика

- **`PropertyAccess`** (не «PropertyAccessor»): кэш `ConcurrentDictionary<(ItemType, ValueType, PropertyName, TargetType), Delegate>`; `Getter<TItem,TValue>` строит и компилирует expression (вложенные пути — с null-propagation); `DateGetter` — обёртки для `DateTime?/DateOnly?/DateTimeOffset?`. Колонка кэширует свой `propertyValueGetter`, fallback — рефлексия.
- **`QueryableExtension.Where<T>`**: `FilterDescriptor`'ы → `GetExpression` → комбинирование `AndAlso/OrElse` по `LogicalFilterOperator` → `Where(lambda)`. Кейс: `inMemory = source is EnumerableQuery` → CaseInsensitive даёт `OrdinalIgnoreCase` (in-memory) или `ToLower` (EF).
- Следствие: **сортировка/фильтрация/группировка работают по членам CLR TItem**. Обёртка `record Row(DashValue[] Cells)` без свойств-колонок валит circuit на группировке («'x' is not a member of type Row») — нужен POCO с честными свойствами (DashSpec: POCO генерит `dsc build types`).

## 3. Фильтрация — точная карта

Enum `FilterMode`: **Simple** (инлайн-инпуты в строке фильтров), **SimpleWithMenu** (инлайн + меню оператора), **Advanced** (попап с оператором и двумя значениями — **дефолт**), **CheckBoxList** (попап-список уникальных значений).

**Где живёт UI фильтра** (по `RadzenDataGridHeaderCell.razor` + `RadzenDataGrid.razor`):
- Advanced/CheckBoxList: иконка (`rzi rz-grid-filter-icon`) + `RadzenPopup` с формой — внутри `<th>`, после кликабельного sort-div.
- Simple/SimpleWithMenu: **отдельная строка фильтров** под заголовками (`FilterRowActive` → `<tr>` с `rz-cell-filter`; операторское меню SimpleWithMenu — `RadzenDataGridFilterMenu` в этой строке; для дат — оверлей `rz-grid-date-filter`).

**HeaderTemplate (исправление ранней гипотезы):** grid-level `HeaderTemplate` вообще не трогает `<thead>` — рендерится блоком `rz-custom-header` над таблицей; column-level `HeaderTemplate` заменяет только span заголовка внутри sort-div, sort-иконки и resizer — соседи. Ранняя версия этой заметки («HeaderTemplate хоронит иконку фильтра») была неверна в механизме: эмпирика «0 vs 8 иконок» (DashSpec, 12.1.11) ловила фильтр-строку SimpleWithMenu, чьё поведение при кастомном хедере меняется косвенно. Практический вывод прежний: хедер лучше оставлять нативным, кастомные обработчики вешать на cell `Template` (индекс колонки известен в рендере).

Параметры: `FilterDelay` (500 мс), `FilterAsYouType` (true), `LogicalFilterOperator`, `AutoApplyCheckBoxListFilter`, `FilterDateFormat`, `FilterCaseSensitivity` (Default=case-sensitive для in-memory; CaseInsensitive), `FilterProperty` (фильтровать по другому свойству), `FilterOperator` (в т.ч. `In` для коллекций). Программно: `ApplyFilter(column, closePopup)`, `ClearFilter(column, closePopup, shouldReload)`, `column.SetCustomFilterExpressionAsync(string|null)`.

**Динамический LINQ** (для Custom-фильтров): литералы `DateTime(y,m,d)` поддержаны; день-диапазон = `it.Prop >= DateTime(y,m,d) && it.Prop < DateTime(y,m,d+1)` — единственный способ дать «выбранный день» для datetime-колонки с временем («равно» сравнивает timestamp на полуночи и не совпадает никогда; боевая проверка).

CheckBoxList по collection-свойству: `Type="typeof(IEnumerable<T>)"` + `FilterOperator="FilterOperator.In"`.

## 4. Сортировка, группировка, страницы, колонки

- Сортировка: `AllowSorting`, per-column `Sortable`; мультисортировка `AllowMultiColumnSorting` + `ShowMultiColumnSortingIndex`; начальная — колонка `SortOrder`; программно `OrderBy/OrderByDescending(string)` + `Reload()` (проверено: `OrderBy` без `Reload` ничего не делает) или `args.Grid.Sorts.Add(new SortDescriptor { Property, SortOrder })` в `Render(FirstRender)`. `GotoFirstPageOnSort`. `SortComparer` — свой IComparer.
- Группировка: `AllowGrouping` (drag в панель), `Groupable` per-column, `HideGroupedColumn`. Программно: **`grid.Groups`** — `ObservableCollection<GroupDescriptor>`; add/remove + `Reload()`; стек = вложенные группы (проверено: Group → Then by → Ungroup работают и из кода, и жестом — одна и та же машина). Событие `Group`; `GroupRowRender/GroupRowExpand/GroupRowCollapse`; `@bind-AllGroupsExpanded`, `ToggleAllGroupsExpand()`, `ExpandGroupItem`. Шаблоны: `GroupHeaderTemplate` (`context.GroupDescriptor.GetTitle()`, `context.Data.Key`, `context.Data.Count`), `GroupHeaderToggleTemplate`, `GroupFooterTemplate`, `GroupFootersAlwaysVisible`. С LoadData группировка в доках не описана (осторожно).
- Пейджинг: `AllowPaging/PageSize/PagerPosition/PagerHorizontalAlign/ShowPagingSummary`; pager API `FirstPage/PrevPage/NextPage/LastPage/GoToPage`.
- Virtualization: `AllowVirtualization` (+ с LoadData: `/datagrid-virtualization-loaddata`); public `Virtualize` (ref на Blazor Virtualize), `RefreshDataAsync()`.
- Колонки: `AllowColumnResize/Reorder`, `AllowColumnPicking` (+`ColumnsPickerAllowFiltering`, событие `PickedColumnsChanged`), `Frozen` + `FrozenPosition Left/Right` (CSS-переменные `--rz-grid-frozen-left-N` из JS), `OrderIndex`, `Visible/Pickable`. События `ColumnReordering` (`args.Cancel=true`), `ColumnReordered`.

## 5. Editing, selection, иерархия, экспорт, состояние

- Editing: `EditMode Single/Multiple`, отдельный incell-режим; `EditRow/EditRows/InsertRow/InsertAfterRow/UpdateRow/CancelEditRow(s)/IsValid`; `RowUpdate/RowCreate`; `EditTemplate` колонки (+ Radzen-валидаторы с `Popup="true"`). `DeleteRow` нет — удалять из данных + `Reload()`.
- Selection: `SelectionMode Single/Multiple`, cell-selection; выделение — `@bind-Value` на `IList<T>`; `SelectRow(item, raiseEvent)`.
- Иерархия: вложенные гриды в `Template` (`ExpandMode Single/Multiple`, `RowRender(args.Expandable)`, `ExpandRow/ExpandRows/CollapseRows/CollapseAll` — но не `ExpandAll`/`CollapseRow`, их нет). `DetailTemplate` в доках нет.
- Экспорт: `ExportToExcelAsync/ExportToCsvAsync` (браузер, chunked для LoadData, `DataGridExportScope.CurrentPage|All`); колонки с Template — `ExportValue`+`FormatString`. `ToWorkbookAsync` → Workbook (Radzen.Documents).
- Состояние: `Settings` + `SaveSettings()/ReloadSettings()` (page/sort/filter персистенция).

## 6. Прочие API (справка)

`Reload()`, `RefreshDataAsync()` (чистит кэш + Virtualize), `Reset(resetColumnState, resetRowState)`, `ResetLoadData()`, `View/PagedView/GroupedPagedView`, `ColumnsCollection`, `Query`, `IsLoading` (+`await Task.Yield()` чтобы лоадер показался), `Render`-событие (`args.FirstRender`).

## 7. Razor-мелочи (боевые)

- `@if` вокруг `FilterTemplate` внутри колонки — RZ9996 (unrecognized child content); ветвится вся декларация колонки.
- RenderFragment с разметкой — только в `.razor` `@code`, не в `.razor.cs`.
- Два `style`-атрибута — RZ10009; CSS-переменную в общий `Style="...;--x:1"`.
- Клик сортировки работает по `.rz-column-title`, не по самому `th`.
- У Blazor-событий нет DOM-таргета: резолв «какая колонка» — из рендер-замыкания (cell Template) или JS-мост.

## 8. Перф

`Data` как `IQueryable` (транслируется в SQL); умеренный `PageSize`; virtualization; «десятки тысяч строк» — заявленная планка; `AllowAlternatingRows=false`, фиксированный `ColumnWidth`; `IsLoading` + `Task.Yield()`.

## Мета

- Проверено: DashSpec `typed-grid` (production events table), 2026-11-08 — фильтр без регистра, день-диапазон через Custom-выражение, группировка/Then by/Ungroup из меню на ячейках (grid.Groups), нативная сортировка по клику заголовка.
- Ссылки: [datagrid](https://blazor.radzen.com/datagrid), [filter-template](https://blazor.radzen.com/datagrid-filter-template), [grouping-api](https://blazor.radzen.com/datagrid-grouping-api), [loaddata](https://blazor.radzen.com/datagrid-loaddata), [performance](https://blazor.radzen.com/datagrid-performance), [API](https://blazor.radzen.com/docs/api/Radzen.Blazor.RadzenDataGrid-1), исходники master.
