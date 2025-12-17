# Provider

[provider](https://pub.dev/packages/provider)
[repository](https://github.com/rrousselGit/provider)

dependencies:

- collection
- [nested](./nested.md)

## Usages

### Injection

```dart
Provider<Foo>(
  create: (context) => Foo(),
  child: CustomWidget(),
);
```

```dart
MultiProvider(
  providers: [
    Provider<Foo>(create: (_) => Foo()),
    Provider<Bar>(create: (_) => Bar()),
  ],
  child: CustomWidget(),
)
```

### Retrieve 

#### Widget

Consumer

```dart
Consumer<Foo>(
  builder: (BuildContext, Foo foo, Widget? child) {
    return FooWidget(foo: foo, child: child);
  },
  child: CustomWidget(),
)
```

Selector

```dart
Selector<Foo, Bar>(
  selector: (BuildContext context, Foo foo) => foo.bar,
  builder: (BuildContext context, Bar bar, Widget? child) {
    return BarWidget(bar: bar, child: child);
  },
  child: CustomWidget(),
)
```

#### Function

Provider.of

```dart
Widget build(BuildContext context) {
  Foo foo = Provider.of<Foo>(context);
  return Text(foo.name);
}
```

```dart
Widget build(BuildContext context) {
  return RaisedButton(
    onPressed: () {
      Foo foo = Provider.of<Foo>(context, listen: false);
      foo.bar();
    },
  );
}
```

BuildContext.read

```dart
Widget build(BuildContext context) {
  return RaisedButton(
    onPressed: () {
      context.read<Foo>().bar();
    },
  );
}
```

BuildContext.watch

```dart
Widget build(BuildContext context) {
  final Foo foo = context.watch<Foo>();
  return Text(foo.name);
}
```

BuildContext.select

```dart
Widget build(BuildContext context) {
  final String name = context.select<Foo, String>((Foo foo) => foo.name);
  return Text(name);
}
```

Locator

```dart
class Foo {
  Foo(this.read);
  final Locator read;
  void bar() {
    print(read<Bar>());
  }
}

Widget build(BuildContext context) {
  return Provider(
    create: (context) => Foo(context.read),
    child: CustomWidget(),
  );
}
```

## Blueprint

```mermaid
classDiagram
  direction LR

  namespace nested {
    class Nested
    class SingleChildStatelessWidget { <<abstract>> }
    class SingleChildStatefulWidget { <<abstract>> }
  }

  class InheritedProvider~T~
  class ValueListenableProvider~T~
  class Provider~T~
  class MultiProvider
  class DeferredInheritedProvider~T,R~

  namespace consumer {
    class Consumer~T~
    class Consumer2~A,B~
    class Consumer3~A,B,C~
    class Consumer4~A,B,C,D~
    class Consumer5~A,B,C,D,E~
    class Consumer6~A,B,C,D,E,F~
  }

  namespace selector {
    class Selector0~T~
    class Selector~A,S~
    class Selector2~A,B,S~
    class Selector3~A,B,C,S~
    class Selector4~A,B,C,D,S~
    class Selector5~A,B,C,D,E,S~
    class Selector6~A,B,C,D,E,F,S~
  }

  namespace async_provider {
    class StreamProvider~T~
    class FutureProvider~T~
  }

  namespace listenable_provider {
    class ListenableProvider~T~
    class ListenableProxyProvider0~R~
    class ListenableProxyProvider~T,R~
    class ListenableProxyProvider2~T,T2,R~
    class ListenableProxyProvider3~T,T2,T3,R~
    class ListenableProxyProvider4~T,T2,T3,T4,R~
    class ListenableProxyProvider5~T,T2,T3,T4,T5,R~
    class ListenableProxyProvider6~T,T2,T3,T4,T5,T6,R~
  }

  namespace change_notifier_provider {
    class ChangeNotifierProvider~T~
    class ChangeNotifierProxyProvider0~R~
    class ChangeNotifierProxyProvider~T,R~
    class ChangeNotifierProxyProvider2~T,T2,R~
    class ChangeNotifierProxyProvider3~T,T2,T3,R~
    class ChangeNotifierProxyProvider4~T,T2,T3,T4,R~
    class ChangeNotifierProxyProvider5~T,T2,T3,T4,T5,R~
    class ChangeNotifierProxyProvider6~T,T2,T3,T4,T5,T6,R~
  }

  namespace proxy_provider {
    class ProxyProvider0~R~
    class ProxyProvider~T,R~
    class ProxyProvider2~T,T2,R~
    class ProxyProvider3~T,T2,T3,R~
    class ProxyProvider4~T,T2,T3,T4,R~
    class ProxyProvider5~T,T2,T3,T4,T5,R~
    class ProxyProvider6~T,T2,T3,T4,T5,T6,R~
  }

  Nested <|-- MultiProvider
  SingleChildStatelessWidget <|-- InheritedProvider
  SingleChildStatelessWidget <|-- ValueListenableProvider
  SingleChildStatelessWidget <|-- Consumer
  SingleChildStatelessWidget <|-- Consumer2
  SingleChildStatelessWidget <|-- Consumer3
  SingleChildStatelessWidget <|-- Consumer4
  SingleChildStatelessWidget <|-- Consumer5
  SingleChildStatelessWidget <|-- Consumer6
  SingleChildStatefulWidget <|-- Selector0
  Selector0 <|-- Selector : T = S
  Selector0 <|-- Selector2 : T = S
  Selector0 <|-- Selector3 : T = S
  Selector0 <|-- Selector4 : T = S
  Selector0 <|-- Selector5 : T = S
  Selector0 <|-- Selector6 : T = S
  InheritedProvider <|-- Provider
  InheritedProvider <|-- ListenableProvider : T extends Listenable?
  InheritedProvider <|-- ListenableProxyProvider0 : T = R extends Listenable?
  InheritedProvider <|-- ProxyProvider0 : T = R
  InheritedProvider <|-- DeferredInheritedProvider : T = R
  DeferredInheritedProvider <|-- StreamProvider : T = Stream?, R = T
  DeferredInheritedProvider <|-- FutureProvider : T = Future?, R = T
  ListenableProvider <|-- ChangeNotifierProvider : T extends ChangeNotifier?
  ListenableProxyProvider0 <|-- ListenableProxyProvider
  ListenableProxyProvider0 <|-- ListenableProxyProvider2
  ListenableProxyProvider0 <|-- ListenableProxyProvider3
  ListenableProxyProvider0 <|-- ListenableProxyProvider4
  ListenableProxyProvider0 <|-- ListenableProxyProvider5
  ListenableProxyProvider0 <|-- ListenableProxyProvider6
  ProxyProvider0 <|-- ProxyProvider
  ProxyProvider0 <|-- ProxyProvider2
  ProxyProvider0 <|-- ProxyProvider3
  ProxyProvider0 <|-- ProxyProvider4
  ProxyProvider0 <|-- ProxyProvider5
  ProxyProvider0 <|-- ProxyProvider6
  ListenableProxyProvider0 <|-- ChangeNotifierProxyProvider0 : R extends ChangeNotifier?
  ListenableProxyProvider <|-- ChangeNotifierProxyProvider : R extends ChangeNotifier?
  ListenableProxyProvider2 <|-- ChangeNotifierProxyProvider2 : R extends ChangeNotifier?
  ListenableProxyProvider3 <|-- ChangeNotifierProxyProvider3 : R extends ChangeNotifier?
  ListenableProxyProvider4 <|-- ChangeNotifierProxyProvider4 : R extends ChangeNotifier?
  ListenableProxyProvider5 <|-- ChangeNotifierProxyProvider5 : R extends ChangeNotifier?
  ListenableProxyProvider6 <|-- ChangeNotifierProxyProvider6 : R extends ChangeNotifier?
```

## Principle

### InheritedProvider

```mermaid
classDiagram
  class SingleChildStatelessWidget {
    <<abstract>>
    - Widget? _child
  }
  class InheritedWidget {}
  class InheritedElement {}
  class InheritedProvider~T~ {
    - _Delegate~T~ _delegate
    - bool? _lazy
    + TransitionBuilder? builder
    + createElement() _InheritedProviderElement~T~
    + buildWithChild(context,child) Widget
    - _buildWithChild(child,key) Widget
  }
  class _InheritedProviderScope~T~ {
    + InheritedProvider~T~ owner
    + updateShouldNotify(oldWidget) bool
    + createElement() _InheritedProviderScopeElement~T~ 
  }
  class _InheritedProviderScopeElement~T~ {
    - _DelegateState~T, _Delegate~ _delegateState
  }
  class _Delegate~T~ {
    <<abstract>>
    + createState() _DelegateState~T, _Delegate~
  }
  class _DelegateState~T,D~ {
    + _InheritedProviderScopeElement~T?~? element
    + T value *
    + D delegate
    + bool hasValue *
    + willUpdateDelegate(newDelegate) bool
    + dispose() void
    + build(isBuildFromExternalSources) void
  }
  class _CreateInheritedProvider~T~ {}
  class _CreateInheritedProviderState {}
  class _ValueInheritedProvider~T~ {}
  class _ValueInheritedProviderState {}

  InheritedWidget <|-- _InheritedProviderScope
  InheritedElement <|-- _InheritedProviderScopeElement
  SingleChildStatelessWidget <|-- InheritedProvider
  _Delegate <|-- _CreateInheritedProvider
  _Delegate <|-- _ValueInheritedProvider
  _DelegateState <|-- _CreateInheritedProviderState
  _DelegateState <|-- _ValueInheritedProviderState

  _InheritedProviderScopeElement *-- _DelegateState : holds 
  _InheritedProviderScopeElement --> _InheritedProviderScope : holds
  _InheritedProviderScope --> InheritedProvider : holds
  InheritedProvider --> _Delegate : holds
  InheritedProvider ..> _InheritedProviderScope : builds
  _InheritedProviderScope ..> _InheritedProviderScopeElement : creates
  _Delegate ..> _DelegateState : creates


```

### MultiProvider

### Provider

```mermaid
classDiagram
  class Provider {
    + of~T~(context,listen) T
  }
```

### Consumer

```dart
class Consumer<T> extends SingleChildStatelessWidget {
  final Widget Function(BuildContext context, T value, Widget? child) builder;

  @override
  Widget buildWithChild(BuildContext context, Widget? child) {
    return builder(
      context,
      Provider.of<T>(context),
      child,
    );
  }
}
```

### Selector

```mermaid
classDiagram
  direction TB
  namespace nested {
    class SingleChildStatefulElement {
      + SingleChildStatefulWidget widget
      + SingleChildState~SingleChildStatefulWidget~ state 
      + build() Widget
    }
    class SingleChildStatefulWidget {
      <<abstract>>
      - Widget? _child
      + createElement() SingleChildStatefulElement
    }
    class SingleChildState~T~ {
      <<abstract>>
      + buildWithChild(context,child) Widget *
      + build(context) Widget
    }
  }
  class Selector0~T~ {
    + ValueWidgetBuilder~T~ builder
    + Function selector
    - ShouldRebuild~T~? _shouldRebuild
    + createState() _Selector0State~T~
  }
  class _Selector0State~T~ {
    + T? value;
    + Widget? cache;
    + Widget? oldWidget;
    + buildWithChild(context,child) Widget
  }
  class Selector~A,S~
  class Selector2~A,B,S~
  class Selector3~A,B,C,S~
  class Selector4~A,B,C,D,S~
  class Selector5~A,B,C,D,E,S~
  class Selector6~A,B,C,D,E,F,S~

  SingleChildStatefulWidget <|-- Selector0
  SingleChildState <|-- _Selector0State
  Selector0 <|-- Selector : T = S
  Selector0 <|-- Selector2 : T = S
  Selector0 <|-- Selector3 : T = S
  Selector0 <|-- Selector4 : T = S
  Selector0 <|-- Selector5 : T = S
  Selector0 <|-- Selector6 : T = S

  Selector0 ..|> _Selector0State : creates
  SingleChildStatefulElement *-- SingleChildState : holds
  SingleChildStatefulElement --> SingleChildStatefulWidget : holds
  SingleChildStatefulWidget ..|> SingleChildStatefulElement : creates
```

```dart
typedef ShouldRebuild<T> = bool Function(T previous, T next);
```

```dart
class Selector0<T> {
  Selector0({
    Key? key,
    required this.builder,
    required this.selector,
    ShouldRebuild<T>? shouldRebuild,
    Widget? child,
  })  : _shouldRebuild = shouldRebuild,
        super(key: key, child: child);

  final ValueWidgetBuilder<T> builder;
  final T Function(BuildContext) selector;
  final ShouldRebuild<T>? _shouldRebuild;
}
```
