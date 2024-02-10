#  Операторы  switch и case
Операторы `switch` хороши, когда существует множество возможных условий для одного значения. Вы должны сравнить значение со значением того же типа, которое не может измениться во время выполнения, вот простой пример:
```dart
int number = 1;
switch(number) {
  case 0:
    print('zero!');
    break; // Оператору switch необходимо указать на выход, иначе он будет выполнять каждый раз.
  case 1:
    print('one!');
    break;
  case 2:
    print('two!');
    break;
  default:
    print('choose a different number!');
}
```
Это совершенно справедливо. Переменная `number`может иметь любое количество значений: 1, 2, 3, 4, 66, 975, -12 или 55. Этот `switch` оператор — это просто более краткий способ написания `if/else` условия. Вот это слишком длинный `if`/ `else` блок, для которого вы должны предпочесть `switch` оператор:

```dart
int number = 1;
if (number == 0) {
    print('zero!');
} else if (number == 1) {
    print('one!');
} else if (number == 2) {
    print('two!');
} else {
    print('choose a different number!');
}
```

В двух словах `switch` обеспечивает краткий способ проверки любого количества значений. Однако важно помнить, что он работает только с константами. Это недействительно:

```dart
  int five = 5;
  switch(five) {
      case(five < 10):
      // do things...
  }
```

`five < 10` не является константой во время компиляции и поэтому не может использоваться. Это могло быть правдой или ложью. Вы не можете выполнять вычисления в строке случая оператора `switch`.

### Выход из оператора Switch

Каждый случай в `switch` должен заканчиваться ключевым словом, которое выходит из переключателя. Если вы этого не сделаете, `switch` выполнит несколько блоков кода.

```dart
switch(number) {
  case 1:
    print(number);
    break; // without this, the switch statement would execute case 2 also!
  case 2:
    print(number + 1)
    break;
}
```

Иногда желательно выполнить несколько блоков кода. В `switch`, вы можете пропустить несколько случаев, не добавляя `break` или `return`:

```dart
intnumber = 1;
switch(number) {
  case -1:
  case -2:
  case -3:
  case -4:
  case -5:
    print('negative!');
    break;
  case 1:
  case 2:
  case 3:
  case 4:
  case 5:
    print('positive!');
    break;
  case 0:
  default:
    print('zero!');
    break;
}
```

В этом примере, если число находится в диапазоне от -5 до -1, то будет напечатан `negative!`.

Чаще всего вы будете использовать `break`или `return`. `break` просто выходит из `switch`; другого эффекта это не дает. Он не возвращает значение. В Дарте `return` оператор немедленно завершает выполнение функции, и, следовательно, она выйдет из `switch`заявление. Помимо них, вы можете использовать `throw` ключевое слово, которое выдает ошибку.

### Выполнить несколько дел с помощью «продолжить»

Наконец, вы можете использовать `continue` утверждение и метку, если вы хотите провалиться, но при этом иметь логику в каждом случае:

```dart
String animal = 'tiger';
switch(animal) {
  case 'tiger':
    print("it's a tiger");
    continue alsoCat;
  case 'lion':
    print("it's a lion");
    continue alsoCat;
  alsoCat:
  case 'cat':
    print("it's a cat");
    break;
  // ...
}
```

Этот `switch` заявление будет напечатано `it's a tiger` и `it's a cat` на консоль.