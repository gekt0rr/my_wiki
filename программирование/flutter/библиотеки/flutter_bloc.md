# flutter_bloc
[flutter_bloc](https://pub.dev/packages/flutter_bloc) — это одно из средств управления состоянием для приложений Flutter. Вы можете использовать его для простой обработки всех возможных состояний вашего приложения. [полезная страница для ознакомления](https://medium.com/flutter-community/flutter-bloc-for-beginners-839e22adb9f5)

Представьте, что вы хотите создать блочную логику, связанную с виджетами страниц , вам понадобятся эти три класса:

- pages_bloc.dart - логика
- pages_event.dart - эвенты, которые будут менять состояния
- pages_state.dart - состояния


## pages_event.dart
```dart
part of 'pages_bloc.dart';

@immutable
abstract class PagesEvent {}

class PagesEventLoading extends PagesEvent {}

class PageHomeEvent extends PagesEvent {}

class PageItem1Event extends PagesEvent {}

class ProizvodstvoDashboardEvent extends PagesEvent {}

class ProizvodstvoFormEvent extends PagesEvent {}

class ProizvodstvoListEvent extends PagesEvent {}

```
## pages_state.dart
```dart
part of 'pages_bloc.dart';

@immutable
abstract class PagesState {}

class PagesInitial extends PagesState {}

// состояние - страница загружается (показываем прогрессбар)
class PagesStateLoading extends PagesState {}

// состояние - страница загружена (показываем данные)
class PagesStateLoaded extends PagesState {
  final Widget page;
  PagesStateLoaded(this.page);
}

```
## pages_bloc.dart
```dart
class PagesBloc extends Bloc<PagesEvent, PagesState> {
  // начальное состояние - страница Item1
  PagesBloc() : super(PagesInitial()) {
    on<PageHomeEvent>(_onPageHomeEvent);
    on<PageItem1Event>(_onPageItem1Event);
    on<ProizvodstvoDashboardEvent>(_onProizvodstvoDashboardEvent);
    on<ProizvodstvoFormEvent>(_onProizvodstvoFormEvent);
    on<ProizvodstvoListEvent>(_onProizvodstvoListEvent);
  }

  _onPageHomeEvent(PageHomeEvent event, Emitter<PagesState> emit) {
    emit(PagesStateLoading());
    emit(PagesStateLoaded(const HomeDefault()));
  }

  _onPageItem1Event(PageItem1Event event, Emitter<PagesState> emit) {
    emit(PagesStateLoading());
    emit(PagesStateLoaded(const Item1Page()));
  }

  _onProizvodstvoDashboardEvent(
      ProizvodstvoDashboardEvent event, Emitter<PagesState> emit) {
    emit(PagesStateLoading());
    emit(PagesStateLoaded(const ProizvodstvoDashboard()));
  }
// и так далее по виджетам
```
## MultiBlocProvider
Данный виджет необходим для объявления наших block в коде. Лучше всего применять перед виджетом MaterialApp, так как в дальнейшем все наши block будут доступны во всем коде.
```dart
return MultiBlocProvider(
      providers: [
        BlocProvider(
          create: (context) => MyColorsBloc(),
        ),
        BlocProvider(
          create: (context) => PagesBloc(),
        ),
        BlocProvider(
          create: (context) => ProizvodstvoBloc(zadelka5Repo),
        ),
      ],
```

## BlocBuilder
Данный виджет занимается тем, что отрисовывает виджеты на основе состояний.
```dart
return BlocBuilder<MyColorsBloc, MyColorsState>(
      builder: (context, state) {
        // return scafold...
```
для смены состояний чаще всего используют кнопки
```dart
						children: [
                            ElevatedButton(
                              onPressed: () {
                                context
                                    .read<MyColorsBloc>()
                                    .add(MyColorsEventRed());
                                Navigator.pop(context);
                              },
                              child: const Text('Red'),
                            ),
                            ElevatedButton(
                              onPressed: () {
                                context
                                    .read<MyColorsBloc>()
                                    .add(MyColorsEventGreen());
                                Navigator.pop(context);
                              },
                              child: const Text('Green'),
                            ),
                            ...
```
так же по нажатию на кнопку можно отправить сразу несколько состояний
```dart
              ListTile(
                leading: const Icon(Icons.precision_manufacturing_outlined),
                title: const Text("Производство"),
                onTap: () {
                  context.read<PagesBloc>().add(ProizvodstvoListEvent());
                  context.read<ProizvodstvoBloc>().add(Zadelka5LoadEvent());
                  // context.read<PagesBloc>().add(ProizvodstvoTestEvent());
                  //Navigator.pop(context);
                },
              ),
            ],
```