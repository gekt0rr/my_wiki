# proizvodstvo_list.dart
Данный код представляет собой виджет таблицы для отображения данных. Рассмотрим его подробно:

1. Давайте рассмотрим основные моменты в данном коде.

1. Имеется класс `ProizvodstvoList`, который является наследником класса `StatefulWidget`. Он используется для создания виджета, отображающего список данных.
    
2. В классе `ProizvodstvoList` определен внутренний класс `_ProizvodstvoListState`, который является наследником класса `State`. Этот класс содержит логику и состояние виджета.
    
3. Внутри класса `_ProizvodstvoListState` определены несколько переменных, таких как `baseListModelData`, `isLoaded`, `_columnVisibility`, `_sortColumnIndex`, `_sortAscending` и контроллеры для поиска по паттерну в таблице.
    
4. В методе `initState()` происходит инициализация контроллеров для поиска по паттерну в таблице.
    
5. Метод `getBaseListModel()` используется для получения данных `baseListModelData` с помощью асинхронного вызова `BaseListGetApi().getBaseListModel()`. После получения данных устанавливается флаг `isLoaded` в значение `true`.
    
6. Метод `_sort` используется для сортировки данных в `baseListModelData` по заданному столбцу и направлению сортировки. Он принимает функцию `getField`, которая получает значение поля из экземпляра `BaseListModel`. Затем метод `sort` сортирует список `baseListModelData` с использованием полученных значений полей и заданного направления сортировки. После сортировки устанавливаются значения `_sortColumnIndex` и `_sortAscending`, чтобы отображение таблицы отразило выбранный столбец и направление сортировки.
    
7. Метод `_filteredBaseListModelData` используется для фильтрации данных в `baseListModelData` на основе значений, введенных в текстовые поля поиска. Он создает переменную `filteredData`, которая инициализируется значением `baseListModelData`. Затем происходит проверка каждого текстового поля поиска. Если оно не пустое, то список `filteredData` фильтруется с использованием метода `where`, который проверяет, содержатся ли искомые значения в соответствующих полях элементов списка. Результат фильтрации сохраняется в `filteredData`. В конце метод возвращает отфильтрованный список.
    
8. Метод `build` используется для построения и отображения пользовательского интерфейса. Он возвращает виджет `Scaffold`, который содержит столбец виджетов. Внутри столбца содержатся различные виджеты, такие как `ColumnVisibilityControls`, `TextField`, `CircularProgressIndicator` и `DataTable`.
    
9. `ColumnVisibilityControls` - это виджет, отображающий элементы управления для изменения видимости столбцов в таблице. Когда пользователь изменяет видимость столбцов, вызывается функция `onVisibilityChanged`, которая обновляет состояние `_columnVisibility`.
    
10. `TextField` - это виджеты текстовых полей для ввода поисковых запросов. Когда пользователь вводит текст в поле, вызывается функция `onChanged`, которая вызывает `setState`, чтобы обновить состояние виджета и выполнить фильтрацию данных.
    
11. `CircularProgressIndicator` - это виджет, отображающий круговой индикатор загрузки. Он отображается, когда данные еще не загружены.
    
12. `DataTable` - это виджет, отображающий таблицу данных. Он принимает список столбцов и список строк данных. Столбцы определяются через `DataColumn`, а строки данных -через `DataRow`. В данном коде столбцы и строки данных берутся из `baseListModelData`. Таблица отображается с использованием текущей видимости столбцов и направления сортировки.
    

Это основные моменты в данном коде. Он создает виджет, который отображает список данных в виде таблицы, позволяет сортировать и фильтровать данные, а также изменять видимость столбцов.
    
Метод `build()` строит и возвращает итоговый виджет таблицы. Внутри него определены следующие виджеты:    

- `Scaffold` - оболочка для построения страницы.
- `ColumnVisibilityControls` - виджет для управления видимостью столбцов таблицы.
- `Row` - строка с текстовыми полями для поиска по паттерну в таблице.
- Условные выражения `if (_columnVisibility[index])` используются для определения видимости каждого текстового поля поиска в зависимости от значения `_columnVisibility`.
- `Expanded` - виджет, который занимает доступное пространство в родительском виджете.
- `SingleChildScrollView` - виджет, который позволяет прокручивать содержимое горизонтально.
- `DataTable` - виджет таблицы, который принимает список столбцов и список строк данных.
- `DataColumn` - виджет столбца таблицы, который содержит метку и функцию сортировки.
- `DataRow` - виджет строки таблицы, который содержит ячейки данных.
- `Text` - виджет текста для отображения данных в ячейках таблицы.
- `Center`, `CircularProgressIndicator`, `Text` - виджеты, которые отображаются в случае, если данные еще не загружены или если данные отсутствуют.

Это общий обзор кода виджета таблицы. Он отображает данные из списка `BaseListModel` в таблице и предоставляет возможность сортировки и фильтрации данных. Также есть возможность управления видимостью столбцов и поиска данных по паттерну.

## Разбор частей кода
Разберем поэтапно все составляющие таблицы.

```dart
  void _sort<T>(Comparable<T> Function(BaseListModel d) getField,
      int columnIndex, bool ascending) {
    baseListModelData!.sort((a, b) {
      final aValue = getField(a);
      final bValue = getField(b);
      return ascending
          ? Comparable.compare(aValue, bValue)
          : Comparable.compare(bValue, aValue);
    });
    setState(() {
      _sortColumnIndex = columnIndex;
      _sortAscending = ascending;
    });
  }

  List<BaseListModel>? _filteredBaseListModelData() {
    var filteredData = baseListModelData;

    if (_dateSearchController.text.isNotEmpty) {
      filteredData = filteredData!
          .where((element) =>
              element.date?.toString().contains(_dateSearchController.text) ??
              false)
          .toList();
    }

    if (_zakazSearchController.text.isNotEmpty) {
      filteredData = filteredData!
          .where((element) =>
              element.zakazN?.contains(_zakazSearchController.text) ?? false)
          .toList();
    }

    if (_zakazchikSearchController.text.isNotEmpty) {
      filteredData = filteredData!
          .where((element) =>
              element.zakazchik?.contains(_zakazchikSearchController.text) ??
              false)
          .toList();
    }

    if (_kolichestvoSearchController.text.isNotEmpty) {
      filteredData = filteredData!
          .where((element) =>
              element.kolichestvo
                  ?.contains(_kolichestvoSearchController.text) ??
              false)
          .toList();
    }

    if (_naznachenieTenSearchController.text.isNotEmpty) {
      filteredData = filteredData!
          .where((element) =>
              element.naznachenieTen
                  ?.contains(_naznachenieTenSearchController.text) ??
              false)
          .toList();
    }

    return filteredData;
  }
```
Представленный код содержит функции для сортировки и фильтрации данных в рамках таблицы `baseListModelData`. Давайте разберем, как эти функции работают.

Функция `_sort` используется для сортировки данных в таблице по заданному столбцу и направлению сортировки. Она принимает три параметра: `getField`, `columnIndex` и `ascending`.

- `getField` - это функция, которая извлекает значение поля из экземпляра `BaseListModel`. Она применяется к каждому элементу списка данных для получения значения, по которому будет производиться сортировка.
    
- `columnIndex` - это индекс столбца, по которому требуется выполнить сортировку.
    
- `ascending` - это флаг, указывающий направление сортировки. Если значение `ascending` равно `true`, то сортировка будет производиться по возрастанию. Если `ascending` равно `false`, то сортировка будет производиться по убыванию.
    

Внутри функции `_sort` происходит сортировка списка `baseListModelData`. Метод `sort` вызывается на списке и принимает функцию сравнения элементов. В этой функции сравнения значения полей извлекаются с помощью `getField` для элементов `a` и `b`. Затем значения сравниваются с использованием `Comparable.compare` в соответствии с заданным направлением сортировки (`ascending`). Результат сортировки сохраняется в `baseListModelData`.

После сортировки вызывается метод `setState`, который обновляет состояние виджета. В частности, обновляются значения `_sortColumnIndex` и `_sortAscending`, чтобы отображение таблицы отразило выбранный столбец и направление сортировки.

Функция `_filteredBaseListModelData` используется для применения фильтрации к данным в таблице на основе значений, введенных в текстовые поля поиска. Она возвращает отфильтрованный список данных `filteredData`.

Внутри функции `_filteredBaseListModelData` создается переменная `filteredData`, которая инициализируется значением `baseListModelData`. Затем происходит проверка каждого текстового поля поиска. Если поле не пустое, то список `filteredData` фильтруется с помощью метода `where`, который проверяет, содержатся ли искомые значения в соответствующих полях элементов списка. Результат фильтрации сохраняется в `filteredData`.

В конце функция возвращает отфильтрованный список `filteredData`, который будет использоваться для отображения таблицы после применения фильтрации.

Таким образом, эти функции позволяют сортировать данные по выбранному столбцу и направлению сортировки, а также фильтровать данные на основе значений, введенных в текстовые поля поиска.

```dart
 @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: ProizvodstvoButton(),
      body: Column(
        children: [
          ColumnVisibilityControls(
            initialVisibility: _columnVisibility,
            onVisibilityChanged: (visibility) {
              setState(() {
                _columnVisibility = visibility;
              });
            },
          ),
          Row(
            children: [
              if (_columnVisibility[0])
                Expanded(
                  child: TextField(
                    controller: _dateSearchController,
                    decoration: InputDecoration(
                      hintText: 'Поиск по дате',
                    ),
                    onChanged: (text) {
                      setState(() {});
                    },
                  ),
                ),
              if (_columnVisibility[1])
                Expanded(
                  child: TextField(
                    controller: _zakazSearchController,
                    decoration: InputDecoration(
                      hintText: 'Поиск по заказу',
                    ),
                    onChanged: (text) {
                      setState(() {});
                    },
                  ),
                ),
              if (_columnVisibility[2])
                Expanded(
                  child: TextField(
                    controller: _zakazchikSearchController,
                    decoration: InputDecoration(
                      hintText: 'Поиск по заказчику',
                    ),
                    onChanged: (text) {
                      setState(() {});
                    },
                  ),
                ),
              if (_columnVisibility[3])
                Expanded(
                  child: TextField(
                    controller: _kolichestvoSearchController,
                    decoration: InputDecoration(
                      hintText: 'Поиск по количеству',
                    ),
                    onChanged: (text) {
                      setState(() {});
                    },
                  ),
                ),
              if (_columnVisibility[4])
                Expanded(
                  child: TextField(
                    controller: _naznachenieTenSearchController,
                    decoration: InputDecoration(
                      hintText: 'Поиск по назначению',
                    ),
                    onChanged: (text) {
                      setState(() {});
                    },
                  ),
                ),
            ],
          ),
          if (isLoaded &&
              baseListModelData != null &&
              baseListModelData!.isNotEmpty)
            Expanded(
              child: SingleChildScrollView(
                scrollDirection: Axis.horizontal,
                child: DataTable(
                  sortColumnIndex: _sortColumnIndex,
                  sortAscending: _sortAscending,
                  columns: [
                    if (_columnVisibility[0])
                      DataColumn(
                        label: Text('Дата'),
                        onSort: (columnIndex, ascending) {
                          _sort((d) => d.date!, columnIndex, ascending);
                        },
                      ),
                    if (_columnVisibility[1])
                      DataColumn(
                        label: Text('Заказ'),
                        onSort: (columnIndex, ascending) {
                          _sort((d) => d.zakazN ?? '', columnIndex, ascending);
                        },
                      ),
                    if (_columnVisibility[2])
                      DataColumn(
                        label: Text('Заказчик'),
                        onSort: (columnIndex, ascending) {
                          _sort(
                              (d) => d.zakazchik ?? '', columnIndex, ascending);
                        },
                      ),
                    if (_columnVisibility[3])
                      DataColumn(
                        label: Text('Количество'),
                        onSort: (columnIndex, ascending) {
                          _sort((d) => int.parse(d.kolichestvo ?? ''),
                              columnIndex, ascending);
                        },
                      ),
                    if (_columnVisibility[4])
                      DataColumn(
                        label: Text('Назначение'),
                        onSort: (columnIndex, ascending) {
                          _sort((d) => d.naznachenieTen ?? '', columnIndex,
                              ascending);
                        },
                      ),
                  ],
                  rows: _filteredBaseListModelData()!.map((data) {
                    return DataRow(
                      cells: [
                        if (_columnVisibility[0])
                          DataCell(
                              Text(data.date!.toString().substring(0, 10))),
                        if (_columnVisibility[1])
                          DataCell(Text(data.zakazN ?? '')),
                        if (_columnVisibility[2])
                          DataCell(Text(data.zakazchik ?? '')),
                        if (_columnVisibility[3])
                          DataCell(Text(data.kolichestvo ?? '')),
                        if (_columnVisibility[4])
                          DataCell(Text(data.naznachenieTen ?? '')),
                      ],
                    );
                  }).toList(),
                ),
              ),
            ),
          if (!isLoaded)
            Expanded(
              child: Center(
                child: CircularProgressIndicator(),
              ),
            ),
          if (isLoaded &&
              baseListModelData != null &&
              baseListModelData!.isEmpty)
            Expanded(
              child: Center(
                child: Text('Данные отсутствуют'),
              ),
            ),
        ],
      ),
    );
  }
}
```
Представленный код является методом `build` виджета и определяет структуру и внешний вид таблицы с возможностью сортировки и фильтрации данных.

Внутри метода `build` используется виджет `Scaffold`, который представляет основной каркас экрана. Внутри `Scaffold` определены следующие элементы:

1. `floatingActionButton`: Это плавающая кнопка `ProizvodstvoButton`, которая располагается на экране.
    
2. `body`: Основное содержимое экрана, разделенное на несколько частей с помощью виджета `Column`.
    
    a. `ColumnVisibilityControls`: Это виджет, который отображает переключатели для управления видимостью столбцов таблицы. Изначально видимость столбцов задается значением `_columnVisibility`, которое передается в параметр `initialVisibility`. При изменении видимости столбцов вызывается метод `onVisibilityChanged`, который обновляет состояние `_columnVisibility`.
    
    b. `Row`: В этом виджете располагаются текстовые поля поиска для каждого столбца таблицы. Количество и видимость полей зависят от значения `_columnVisibility`. Если `_columnVisibility[i]` равно `true`, то соответствующее текстовое поле будет отображаться. Каждое текстовое поле использует соответствующий контроллер (`_dateSearchController`, `_zakazSearchController`, и т.д.) для получения и обновления значения текста при изменении.
    
    c. `if (isLoaded && baseListModelData != null && baseListModelData!.isNotEmpty)`: Условие проверяет, что данные загружены (`isLoaded`), и `baseListModelData` не является пустым списком. Если условие истинно, то отображается таблица с данными.
    
    - `Expanded`: Этот виджет занимает доступное пространство внутри родительскего виджета. В данном случае, он обертывает виджет `SingleChildScrollView`, который позволяет прокручивать таблицу горизонтально.
        
        - `DataTable`: Этот виджет представляет таблицу данных. В параметрах `sortColumnIndex` и `sortAscending` указываются индекс текущего сортируемого столбца и направление сортировки, которые определены в предыдущем коде. В параметре `columns` определены столбцы таблицы.
            
            - `DataColumn`: Этот виджет представляет столбец таблицы. Каждый столбец имеет метку (`label`) и функцию обработчик (`onSort`), которая вызывается при нажатии на заголовок столбца для выполнения сортировки. Метки столбцов берутся из соответствующих полей данных (`date`, `zakazN`, и т.д.), а функции обработчики сортировки используют функцию `_sort` из предыдущего кода.
        - `rows`: Этот параметр определяет строки таблицы и использует метод `_filteredBaseListModelData` для получения отфильтрованных данных. Для каждого элемента данных создается `DataRow`, а для каждого столбца создается `DataCell`, содержащая соответствующее значение поля данных.
            
    
    d. `if (!isLoaded)`: Условие проверяет, что данные не загружены (`isLoaded` равно `false`). Если условие истинно, отображается индикатор загрузки (`CircularProgressIndicator`).
    
    e. `if (isLoaded && baseListModelData != null && baseListModelData!.isEmpty)`: Условие проверяет, что данные загружены (`isLoaded`), и `baseListModelData` является пустым списком. Если условие истинно, отображается текст "Данные отсутствуют".

```dart
class _ColumnVisibilityControlsState extends State<ColumnVisibilityControls> {
  late List<bool> _visibility;

  @override
  void initState() {
    super.initState();
    _visibility = widget.initialVisibility;
  }

  @override
  Widget build(BuildContext context) {
    return Row(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Column(
          children: [
            Text('Дата'),
            Switch(
              value: _visibility[0],
              onChanged: (value) {
                setState(() {
                  _visibility[0] = value;
                  widget.onVisibilityChanged(_visibility);
                });
              },
            ),
          ],
        ),
        Column(
          children: [
            Text('Заказ'),
            Switch(
              value: _visibility[1],
              onChanged: (value) {
                setState(() {
                  _visibility[1] = value;
                  widget.onVisibilityChanged(_visibility);
                });
              },
            ),
          ],
        ),
        Column(
          children: [
            Text('Заказчик'),
            Switch(
              value: _visibility[2],
              onChanged: (value) {
                setState(() {
                  _visibility[2] = value;
                  widget.onVisibilityChanged(_visibility);
                });
              },
            ),
          ],
        ),
        Column(
          children: [
            Text('Количество'),
            Switch(
              value: _visibility[3],
              onChanged: (value) {
                setState(() {
                  _visibility[3] = value;
                  widget.onVisibilityChanged(_visibility);
                });
              },
            ),
          ],
        ),
        Column(
          children: [
            Text('Назначение ТЭНа'),
            Switch(
              value: _visibility[4],
              onChanged: (value) {
                setState(() {
                  _visibility[4] = value;
                  widget.onVisibilityChanged(_visibility);
                });
              },
            ),
          ],
        ),
      ],
    );
  }
}
```
Представленный код представляет класс `_ColumnVisibilityControlsState`, который является состоянием для виджета `ColumnVisibilityControls`. Этот класс содержит список булевых значений `_visibility`, который отражает текущую видимость столбцов таблицы.

В классе `_ColumnVisibilityControlsState` определены следующие методы:

- `initState()`: Этот метод вызывается при создании состояния и инициализирует список `_visibility` значением `widget.initialVisibility`. Значение `widget.initialVisibility` передается в состояние через конструктор виджета `ColumnVisibilityControls`.
    
- `build(BuildContext context)`: Этот метод создает и возвращает виджет `Row`, который содержит столбцы с метками и переключателями видимости для каждого столбца таблицы. Количество и видимость столбцов определяются списком `_visibility`.
    
    - Каждый столбец представлен виджетом `Column`, содержащим текстовую метку и переключатель `Switch`. Метки столбцов берутся из предопределенных строк (`'Дата'`, `'Заказ'`, и т.д.), а значения видимости и обработчики изменений берутся из соответствующих элементов списка `_visibility`. При изменении значения переключателя вызывается метод `onChanged`, который обновляет значение `_visibility` и вызывает функцию обратного вызова `widget.onVisibilityChanged`, передавая ей обновленный список `_visibility`.