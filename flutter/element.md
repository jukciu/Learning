# Element

**Widget**

```mermaid
classDiagram
  direction LR
  classDef widgets fill:#f9f,stroke:#fff,stroke-width:4px;

  class Widget {
    <<abstract>>
    + Key? key
    # createElement() Element *
    + canUpdate(oldWidget, newWidget) bool $
  }
  class StatelessWidget {
    <<abstract>>
    + createElement() StatelessElement
    # build(context) Widget *
  }
  class StatefulWidget {
    <<abstract>>
    + createElement() StatefulElement
    # createState() State *
  }
  class State~T extends StatefulWidget~ {
    <<abstract>>
    + T widget
    + BuildContext context
    + bool mounted
    # initState() void
    + didUpdateWidget(oldWidget) void
    # reassemble() void
    # setState(fn) void
    # deactivate() void
    # activate() void
    # dispose() void
    # build(context) Widget *
    # didChangeDependencies() void
  }
  class ProxyWidget {
    <<abstract>>
    + Widget child
  }
  class ParentDataWidget~T extends ParentData~ {
    <<abstract>>
    + createElement() ParentDataElement~T~ 
    # applyParentData(renderObject) void *
  }
  class InheritedWidget {
    <<abstract>>
    + createElement() InheritedElement
    # updateShouldNotify(oldWidget) bool *
  }
  class RenderObjectWidget {
    <<abstract>>
    + createElement() RenderObjectElement *
    # createRenderObject(context) RenderObject *
    # updateRenderObject(context, renderObject) void
    # didUnmountRenderObject(renderObject) void
  }
  class LeafRenderObjectWidget {
    <<abstract>>
    + createElement() LeafRenderObjectElement
  }
  class SingleChildRenderObjectWidget {
    <<abstract>>
    + Widget? child
    + createElement() SingleChildRenderObjectElement
  }
  class MultiChildRenderObjectWidget {
    <<abstract>>
    + List~Widget~ children
    + createElement() MultiChildRenderObjectElement
  }
  class ErrorWidget {
    + String message
    + createRenderObject(context) RenderBox
  }

  Widget <|-- ProxyWidget
  Widget <|-- StatelessWidget
  Widget <|-- StatefulWidget
  Widget <|-- RenderObjectWidget
  ProxyWidget <|-- ParentDataWidget
  ProxyWidget <|-- InheritedWidget
  RenderObjectWidget <|-- LeafRenderObjectWidget
  RenderObjectWidget <|-- SingleChildRenderObjectWidget
  RenderObjectWidget <|-- MultiChildRenderObjectWidget
  LeafRenderObjectWidget <|-- ErrorWidget
  StatefulWidget ..> State : creates
```

**Element**

```mermaid
classDiagram
  direction LR

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
    + Widget widget
    + BuildOwner? owner
    + bool mounted
    + Size? size
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
    + Element? renderObjectAttachingChild 
    + mount(parent, newSlot) void
    + performRebuild() void
    # build() Widget *
    + visitChildren(visitor) void
    + forgetChild(child) void
  }
  class StatelessElement {
    + build() Widget
    + update(newWidget) void
  }
  class StatefulElement {
    + State~StatefulWidget~ state
    + build() Widget
    + reassemble() void
    + performRebuild() void
    + update(newWidget) void
    + activate() void
    + deactivate() void
    + unmount() void
    + didChangeDependencies() void
  }
  class ProxyElement {
    <<abstract>>
    + build() Widget
    + update(newWidget) void
    # updated(oldWidget) void
    # notifyClients(oldWidget) void *
  }
  class ParentDataElement~T extends ParentData~ {
    + applyWidgetOutOfTurn(newWidget) void
    + notifyClients(oldWidget) void
  }
  class InheritedElement {
    # getDependencies(dependent) Object?
    # setDependencies(dependent, value) void
    # updateDependencies(dependent, aspect) void
    # notifyDependent(oldWidget, dependent) void
    # removeDependent(Element dependent) void
    + updated(oldWidget) void
    + notifyClients(oldWidget) void
  }
  class RenderObjectElement {
    <<abstract>>
    + RenderObject renderObject
    + Element? renderObjectAttachingChild
    + mount(parent, newSlot) void
    + update(newWidget) void
    + performRebuild() void
    + deactivate() void
    + unmount() void
    + updateSlot(newSlot) void
    + attachRenderObject(newSlot) void
    + detachRenderObject() void
    # insertRenderObjectChild(child, slot) void *
    # moveRenderObjectChild(child, oldSlot, newSlot) void *
    # removeRenderObjectChild(child, slot) void *
  }
  class LeafRenderObjectElement {
    + forgetChild(child) void
    + insertRenderObjectChild(child, slot) void
    + moveRenderObjectChild(child, oldSlot, newSlot) void
    + removeRenderObjectChild(child, slot) void
  }
  class SingleChildRenderObjectElement {
    + visitChildren(visitor) void
    + forgetChild(child) void
    + mount(parent, newSlot) void
    + update(newWidget) void
    + insertRenderObjectChild(child, slot) void
    + moveRenderObjectChild(child, oldSlot, newSlot) void
    + removeRenderObjectChild(child, slot) void
  }
  class MultiChildRenderObjectElement {
    + ContainerRenderObjectMixin renderObject
    # Iterable~Element~ children
    + insertRenderObjectChild(child, slot) void
    + moveRenderObjectChild(child, oldSlot, newSlot) void
    + removeRenderObjectChild(child, slot) void
    + visitChildren(visitor)
    + forgetChild(child)
    + inflateWidget(newWidget, newSlot) Element
    + mount(parent, newSlot)
    + update(newWidget)
  }
  class RenderTreeRootElement {
    <<abstract>>
    + attachRenderObject(newSlot) void
    + detachRenderObject() void
    + updateSlot(newSlot) void
  }

  BuildContext <|.. Element
  Element <|-- ComponentElement
  Element <|-- RenderObjectElement
  ComponentElement <|-- StatelessElement
  ComponentElement <|-- StatefulElement
  ComponentElement <|-- ProxyElement
  ProxyElement <|-- ParentDataElement
  ProxyElement <|-- InheritedElement
  RenderObjectElement <|-- LeafRenderObjectElement
  RenderObjectElement <|-- SingleChildRenderObjectElement
  RenderObjectElement <|-- MultiChildRenderObjectElement
  RenderObjectElement <|-- RenderTreeRootElement
```

## Tree

```mermaid
classDiagram 
  class Widget {
    <<abstract>>
    + Key? key
    # createElement() Element *
    + canUpdate(oldWidget, newWidget) bool $
  }
```

### Element Tree

```dart
abstract class Element {
  Element? _parent;
}
```

### Widget Tree

```dart
abstract class Element {
  Element(Widget widget) : _widget = widget;

  @override
  Widget get widget => _widget!;
  Widget? _widget;

  @mustCallSuper
  void update(covariant Widget newWidget) {
    _widget = newWidget;
  }

  @mustCallSuper
  void unmount() {
    _widget = null;
  }
}
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
class InheritedElement {
  @override
  void _updateInheritance() {
    final incomingWidgets =
        _parent?._inheritedElements ?? const PersistentHashMap<Type, InheritedElement>.empty();
    _inheritedElements = incomingWidgets.put(widget.runtimeType, this);
  }
}
```

### Element

```mermaid
classDiagram
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
    + Widget widget
    + BuildOwner? owner
    + bool mounted
    + Size? size
    - PersistentHashMap~Type, InheritedElement~? _inheritedElements
    - Set~InheritedElement~? _dependencies
    - _ElementLifecycle _lifecycleState
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
    - _updateInheritance() void
    + didChangeDependencies() void
    + markNeedsBuild() void
    + rebuild(force) void
    # performRebuild() void
    + findRenderObject() RenderObject?
    + dependOnInheritedElement(ancestor, aspect) InheritedWidget
    + dependOnInheritedWidgetOfExactType~T~(aspect) T?
    + getInheritedWidgetOfExactType~T~()  T?
    + getElementForInheritedWidgetOfExactType~T~() InheritedElement?
    + findAncestorWidgetOfExactType~T~() T?
    + findAncestorStateOfType~T~() T?
    + findRootAncestorStateOfType~T~() T?
    + findAncestorRenderObjectOfType~T~() T?
    + visitAncestorElements(visitor) void
    + visitChildElements(visitor) void
    + dispatchNotification(notification) void
  }

  BuildContext <|.. Element
```


```mermaid
---
title: reassemble
---
flowchart LR
  start([Start])
  reassemble["reassemble()"]
  markNeedsBuild["markNeedsBuild()"]
  visitChildren["visitChildren((child) => child.reassemble())"]
  stop([Stop])
  start --> reassemble --> markNeedsBuild --> visitChildren --> stop
```

```mermaid
---
title: didChangeDependencies
---
flowchart LR
  start([Start])
  didChangeDependencies["didChangeDependencies()"]
  markNeedsBuild["markNeedsBuild()"]
  stop([Stop])
  start --> didChangeDependencies --> markNeedsBuild --> stop
```


```mermaid
---
title: markNeedsBuild
---
flowchart LR
  start([Start])
  markNeedsBuild["markNeedsBuild()"]
  active{active?}
  dirty{dirty?}
  assign["dirty = true"]
  scheduleBuildFor["owner.scheduleBuildFor(this)"]
  stop([Stop])
  start --> markNeedsBuild --> active -->|YES| dirty --> |NO| assign --> scheduleBuildFor --> stop
  active -->|NO| stop
  dirty -->|YES| stop
```


```mermaid
---
title: rebuild
---
flowchart LR
  start([Start])
  rebuild["rebuild(force)"]
  active{active?}
  force{force?}
  dirty{dirty?}
  performRebuild["performRebuild()"]
  stop([Stop])
  start --> rebuild --> active -->|YES| dirty -->|YES| performRebuild --> stop
  active -->|NO| stop
  dirty -->|NO| force -->|YES| performRebuild
  force -->|NO| stop
```

```mermaid
---
title: performRebuild
---
flowchart LR
  start([Start])
  performRebuild["performRebuild()"]
  assign["dirty = false"]
  stop([Stop])
  start --> performRebuild --> assign --> stop
```

```mermaid
flowchart
  p["performRebuild()"] --> build["build()"] --> a["super.performRebuild()"] --> ee["updateChild(_child, built, slot)"]
```

## StatelessWidget

```mermaid
classDiagram
  class Widget {
    <<abstract>>
    + Key? key
    # createElement() Element *
  }
  class StatelessWidget {
    <<abstract>>
    + createElement() StatelessElement
    # build(context) Widget *
  }
  class BuildContext {
    <<abstract>>
    + Widget widget *
  }
  class Element {
    <<abstract>>
    + Widget widget
  }
  class ComponentElement {
    <<abstract>>
    # build() Widget *
  }
  class StatelessElement {
    + build() Widget
    + update(newWidget) void
  }

  Widget <|-- StatelessWidget
  BuildContext <|.. Element
  Element <|-- ComponentElement
  ComponentElement <|-- StatelessElement
  StatelessElement --> StatelessWidget : config
```

## StatefulWidget

```mermaid
classDiagram
  class Widget {
    <<abstract>>
    + Key? key
    # createElement() Element *
  }
  class StatefulWidget {
    <<abstract>>
    + createElement() StatelessElement
    # createState() State *
  }
  class State~T extends StatefulWidget~ {
    <<abstract>>
    + T widget
    + BuildContext context
    - StatefulElement _element
    + bool mounted
    # initState() void
    + didUpdateWidget(oldWidget) void
    # reassemble() void
    # setState(fn) void
    # deactivate() void
    # activate() void
    # dispose() void
    # build(context) Widget *
    # didChangeDependencies() void
  }
  class BuildContext {
    <<abstract>>
    + Widget widget *
  }
  class Element {
    <<abstract>>
    + Widget widget
  }
  class ComponentElement {
    <<abstract>>
    # build() Widget *
  }
  class StatefulElement {
    + State~StatefulWidget~ state
    + build() Widget
    + reassemble() void
    + performRebuild() void
    + update(newWidget) void
    + activate() void
    + deactivate() void
    + unmount() void
    + didChangeDependencies() void
  }

  Widget <|-- StatefulWidget
  BuildContext <|.. Element
  Element <|-- ComponentElement
  ComponentElement <|-- StatefulElement
  StatefulElement --> StatefulWidget : config
  StatefulElement *-- State : owns
```

## ParentDataWidget


## InheritedWidget

```mermaid
classDiagram
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
  }
  class Element {
    <<abstract>>
    + Widget widget
  }
  class ComponentElement {
    <<abstract>>
    - Element? _child
    + Element? renderObjectAttachingChild 
    + mount(parent, newSlot) void
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
  InheritedElement --> InheritedWidget : config
```




### Element Lifecycle

```dart
enum _ElementLifecycle {
  initial,
  active,
  inactive,
  defunct,
}
```

```dart
abstract class Element {
  _ElementLifecycle _lifecycleState = _ElementLifecycle.initial;
  
  @mustCallSuper
  void mount(Element? parent, Object? newSlot) {
    _lifecycleState = _ElementLifecycle.active;
  }

  @mustCallSuper
  void activate() {
    _lifecycleState = _ElementLifecycle.active;
  }

  @mustCallSuper
  void deactivate() {
    _lifecycleState = _ElementLifecycle.inactive;
  }
  
  @mustCallSuper
  void unmount() {
    _lifecycleState = _ElementLifecycle.defunct;
  }
}
```

## InheritedElement Dependencies

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



## LeafRenderObjectWidget

## SingleChildRenderObjectWidget

## MultiChildRenderObjectWidget

## InheritedModel
