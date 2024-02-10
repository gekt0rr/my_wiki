# proizvodstvo_form.dart

Приведенный код представляет собой Flutter-код для создания полей автозаполнения (TypeAheadFormField) в столбце. Каждое поле автозаполнения предлагает пользователю варианты на основе введенного текста и обновляет соответствующий контроллер текстом выбранного варианта.

В данном коде есть три поля автозаполнения: "Материал", "Диаметр" и "Толщина стенки". Каждое поле имеет конфигурацию textFieldConfiguration, которая определяет внешний вид текстового поля и связанный с ним контроллер.

Функция suggestionsCallback определяет, какие варианты предлагать для автозаполнения на основе введенного текста. В данном случае, она использует список моделей zagotovka2ModelList и фильтрует его с помощью функции filterZagotovka2Models, которая принимает в качестве параметров значения из соответствующих контроллеров. Затем из отфильтрованных моделей извлекаются уникальные значения для соответствующего поля, и возвращаются варианты, содержащие введенный текст.

Функция itemBuilder определяет, как отображать каждый вариант в выпадающем списке предложений. В данном случае, используется ListTile, который отображает текст варианта.

Функция onSuggestionSelected вызывается при выборе варианта из списка предложений и устанавливает выбранный текст в соответствующий контроллер.

1. `filterZagotovka2Models`: Эта функция выполняет фильтрацию списка моделей `models` на основе значений из контроллеров `material`, `diameter` и `thickness`. Возвращается отфильтрованный список моделей, которые соответствуют заданным критериям. Вот вырезка кода:

```dart
List<Zagotovka2Model> filterZagotovka2Models(List<Zagotovka2Model> models,
    String material, String diameter, String thickness) {
  return models.where((model) {
    final materialMatch = material.isEmpty ||
        model.material.toLowerCase().contains(material.toLowerCase());
    final diameterMatch = diameter.isEmpty ||
        model.diametrZagotovki.toString().contains(diameter);
    final thicknessMatch = thickness.isEmpty ||
        model.tolshinaStenki.toString().contains(thickness);
    return materialMatch && diameterMatch && thicknessMatch;
  }).toList();
}
```

2. `TypeAheadFormField` для поля "Материал": Это поле автозаполнения позволяет пользователю выбрать материал на основе введенного текста. Вот вырезка кода:

```dart
TypeAheadFormField(
  textFieldConfiguration: TextFieldConfiguration(
    decoration: InputDecoration(labelText: 'Материал'),
    controller: _materialController,
  ),
  suggestionsCallback: (pattern) async {
    // Получение списка моделей
    final zagotovka2ModelList = await zagotovka2Api.getZagotovka2Model();

    // Фильтрация моделей
    final filteredModels = filterZagotovka2Models(
      zagotovka2ModelList!,
      _materialController.text,
      _diameterController.text,
      _thicknessController.text,
    );

    // Получение уникальных значений материалов
    final uniqueMaterials = filteredModels
        .map((model) => model.material)
        .toSet()
        .toList();

    // Возврат вариантов, содержащих введенный текст
    return uniqueMaterials
        .where((material) => material
            .toLowerCase()
            .contains(pattern.toLowerCase()))
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
```

3. `TypeAheadFormField` для поля "Диаметр": Это поле автозаполнения позволяет пользователю выбрать диаметр на основе введенного текста. Вот вырезка кода:

```dart
TypeAheadFormField(
  textFieldConfiguration: TextFieldConfiguration(
    decoration: InputDecoration(labelText: 'Диаметр'),
    controller: _diameterController,
  ),
  suggestionsCallback: (pattern) async {
    // Получение списка моделей
    final zagotovka2ModelList = await zagotovka2Api.getZagotovka2Model();

    // Фильтрация моделей
    final filteredModels = filterZagotovka2Models(
      zagotovka2ModelList!,
      _materialController.text,
      _diameterController.text,
      _thicknessController.text,
    );

    // Получение уникальных значений диаметров
    final uniqueDiameters = filteredModels
        .map((model) => model.diametrZagotovki.toString())
        .toSet()
        .toList();

    // Возврат вариантов, содержащих введенный текст
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
```

4. `TypeAheadFormField` для поля "Толщина стенки": Это поле автозаполнения позволяет пользователю выбрать толщину стенки на основе введенного текста. Вот вырезка кода:

```dart
Type```dart
TypeAheadFormField(
  textFieldConfiguration: TextFieldConfiguration(
    decoration: InputDecoration(labelText: 'Толщина стенки'),
    controller: _thicknessController,
  ),
  suggestionsCallback: (pattern) async {
    // Получение списка моделей
    final zagotovka2ModelList = await zagotovka2Api.getZagotovka2Model();

    // Фильтрация моделей
    final filteredModels = filterZagotovka2Models(
      zagotovka2ModelList!,
      _materialController.text,
      _diameterController.text,
      _thicknessController.text,
    );

    // Получение уникальных значений толщин стенок
    final uniqueThicknesses = filteredModels
        .map((model) => model.tolshinaStenki.toString())
        .toSet()
        .toList();

    // Возврат вариантов, содержащих введенный текст
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
```

Эти три `TypeAheadFormField` используются для создания полей автозаполнения с разными названиями ("Материал", "Диаметр" и "Толщина стенки"). Каждое поле имеет свой `textFieldConfiguration` для настройки текстового поля, `suggestionsCallback` для получения вариантов автозаполнения, `itemBuilder` для отображения вариантов в виде списка и `onSuggestionSelected` для обработки выбора варианта пользователем.

Общая структура кода заключается в создании столбца (`Column`), в котором содержатся все три поля `TypeAheadFormField`. Каждое поле использует свои контроллеры (`_materialController`, `_diameterController`, `_thicknessController`), которые могут быть предварительно созданы в коде.