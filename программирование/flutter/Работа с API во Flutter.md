# Работа с API во Flutter

## Парсинг ответа API, создание модели

Ответ обычно приходит в JSON, который нужно распарсить. Есть [отличный сервис](https://app.quicktype.io/) для этого. Нужно просто вставить в него ответ api и указать тип запроса (post, get ...) - в итоге получим готовую **модель** которую можем вставить в код.
```dart
// Класс-модель пользователя
class Zagotovka2Model {
  // тут должны быть поля, которые будут приходить с сервера
  int diametrZagotovki;
  String material;
  double tolshinaStenki;
  int idZagotovka;

  // конструктор класса
  Zagotovka2Model(
      {required this.diametrZagotovki,
      required this.material,
      required this.tolshinaStenki,
      required this.idZagotovka});

  // фабричный метод, который создает экземпляр класса из Map
  factory Zagotovka2Model.fromJson(Map<String, dynamic> json) {
    return Zagotovka2Model(
      diametrZagotovki: json['diametr_zagotovki'],
      material: json['material'],
      tolshinaStenki: json['tolshina_stenki'],
      idZagotovka: json['id_zagotovka'],
    );
  }

  // метод, который создает Map из экземпляра класса
  Map<String, dynamic> toJson() => {
        'diametr_zagotovki': diametrZagotovki,
        'material': material,
        'tolshina_stenki': tolshinaStenki,
        'id_zagotovka': idZagotovka
      };
}
```
- Используется `jsonDecode` для преобразования JSON в Map/List
- Данные записываются в модели (классы)

## Выполнение запроса
Создается отдельный класс (например `ApiService`). В нем описываются методы для разных запросов к API
Вот пример использования http.get с конкретным URL:
### Способ 1
```dart
import 'dart:convert';
import 'package:http/http.dart' as http;
import '../models/zagotovka_2_model.dart';

class Zagotovka2Api {

  Future<List<Zagotovka2Model>> getZagotovka2Model() async {
    final responseZagotovka2Model =
        await http.get(Uri.parse('http://127.0.0.1:3000/zagotovka_2'));

    if (responseZagotovka2Model.statusCode == 200) {
      final List<Zagotovka2Model> zagotovka2ApiJson =
          (json.decode(responseZagotovka2Model.body) as List)
              .map((json) => Zagotovka2Model.fromJson(json))
              .toList();
      return zagotovka2ApiJson;
    } else {
      throw const FormatException(
          'Ошибка в получении данных из таблицы zagotovka_2');
    }
  }
}
```
Здесь мы используем URL вашего API для получения данных о заготовках.

### Способ 2
этот способ немного лучше так как мы выносим все составляющие запроса в отдельные переменные. 
```dart
import 'dart:convert';
import 'package:http/http.dart' as http;
import '../models/zagotovka_2_model.dart';

class Zagotovka2Api {
  Future<List<Zagotovka2Model>?> getZagotovka2Model() async {
    var clientZagotovka2 = http.Client();
    var urlZagotovka2 = Uri.parse('http://127.0.0.1:3000/zagotovka_2');
    var responseZagotovka2Model = await clientZagotovka2.get(urlZagotovka2);

    if (responseZagotovka2Model.statusCode == 200) {
      final List<Zagotovka2Model> zagotovka2ApiJson =
          (json.decode(responseZagotovka2Model.body) as List)
              .map((json) => Zagotovka2Model.fromJson(json))
              .toList();
      return zagotovka2ApiJson;
    } else {
      throw const FormatException(
          'Ошибка в получении данных из таблицы zagotovka_2');
    }
  }
}
```


## Отображение данных в коде
После получения данных и их обработке мы можем вывести их в коде в виде различных виджетов.
### Вывод в виде списка ListView
```dart
import 'package:flutter/material.dart';
import 'package:ten/main_project/repository/models/zagotovka_2_model.dart';
import 'package:ten/main_project/repository/services/zagotovka_2_api.dart';

class ProizvodstvoForm extends StatefulWidget {
  ProizvodstvoForm({Key? key}) : super(key: key);

  @override
  _ProizvodstvoFormState createState() => _ProizvodstvoFormState();
}

class _ProizvodstvoFormState extends State<ProizvodstvoForm> {
  List<Zagotovka2Model>? zagotovka2ModelData;
  var isLoaded = false;

  @override
  void initState() {
    super.initState();

    // получаем данные из таблицы zagotovka_2 через API
    getZagotovka2Model();
  }

  getZagotovka2Model() async {
    zagotovka2ModelData = await Zagotovka2Api().getZagotovka2Model();
    if (zagotovka2ModelData != null) {
      setState(() {
        isLoaded = true;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        body: Visibility(
      visible: isLoaded,
      replacement: const Center(
        child: CircularProgressIndicator(),
      ),
      child: Container(
        padding: EdgeInsets.all(10),
        child: ListView.builder(
          itemCount: zagotovka2ModelData?.length,
          itemBuilder: (context, index) {
            return Container(
              margin: EdgeInsets.all(10),
              decoration: BoxDecoration(),
              child: Row(
                mainAxisAlignment: MainAxisAlignment.spaceAround,
                children: [
                  Container(
                    width: 50,
                    height: 50,
                    child: Center(
                      child: Text(
                        'ID:${zagotovka2ModelData?[index].idZagotovka}',
                        style: TextStyle(fontSize: 16, color: Colors.black),
                      ),
                    ),
                    decoration: BoxDecoration(
                        borderRadius: BorderRadius.circular(10),
                        color: Colors.orange[300]),
                  ),
                  SizedBox(width: 10),
                  SizedBox(width: 10),
                  Text(
                    'Материал:${zagotovka2ModelData?[index].material}',
                    style: TextStyle(
                      fontSize: 16,
                    ),
                  ),
                  SizedBox(width: 10),
                  Text(
                    'Диаметр:${zagotovka2ModelData?[index].diametrZagotovki}',
                    style: TextStyle(
                      fontSize: 16,
                    ),
                  ),
                  SizedBox(width: 10),
                  Text(
                    'Толщина:${zagotovka2ModelData?[index].tolshinaStenki}',
                    style: TextStyle(
                      fontSize: 16,
                    ),
                  )
                ],
              ),
            );
          },
        ),
      ),
    ));
  }
}

```
### Автозаполнение на основе TypeAhead
для начала приведу пример обычной формы автозаполненения
```dart
TypeAheadFormField(
                  textFieldConfiguration: TextFieldConfiguration(
                    decoration: InputDecoration(labelText: 'Диаметр'),
                    controller: _diameterController,
                  ),
                  suggestionsCallback: (pattern) {
                    return ['10', '20', '30']
                        .where((item) => item.contains(pattern));
                  },
                  itemBuilder: (context, suggestion) {
                    return ListTile(
                      title: Text(suggestion),
                    );
                  },
                  onSuggestionSelected: (suggestion) {
                    _diameterController.text = suggestion;
                  },
                ),
```
нам будут предлагаться значения в suggestionsCallback (10, 20 и 30). Для отображения значения из api необходимо:
1. Создать экземпляр класса вашего API-сервиса в вашей форме:
```dart
final zagotovka2Api = Zagotovka2Api();
```

2. В методе `suggestionsCallback` для каждого поля, вместо возврата жестко заданных значений, вызвать метод вашего API-сервиса для получения списка значений:
```dart
suggestionsCallback: (pattern) async {
    final zagotovka2ModelList =
        await zagotovka2Api.getZagotovka2Model();
    return zagotovka2ModelList!
        .where((model) => model.material.contains(pattern))
        .map((model) => model.material)
        .toList();
    },
},
```

Здесь мы получаем список объектов `Zagotovka2Model` из вашего API-сервиса, фильтруем его по заданному шаблону `pattern` и возвращаем только значения поля `material` в виде списка строк.

> [!NOTE]
> В случае когда мы работаем с api иногда требуется дополнительно обработать значения, оставив только **уникальные**. 

Чтобы отображать только уникальные значения в выпадающем списке, вам нужно изменить метод `suggestionsCallback` следующим образом:
```dart
suggestionsCallback: (pattern) async {
  final zagotovka2ModelList = await zagotovka2Api.getZagotovka2Model();
  final uniqueMaterials = zagotovka2ModelList!
      .where((model) => model.material.contains(pattern))
      .map((model) => model.material)
      .toSet()
      .toList();
  return uniqueMaterials;
},
```
Здесь мы сначала получаем список всех объектов `Zagotovka2Model` из вашего API-сервиса. Затем мы фильтруем этот список по заданному шаблону `pattern`, получаем список всех значений поля `material` и преобразуем его в множество (Set) для удаления дубликатов.

> [!warning]
> Так же стоит учесть что если в атозаполнении будет тип данных не `string`, а например `int`, то данные необходимо преобразовать в `string`! Рассмотрим на примере поля `diametrZagotovki`


Если тип данных поля `diametrZagotovki` int, то вы можете изменить методы `suggestionsCallback` , `itemBuilder`  и `onSuggestionSelected` следующим образом:
```dart
suggestionsCallback: (pattern) async {
  final zagotovka2ModelList = await zagotovka2Api.getZagotovka2Model();
  final uniqueDiameters = zagotovka2ModelList!
      .where((model) => model.diametrZagotovki.toString().contains(pattern))
      .map((model) => model.diametrZagotovki)
      .toSet()
      .toList();
  return uniqueDiameters;
},
```
Здесь мы преобразуем значение поля `diametrZagotovki` в строку с помощью метода `toString()` перед фильтрацией и преобразованием в список.
```dart
itemBuilder: (context, suggestion) {
  return ListTile(
    title: Text(suggestion.toString()),
  );
},
```
Здесь мы преобразуем значение `suggestion` (которое является типом int) в строку с помощью метода `toString()` перед отображением его в ListTile.

Не забываем про `onSuggestionSelected`
```dart
onSuggestionSelected: (suggestion) {
    _diameterController.text = suggestion as String;
},
```

Эти изменения должны помочь избежать ошибки и правильно отображать значения поля `diametrZagotovki` в выпадающем списке.

> [!NOTE]
> Так же бывают случаи когда нужно фильтровать данные в полях на основе данных введенных в других полях, при этом должна быть возможность начинать заполнение с любого поля в любой последовательности.

Для того, чтобы обеспечить универсальность фильтрации нужно фильтровать список моделей последовательно по каждому полю. Для этого можно использовать переменную `filteredModels`, которая будет хранить отфильтрованные модели после каждого шага.
```dart
class _ProizvodstvoFormState extends State<ProizvodstvoForm> {
  final _formKey = GlobalKey<FormBuilderState>();
  final zagotovka2Api = Zagotovka2Api();
  final TextEditingController _diameterController = TextEditingController();
  final TextEditingController _materialController = TextEditingController();
  final TextEditingController _thicknessController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: FormBuilder(
          key: _formKey,
          child: Padding(
            padding: const EdgeInsets.all(50.0),
            child: Column(
              mainAxisSize: MainAxisSize.min,
              children: [
                // фильтр по материалу
                TypeAheadFormField(
                  textFieldConfiguration: TextFieldConfiguration(
                    decoration: InputDecoration(labelText: 'Материал'),
                    controller: _materialController,
                  ),
                  suggestionsCallback: (pattern) async {
                    final zagotovka2ModelList =
                        await zagotovka2Api.getZagotovka2Model();
                    var filteredModels = zagotovka2ModelList!;

                    if (_diameterController.text.isNotEmpty) {
                      filteredModels = filteredModels.where((model) =>
                          model.diametrZagotovki.toString() ==
                          _diameterController.text);
                    }

                    if (_thicknessController.text.isNotEmpty) {
                      filteredModels = filteredModels.where((model) =>
                          model.tolshinaStenki.toString() ==
                          _thicknessController.text);
                    }

                    filteredModels = filteredModels.where((model) =>
                        model.material.contains(pattern)).toList();

                    final uniqueMaterials = filteredModels
                        .map((model) => model.material)
                        .toSet()
                        .toList();

                    return uniqueMaterials
                        .where((material) => material.contains(pattern))
                        .toList();
                  },
                  itemBuilder: (context, suggestion) {
                    return ListTile(
                      title: Text(suggestion),
                    );
                  },
                  onSuggestionSelected: (suggestion) {
                    _materialController.text = suggestion as String;
                  },
                ),
                // фильтр по диаметру
                TypeAheadFormField(
                  textFieldConfiguration: TextFieldConfiguration(
                    decoration: InputDecoration(labelText: 'Диаметр'),
                    controller: _diameterController,
                  ),
                  suggestionsCallback: (pattern) async {
                    final zagotovka2ModelList =
                        await zagotovka2Api.getZagotovka2Model();
                    var filteredModels = zagotovka2ModelList!;

                    if (_materialController.text.isNotEmpty) {
                      filteredModels = filteredModels.where((model) =>
                          model.material == _materialController.text);
                    }

                    if (_thicknessController.text.isNotEmpty) {
                      filteredModels = filteredModels.where((model) =>
                          model.tolshinaStenki.toString() ==
                          _thicknessController.text);
                    }

                    filteredModels = filteredModels.where((model) =>
                        model.diametrZagotovki.toString().contains(pattern))
                        .toList();

                    final uniqueDiameters = filteredModels
                        .map((model) => model.diametrZagotovki.toString())
                        .toSet()
                        .toList();

                    return uniqueDiameters
                        .where((diameter) => diameter.contains(pattern))
                        .toList();
                  },
                  itemBuilder: (context, suggestion) {
                    return ListTile(
                      title: Text(suggestion),
                    );
                  },
                  onSuggestionSelected: (suggestion) {
                    _diameterController.text = suggestion as String;
                  },
                ),
                // фильтр по толщине стенки
                TypeAheadFormField(
                  textFieldConfiguration: TextFieldConfiguration(
                    decoration: InputDecoration(labelText: 'Толщина стенки'),
                    controller: _thicknessController,
                  ),
                  suggestionsCallback: (pattern) async {
                    final zagotovka2ModelList =
                        await zagotovka2Api.getZagotovka2Model();
                    var filteredModels = zagotovka2ModelList!;

                    if (_materialController.text.isNotEmpty) {
                      filteredModels = filteredModels.where((model) =>
                          model.material == _materialController.text);
                    }

                    if (_diameterController.text.isNotEmpty) {
                      filteredModels =filteredModels.where((model) =>
                          model.diametrZagotovki.toString() ==
                          _diameterController.text);
                    }

                    filteredModels = filteredModels.where((model) =>
                        model.tolshinaStenki.toString().contains(pattern))
                        .toList();

                    final uniqueThicknesses = filteredModels
                        .map((model) => model.tolshinaStenki.toString())
                        .toSet()
                        .toList();

                    return uniqueThicknesses
                        .where((thickness) => thickness.contains(pattern))
                        .toList();
                  },
                  itemBuilder: (context, suggestion) {
                    return ListTile(
                      title: Text(suggestion),
                    );
                  },
                  onSuggestionSelected: (suggestion) {
                    _thicknessController.text = suggestion as String;
                  },
                ),
              ],
            ),
          ),
        ),
      ),
    );
  }
}
```
отметим несколько ключевых моментов
1. Проверка на наличие значения в поле

В  коде используются три текстовых поля для фильтрации: `_materialController`, `_diameterController` и `_thicknessController`. Чтобы проверить, было ли введено значение в поле, мы используем метод `isNotEmpty`, который возвращает `true`, если значение в поле не пустое, и `false` в противном случае.

```dart
if (_diameterController.text.isNotEmpty) {
  // делаем фильтрацию по диаметру
}
```

2. Цикл if со следующей логикой

Для обеспечения универсальности фильтрации мы используем переменную `filteredModels`, которая хранит список моделей, отфильтрованных после каждого шага. В цикле `if` мы проверяем, было ли введено значение в предыдущее поле фильтрации, и если да, то мы фильтруем список моделей только по текущему полю. Если значение не было введено, то пропускаем этот шаг фильтрации.


```dart
if (_materialController.text.isNotEmpty) {
  filteredModels = filteredModels.where((model) =>
      model.material == _materialController.text);
}

if (_diameterController.text.isNotEmpty) {
  filteredModels = filteredModels.where((model) =>
      model.diametrZagotovki.toString() ==
      _diameterController.text);
}

if (_thicknessController.text.isNotEmpty) {
  filteredModels = filteredModels.where((model) =>
      model.tolshinaStenki.toString() ==
      _thicknessController.text);
}
```

3. Использование методов `toList()`, `toSet()` и `map()`

Код использует несколько методов для работы со списками. Вот что они делают:

- `toList()` - преобразует коллекцию в список.
- `toSet()` - преобразует коллекцию в множество (с уникальными элементами).
- `map()` - применяет функцию к каждому элементу коллекции и возвращает список результатов.

В  коде метод `map()` используется для преобразования списка моделей в список значений определенного поля, например, значения поля `material`. Метод `toSet()` используется для удаления повторяющихся элементов из списка, а метод `toList()` - для преобразования множества обратно в список.

```dart
final uniqueMaterials = filteredModels
    .map((model) => model.material)
    .toSet()
    .toList();
```


## Обработка ошибок

Нужно обрабатывать различные ошибки:

- Проверять код ответа (`response.statusCode`)
- Обрабатывать исключения при запросе
- Возвращать понятные ошибки приложению

Это основные моменты при работе с API во Flutter.