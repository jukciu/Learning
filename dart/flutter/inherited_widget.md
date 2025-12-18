# InheritedWidget

## Usages

```dart
_ScrollableScope? widget = context.getInheritedWidgetOfExactType<_ScrollableScope>();
```

## Principle

```mermaid
classDiagram
  direction TB

  class Widget {
    <<abstract>>
    + Key? key
    # createElement() Element *
  }
  class ProxyWidget {
    <<abstract>>
    + Widget child
  }
  class InheritedWidget {
    <<abstract>>
    + createElement() InheritedElement
    # updateShouldNotify(oldWidget) bool *
  }
  class BuildContext {
    <<abstract>>
    + Widget widget *
    + BuildOwner? owner *
    + bool mounted *
    + Size? size *
    + findRenderObject() RenderObject? *
    + dependOnInheritedElement(ancestor, aspect) InheritedWidget *
    + dependOnInheritedWidgetOfExactType~T extends InheritedWidget~(aspect) T? *
    + getInheritedWidgetOfExactType~T extends InheritedWidget~()  T? *
    + getElementForInheritedWidgetOfExactType~T extends InheritedWidget~() InheritedElement? *
    + findAncestorWidgetOfExactType~T extends Widget~() T? *
    + findAncestorStateOfType~T extends State~() T? *
    + findRootAncestorStateOfType~T extends State~() T? *
    + findAncestorRenderObjectOfType~T extends RenderObject~() T? *
    + visitAncestorElements(visitor) void *
    + visitChildElements(visitor) void *
    + dispatchNotification(notification) void *
  }
  class Element {
    <<abstract>>
    Element? _parent
    + Widget widget
    + BuildOwner? owner
    + bool mounted
    + Size? size
    - PersistentHashMap~Type, InheritedElement~? _inheritedElements
    - Set~InheritedElement~? _dependencies
    - bool _hadUnsatisfiedDependencies
    # reassemble() void
    + visitChildren(visitor) void
    # updateChild(child, newWidget, newSlot) Element?
    # updateChildren(oldChildren, newWidgets, forgottenChildren, slots) List~Element~
    + mount(parent, newSlot) void
    + update(newWidget) void
    # inflateWidget(newWidget, newSlot) Element
    # forgetChild(Element child) void
    + activate() void
    + deactivate() void
    + unmount() void
    + didChangeDependencies() void
    + markNeedsBuild() void
    + rebuild(force) void
    # performRebuild() void
    - _updateInheritance() void
    + findRenderObject() RenderObject?
    + dependOnInheritedElement(ancestor, aspect) InheritedWidget
    + dependOnInheritedWidgetOfExactType~T extends InheritedWidget~(aspect) T?
    + getInheritedWidgetOfExactType~T extends InheritedWidget~()  T?
    + getElementForInheritedWidgetOfExactType~T extends InheritedWidget~() InheritedElement?
    + findAncestorWidgetOfExactType~T extends Widget~() T?
    + findAncestorStateOfType~T extends State~() T?
    + findRootAncestorStateOfType~T extends State~() T?
    + findAncestorRenderObjectOfType~T extends RenderObject~() T?
    + visitAncestorElements(visitor) void
    + visitChildElements(visitor) void
    + dispatchNotification(notification) void
  }
  class ComponentElement {
    <<abstract>>
    - Element? _child
    + Element? renderObjectAttachingChild 
    + mount(parent, newSlot) void
    - _firstBuild() void
    + performRebuild() void
    # build() Widget *
    + visitChildren(visitor) void
    + forgetChild(child) void
  }
  class ProxyElement {
    <<abstract>>
    + build() Widget
    + update(newWidget) void
    # updated(oldWidget) void
    # notifyClients(oldWidget) void *
  }
  class InheritedElement {
    - Map~Element, Object?~ _dependents
    - _updateInheritance() void
    # getDependencies(dependent) Object?
    # setDependencies(dependent, value) void
    # updateDependencies(dependent, aspect) void
    # notifyDependent(oldWidget, dependent) void
    # removeDependent(Element dependent) void
    + updated(oldWidget) void
    + notifyClients(oldWidget) void
  }
  
  Widget <|-- ProxyWidget
  ProxyWidget <|-- InheritedWidget
  BuildContext <|.. Element
  Element <|-- ComponentElement
  ComponentElement <|-- ProxyElement
  ProxyElement <|-- InheritedElement
  InheritedElement *-- InheritedWidget : holds
  InheritedWidget ..> InheritedElement : creates
```

### InheritedElement Tree

```dart
abstract class Element {
  PersistentHashMap<Type, InheritedElement>? _inheritedElements;

  @mustCallSuper
  void mount(Element? parent, Object? newSlot) {
    _updateInheritance();
  }

  @mustCallSuper
  void activate() {
    _updateInheritance();
  }

  @mustCallSuper
  void deactivate() {
    _inheritedElements = null;
  }

  void _updateInheritance() {
    _inheritedElements = _parent?._inheritedElements;
  }
}
```

```dart
class InheritedElement extends ProxyElement {
  @override
  void _updateInheritance() {
    final incomingWidgets =
        _parent?._inheritedElements ?? const PersistentHashMap<Type, InheritedElement>.empty();
    _inheritedElements = incomingWidgets.put(widget.runtimeType, this);
  }
}
```


### InheritedElement Dependencies

```dart
abstract class Element {
  Set<InheritedElement>? _dependencies;
  bool _hadUnsatisfiedDependencies = false;

  @mustCallSuper
  void activate() {
    _dependencies?.clear();
    _hadUnsatisfiedDependencies = false;    
  }

  @mustCallSuper
  void deactivate() {
    if (_dependencies != null && _dependencies!.isNotEmpty) {
      for (final InheritedElement dependency in _dependencies!) {
        dependency.removeDependent(this);
      }
    }
  }

  @mustCallSuper
  void unmount() {
    _dependencies = null;
  }

  @override
  InheritedWidget dependOnInheritedElement(InheritedElement ancestor, { Object? aspect }) {
    _dependencies ??= HashSet<InheritedElement>();
    _dependencies!.add(ancestor);
    ancestor.updateDependencies(this, aspect);
    return ancestor.widget as InheritedWidget;
  }

  @override
  T? dependOnInheritedWidgetOfExactType<T extends InheritedWidget>({Object? aspect}) {
    final InheritedElement? ancestor = _inheritedElements == null ? null : _inheritedElements![T];
    if (ancestor != null) {
      return dependOnInheritedElement(ancestor, aspect: aspect) as T;
    }
    _hadUnsatisfiedDependencies = true;
    return null;
  }
}
```

```dart
class InheritedElement extends ProxyElement {
  final Map<Element, Object?> _dependents = HashMap<Element, Object?>();

  @protected
  void setDependencies(Element dependent, Object? value) {
    _dependents[dependent] = value;
  }

  @protected
  void updateDependencies(Element dependent, Object? aspect) {
    setDependencies(dependent, null);
  }

  @protected
  @mustCallSuper
  void removeDependent(Element dependent) {
    _dependents.remove(dependent);
  }
}
```

### InheritedElement didChangeDependencies

```dart
abstract class Element {
  @mustCallSuper
  void activate() {
    final bool hadDependencies = (_dependencies != null && _dependencies!.isNotEmpty) || _hadUnsatisfiedDependencies;
    if (hadDependencies) {
      didChangeDependencies();
    }
  }

  @mustCallSuper
  void didChangeDependencies() {
    markNeedsBuild();
  }
}
```

```dart
abstract class ProxyElement extends ComponentElement {
  @override
  void update(ProxyWidget newWidget) {
    final ProxyWidget oldWidget = widget as ProxyWidget;
    updated(oldWidget);
  }

  @protected
  void updated(covariant ProxyWidget oldWidget) {
    notifyClients(oldWidget);
  }

  @protected
  void notifyClients(covariant ProxyWidget oldWidget);
}
```

```dart
class InheritedElement extends ProxyElement {
  @protected
  void notifyDependent(covariant InheritedWidget oldWidget, Element dependent) {
    dependent.didChangeDependencies();
  }

  @override
  void updated(InheritedWidget oldWidget) {
    if ((widget as InheritedWidget).updateShouldNotify(oldWidget)) {
      super.updated(oldWidget);
    }
  }

  @override
  void notifyClients(InheritedWidget oldWidget) {
    for (final Element dependent in _dependents.keys) {     
      notifyDependent(oldWidget, dependent);
    }
  }
}
```

```mermaid
---
title: update
---
flowchart TB
  start([Start])
  updated["updated"]
  updateShouldNotify{"updateShouldNotify?"}
  super["super.updated"]
  notifyClients["notifyClients"]
  notifyDependent["notifyDependent"]
  stop([Stop])
  start --> updated --> updateShouldNotify -->|YES| super --> notifyClients --> notifyDependent --> stop
  updateShouldNotify -->|NO| stop
```


**getInheritedWidgetOfExactType**

返回最近的widget, 无依赖关系

```mermaid
flowchart TB
  start([开始])
  fetch["向上查找最近的 InheritedElement"]
  null{"是否存在?"}
  return["返回查到的 **widget**"]
  returnNull["返回 **null**"]
  stop([结束])
  start --> fetch --> null -->|YES| return --> stop
  null -->|NO| returnNull --> stop
```

**dependOnInheritedWidgetOfExactType**

返回最近的widget,并建立依赖关系

```mermaid
flowchart TB
  start([开始])
  fetch["向上查找最近的 InheritedElement"]
  null{"是否存在?"}
  depend["添加依赖关系"]
  dependx["标记为 *未满足依赖*"]
  return["返回查到的 **widget**"]
  returnNull["返回 **null**"]
  stop([结束])
  start --> fetch --> null -->|YES| depend --> return --> stop
  null -->|NO| dependx --> returnNull --> stop
```
