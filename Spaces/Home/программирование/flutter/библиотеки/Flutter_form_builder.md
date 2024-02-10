# Flutter_form_builder

Библиотека для упрощения работы с формами во Flutter.
> Этот пакет помогает в создании форм сбора данных во Flutter, удаляя шаблон, необходимый для создания формы, проверки полей, реагирования на изменения и сбора окончательного пользовательского ввода.

Также включены общие готовые поля ввода формы для FormBuilder.

## Основные возможности

- Валидация полей
- Централизованное управление значениями
- Сохранение состояния при повороте экрана
- Группировка полей
- Кастомная обработка отправки формы

## Использование

1. Добавить зависимость в pubspec.yaml
```yaml
dependencies:
  flutter_form_builder: ^7.0.0
```
2. Импортировать библиотеку
```dart
import 'package:flutter_form_builder/flutter_form_builder.dart';
```
3. Обернуть форму в FormBuilder
```dart
FormBuilder( 
	key: _fbKey,
	// child: ... 
)
```
4. Добавить поля, используя виджеты FormBuilder
```dart
FormBuilderTextField(
  attribute: "name",
  validators: [
    FormBuilderValidators.required()
  ],
)
```
Доступны:

- FormBuilderTextField
- FormBuilderDropdown
- FormBuilderCheckbox
- FormBuilderDateTimePicker
- FormBuilderSlider
- и другие

5. Добавить кнопку отправки
```dart
FormBuilder(
  onSubmit: () {
    _fbKey.currentState.save();
    // Обработать данные
  } 
)	
```
6. Получить доступ к значениям через FormBuilderState
```dart
_fbKey.currentState.fields['name'];
```
## Пример валидации
```dart
FormBuilderTextField(
  name: 'email',
  validator: FormBuilderValidators.compose([
    FormBuilderValidators.required(context),
    FormBuilderValidators.email(context),
  ]),    
),
```
## Пример группировки полей
```dart
FormBuilderFieldGroup(
  name: 'personal_data',
  children: [
    FormBuilderTextField(
      name: 'first_name',
      
    ),
    FormBuilderTextField(
      name: 'last_name', 
    ),
  ],
)
```


## Список виджетов ввода

В настоящее время поддерживаются следующие поля:

- `FormBuilderCheckbox`- Одно поле флажка
- `FormBuilderCheckboxGroup`- Список флажков для множественного выбора
- `FormBuilderChoiceChip`- Создает чип, который действует как переключатель.
- `FormBuilderDateRangePicker`- Для выбора диапазона дат
- `FormBuilderDateTimePicker`- Для `Date`, `Time`и `DateTime`вход
- `FormBuilderDropdown`- Используется для выбора одного значения из списка в виде раскрывающегося списка.
- `FormBuilderFilterChip`- Создает чип, который действует как флажок.
- `FormBuilderRadioGroup`- Используется для выбора одного значения из списка виджетов радио.
- `FormBuilderRangeSlider`- Используется для выбора диапазона из диапазона значений
- `FormBuilderSegmentedControl`- Для выбора значения с помощью `CupertinoSegmentedControl`виджет как вход
- `FormBuilderSlider`- Для выбора числового значения на ползунке
- `FormBuilderSwitch`- Поле включения/выключения
- `FormBuilderTextField`- Ввод текстового поля Material Design.

## Экосистема

Вот дополнительные пакеты, которые вы можете использовать для расширения `flutter_form_builder`функциональность.

- [form_builder_validators](https://pub.dev/packages/form_builder_validators) — предоставляет удобный способ проверки данных, введенных в любой Flutter. `FormField`.
- [form_builder_extra_fields](https://pub.dev/packages/form_builder_extra_fields) — предоставляет дополнительные готовые поля ввода формы, совместимые с `flutter_form_builder`.
- [form_builder_file_picker](https://pub.dev/packages/form_builder_file_picker) — А `FormbuilderField`который позволяет выбирать изображения из памяти пользовательского устройства.
- [form_builder_image_picker](https://pub.dev/packages/form_builder_image_picker) — А `FormbuilderField`который позволяет выбирать изображения из Галереи или Камеры устройства.
- [form_builder_map_field](https://pub.dev/packages/form_builder_map_field) — А `FormbuilderField`для ввода географического местоположения.
- [form_builder_phone_field](https://pub.dev/packages/form_builder_phone_field) — А `FormbuilderField`для ввода международного номера телефона.

## Пример

`flutter_form_builder`дает нам полное решение для форм! Не только для TextField, но и для других готовых полей ввода формы.
```dart
import 'package:flutter/material.dart';
import 'package:flutter_form_builder/flutter_form_builder.dart';
import 'package:form_builder_validators/form_builder_validators.dart';

class HomePage3 extends StatelessWidget {
  HomePage3({Key? key}) : super(key: key);

  final _formKey = GlobalKey<FormBuilderState>();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: FormBuilder(
          key: _formKey,
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              FormBuilderTextField(
                name: 'email',
                validator: FormBuilderValidators.compose([
                  FormBuilderValidators.required(context),
                  FormBuilderValidators.email(context),
                ]),
              ),
              FormBuilderTextField(
                name: 'password',
                validator: FormBuilderValidators.compose([
                  FormBuilderValidators.required(context),
                  FormBuilderValidators.minLength(context, 6),
                ]),
              ),
              ElevatedButton(
                onPressed: () {
                  if (_formKey.currentState!.saveAndValidate()) {
                    print(_formKey.currentState!.value['email']);
                    print(_formKey.currentState!.value['password']);
                  }
                },
                child: const Text('Save'),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```
## Ресурсы

- [Документация](https://pub.dev/documentation/flutter_form_builder/latest/)
- [Примеры](https://github.com/danvick/flutter_form_builder/tree/master/example)

Таким образом библиотека flutter_form_builder упрощает работу с формами и валидацией во Flutter.


