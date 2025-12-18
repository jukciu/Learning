# Listenable

```mermaid
classDiagram
  direction TB
  class Listenable {
    <<abstract>>
    + Listenable.merge(listenables) $
    + addListener(listener) void *
    + removeListener(listener) void *
  }
  class ValueListenable~T~ {
    <<abstract>>
    + T value *
  }
  class ChangeNotifier {
    <<mixin>>
    # bool hasListeners
    + addListener(listener) void
    + removeListener(listener) void
    + dispose() void
    # notifyListeners() void
  }
  class ValueNotifier~T~ {
    + T value
  }

  Listenable <|-- ValueListenable
  Listenable <|.. ChangeNotifier
  ChangeNotifier <|-- ValueNotifier
  ValueListenable <|.. ValueNotifier
```

## Usages

```mermaid
classDiagram
  class StatefulWidget {
    <<abstract>>
    # createState() State
  }
  class ValueListenableBuilder~T~ {
    + ValueListenable~T~ valueListenable
    + ValueWidgetBuilder~T~ builder
    + Widget? child
    + createState() State~StatefulWidget~
  }
  class AnimatedWidget {
    <<abstract>>
    + Listenable listenable
    # Widget build(context) *
    + createState() State~AnimatedWidget~
  }
  class ListenableBuilder {
    + Listenable listenable
    + TransitionBuilder builder;
    + Widget? child;
    + build(context) Widget
  }
  class AnimatedBuilder {
    + Listenable animation
    + Listenable listenable
    + TransitionBuilder builder
  }

  StatefulWidget <|-- ValueListenableBuilder
  StatefulWidget <|-- AnimatedWidget
  AnimatedWidget <|-- ListenableBuilder
  ListenableBuilder <|-- AnimatedBuilder
```

## Principle

### changeNotifier

```dart
mixin class changeNotifier {
  int _count = 0;
  List<VoidCallback?> _listeners = _emptyListeners;

  @protected
  void notifyListeners() {
    if (_count == 0) {
      return;
    }
    final int end = _count;
    for (int i = 0; i < end; i++) {
      _listeners[i]?.call();
    }
  }
}
```

### ValueNotifier

```dart
class ValueNotifier<T> {
  T _value;

  @override
  T get value => _value;

  set value(T newValue) {
    if (_value == newValue) return;
    _value = newValue;
    notifyListeners();
  }
}
```
