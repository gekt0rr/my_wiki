# Flutter_typeahead
Виджет [TypeAhead](https://pub.dev/packages/flutter_typeahead) (автозаполнение) для Flutter, где вы можете показывать предложения для пользователи по мере ввода

![](https://raw.githubusercontent.com/AbdulRahmanAlHamali/flutter_typeahead/master/flutter_typeahead.gif)

## Особенности[](https://pub.dev/packages/flutter_typeahead#features)

- Показывает предложения в оверлее, которое плавает поверх других виджетов.
- Позволяет указать, как будут выглядеть предложения через функция строителя
- Позволяет указать, что происходит, когда пользователь нажимает на предложение
- Принимает все параметры, которые принимают традиционные текстовые поля, например оформление, пользовательский TextEditingController, стиль текста и т. д.
- Предоставляет две версии: обычную версию и [FormField.](https://docs.flutter.io/flutter/widgets/FormField-class.html) версия, которая принимает проверку, отправку и т. д.
- Обеспечивает высокую настраиваемость; вы можете настроить оформление ящика для предложений, полоса загрузки, анимация, продолжительность отката и т. д.

Чтобы использовать, нужно:

1. Добавить зависимость в pubspec.yaml
```yaml
dependencies:
  flutter_typeahead: ^3.1.1
```
2. Импортировать пакет
Вы можете импортировать пакет с помощью:
```dart
import 'package:flutter_typeahead/flutter_typeahead.dart';
```
Для пользователей Купертино импорт:
```dart
import 'package:flutter_typeahead/cupertino_flutter_typeahead.dart';
```
3. Создать виджет TypeAheadFormField
```dart
TypeAheadFormField(
  // Конфигурация текстового поля 
  textFieldConfiguration: TextFieldConfiguration(
    autofocus: true,
  ),
  
  // Колбек для генерации подсказок
  suggestionsCallback: (pattern) {
    return ['Опция 1', 'Опция 2']; 
  },
)
```
В свойстве suggestionsCallback передается функция, которая должна вернуть список подсказок на основе введенного текста.

Также можно настроить отображение подсказок, обработку выбора и многое другое.

Это основы, на которых можно построить автозаполнение из API с фильтрацией и кешированием.

## Примеры использования[](https://pub.dev/packages/flutter_typeahead#usage-examples)
### Пример материала 1:[](https://pub.dev/packages/flutter_typeahead#material-example-1)

```dart
TypeAheadField(
  textFieldConfiguration: TextFieldConfiguration(
    autofocus: true,
    style: DefaultTextStyle.of(context).style.copyWith(
      fontStyle: FontStyle.italic
    ),
    decoration: InputDecoration(
      border: OutlineInputBorder()
    )
  ),
  suggestionsCallback: (pattern) async {
    return await BackendService.getSuggestions(pattern);
  },
  itemBuilder: (context, suggestion) {
    return ListTile(
      leading: Icon(Icons.shopping_cart),
      title: Text(suggestion['name']),
      subtitle: Text('\$${suggestion['price']}'),
    );
  },
  onSuggestionSelected: (suggestion) {
    Navigator.of(context).push(MaterialPageRoute(
      builder: (context) => ProductPage(product: suggestion)
    ));
  },
)
```

В приведенном выше коде `textFieldConfiguration`собственность позволяет нам настроить отображаемый `TextField`как мы хотим. В этом примере мы настройка `autofocus`, `style`и `decoration`характеристики.

The `suggestionsCallback`вызывается со строкой поиска, которую пользователь типов и, как ожидается, вернет `List`данных либо синхронно, либо асинхронно. В этом примере мы вызываем асинхронную функцию называется `BackendService.getSuggestions`который получает список предложения.

The `itemBuilder`вызывается для создания виджета для каждого предложения. В этом примере мы строим простую `ListTile`который показывает имя и цена товара. Обратите внимание, что вы не должны предоставлять `onTap`обратный звонок здесь. Об этом позаботится виджет TypeAhead.

The `onSuggestionSelected`это обратный вызов, который вызывается, когда пользователь нажимает на предположение. В этом примере, когда пользователь нажимает предложение, мы переходим на страницу, которая показывает нам информацию о нарезанный продукт.

### Пример материала 2:[](https://pub.dev/packages/flutter_typeahead#material-example-2)

Вот еще один пример, где мы используем TypeAheadFormField внутри `Form`:

```dart
final GlobalKey<FormState> _formKey = GlobalKey<FormState>();
final TextEditingController _typeAheadController = TextEditingController();
String _selectedCity;
...
Form(
  key: this._formKey,
  child: Padding(
    padding: EdgeInsets.all(32.0),
    child: Column(
      children: <Widget>[
        Text(
          'What is your favorite city?'
        ),
        TypeAheadFormField(
          textFieldConfiguration: TextFieldConfiguration(
            controller: this._typeAheadController,
            decoration: InputDecoration(
              labelText: 'City'
            )
          ),          
          suggestionsCallback: (pattern) {
            return CitiesService.getSuggestions(pattern);
          },
          itemBuilder: (context, suggestion) {
            return ListTile(
              title: Text(suggestion),
            );
          },
          transitionBuilder: (context, suggestionsBox, controller) {
            return suggestionsBox;
          },
          onSuggestionSelected: (suggestion) {
            this._typeAheadController.text = suggestion;
          },
          validator: (value) {
            if (value.isEmpty) {
              return 'Please select a city';
            }
          },
          onSaved: (value) => this._selectedCity = value,
        ),
        SizedBox(height: 10.0,),
        RaisedButton(
          child: Text('Submit'),
          onPressed: () {
            if (this._formKey.currentState.validate()) {
              this._formKey.currentState.save();
              Scaffold.of(context).showSnackBar(SnackBar(
                content: Text('Your Favorite City is ${this._selectedCity}')
              ));
            }
          },
        )
      ],
    ),
  ),
)
```

Здесь мы присваиваем `controller`собственность `textFieldConfiguration` а `TextEditingController`что мы называем `_typeAheadController`. Мы используем этот контроллер в `onSuggestionSelected`обратный вызов для установки стоимость `TextField`к выбранному предложению.

The `validator`обратный вызов может использоваться как любой `FormField.validator`функция. В нашем примере он проверяет, было ли введено значение, и отображает сообщение об ошибке, если нет. `onSaved`обратный вызов используется для сохранить значение поля в `_selectedCity`переменная-член.

The `transitionBuilder`позволяет настроить анимацию ящик для предложений. В этом примере мы возвращаем поле предложений. немедленно, что означает, что нам не нужна анимация.

### Материал с альтернативной архитектурой макета:[](https://pub.dev/packages/flutter_typeahead#material-with-alternative-layout-architecture)

По умолчанию TypeAhead использует `ListView`отображать элементы, созданные `itemBuilder`. Если вы укажете `layoutArchitecture`компонент, он будет использовать этот компонент вместо этого. Например, вот как мы визуализируем элементы в сетке, используя стандартный `GridView`:

```dart
TypeAheadField(
    ...,
  layoutArchitecture: (items, scrollContoller) {
        return ListView(
            controller: scrollContoller,
            shrinkWrap: true,
            children: [
              GridView.count(
                physics: const ScrollPhysics(),
                crossAxisCount: 3,
                crossAxisSpacing: 8,
                mainAxisSpacing: 8,
                childAspectRatio: 5 / 5,
                shrinkWrap: true,
                children: items.toList(),
              ),
            ]);
      },
);
```