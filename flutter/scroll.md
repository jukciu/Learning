# Scroll

- `Scrollable`
- ScrollView
  - `CustomScrollView`
  - BoxScrollView
    - `ListView`
    - `GridView`
- `SingleChildScrollView`
- `PageView`
- `ReorderableListView`
- `ListWheelScrollView`
- `NestedScrollView`
- `DraggableScrollableSheet`
- `InteractiveViewer`

```mermaid
classDiagram
  direction LR
  class Widget {
    <<abstract>>
    # createElement() Element
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
  class Scrollable {
    + createState() ScrollableState
  }
  class ScrollView {
    <<abstract>>
    # buildSlivers(context) List~Widget~ *
    + build(context) Widget
  }
  class CustomScrollView {
    + buildSlivers(context) List~Widget~
  }
  class BoxScrollView {
    <<abstract>>
    + buildSlivers(context) List~Widget~
    # buildChildLayout(context) Widget *
  }
  class ListView {
    + buildChildLayout(context) Widget
  }
  class GridView {
    + buildChildLayout(context) Widget
  }
  class SingleChildScrollView {
    + build(context) Widget
  }
  class PageView {
    + createState() State~PageView~
  }
  class ListWheelScrollView {
    + createState() State~ListWheelScrollView~
  }
  class ReorderableListView {
    + createState() State~ReorderableListView~
  }
  class NestedScrollView {
    + createState() NestedScrollViewState
  }
  class DraggableScrollableSheet {
    + createState() State~DraggableScrollableSheet~
  }
  class InteractiveViewer {
    + createState() State~InteractiveViewer~
  }

  Widget <|-- StatelessWidget
  Widget <|-- StatefulWidget
  StatelessWidget <|-- ScrollView
  StatelessWidget <|-- SingleChildScrollView
  ScrollView <|-- CustomScrollView
  ScrollView <|-- BoxScrollView
  BoxScrollView <|-- ListView
  BoxScrollView <|-- GridView
  StatefulWidget <|-- Scrollable
  StatefulWidget <|-- PageView
  StatefulWidget <|-- ListWheelScrollView
  StatefulWidget <|-- ReorderableListView
  StatefulWidget <|-- NestedScrollView
  StatefulWidget <|-- DraggableScrollableSheet
  StatefulWidget <|-- InteractiveViewer
```

## Scrollable

```mermaid
classDiagram
  direction LR
  class Scrollable {
    + ViewportBuilder viewportBuilder
    + createState() ScrollableState
  }
  class ScrollableState {
    + build(context) Widget
  }

  Scrollable ..> ScrollableState
```

### ScrollView

```mermaid
classDiagram
  class ScrollView {
    <<abstract>>
    + bool shrinkWrap
    # buildSlivers(context) List~Widget~ *
    + build(context) Widget
  }
  class Scrollable {
    + ViewportBuilder viewportBuilder
  }
  class Viewport {
    + List~Widget~ slivers
  }
  class ShrinkWrappingViewport {
    + List~Widget~ slivers
  }

  ScrollView ..> Scrollable
  Scrollable ..> Viewport : shrinkWrap = false
  Scrollable ..> ShrinkWrappingViewport : shrinkWrap = true
```

示意代码:

```dart
abstract class ScrollView extends StatelessWidget {
  List<Widget> buildSlivers(BuildContext context);

  Widget build(BuildContext context) {
    final List<Widget> slivers = buildSlivers(context);
    final Scrollable scrollable = Scrollable(
      viewportBuilder: (context, offset) {
        return Viewport(slivers: slivers);
      }
    );
    return scrollable;
  }
}
```

### CustomScrollView

```mermaid
classDiagram
  class CustomScrollView {
    + List~Widget~ slivers
    + buildSlivers(context) List~Widget~
  }
```

示意代码:

```dart
class CustomScrollView extends ScrollView {
  final List<Widget> slivers;

  List<Widget> buildSlivers(BuildContext context) => slivers;
}
```

> `Widget`s in these `slivers` must produce `RenderSliver` objects.

### BoxScrollView

```mermaid
classDiagram
  class BoxScrollView {
    <<abstract>>
    + EdgeInsetsGeometry? padding
    + buildSlivers(context) List~Widget~
    # buildChildLayout(context) Widget *
  }
  class SliverPadding {
    + EdgeInsetsGeometry padding
    + Widget? sliver
  }

  BoxScrollView ..> SliverPadding
```

示意代码:

```dart
abstract class BoxScrollView extends ScrollView {
  final EdgeInsetsGeometry? padding;

  List<Widget> buildSlivers(BuildContext context) {
    Widget sliver = buildChildLayout(context);
    sliver = SliverPadding(padding: padding, sliver: sliver);
    return <Widget>[sliver];
  }

  Widget buildChildLayout(BuildContext context);
}
```

### ListView

```mermaid
classDiagram
  class ListView {
    + double? itemExtent
    + ItemExtentBuilder? itemExtentBuilder
    + Widget? prototypeItem
    + SliverChildDelegate childrenDelegate
    + buildChildLayout(context) Widget
  }
  class SliverFixedExtentList {
    + SliverChildDelegate delegate
    + double itemExtent
  }
  class SliverVariedExtentList {
    + SliverChildDelegate delegate
    + ItemExtentBuilder itemExtentBuilder
  }
  class SliverPrototypeExtentList {
    + SliverChildDelegate delegate
    + Widget prototypeItem
  }
  class SliverList {
    + SliverChildDelegate delegate
  }

  ListView ..> SliverFixedExtentList : itemExtent != null
  ListView ..> SliverVariedExtentList : itemExtentBuilder != null
  ListView ..> SliverPrototypeExtentList : prototypeItem != null
  ListView ..> SliverList : default
```

示意代码:

```dart
class ListView extends BoxScrollView {
  final SliverChildDelegate childrenDelegate;

  Widget buildChildLayout(BuildContext context) {
    return SliverList(delegate: childrenDelegate);
  }
}
```

| init               | childrenDelegate           |
| :----------------- | :------------------------- |
| ListView           | SliverChildListDelegate    |
| ListView.builder   | SliverChildBuilderDelegate |
| ListView.separated | SliverChildBuilderDelegate |
| ListView.custom    | o                          |

### GridView

```mermaid
classDiagram
  class GridView {
    + SliverGridDelegate gridDelegate
    + SliverChildDelegate childrenDelegate
    + buildChildLayout(context) Widget
  }
  class SliverGrid {
    + SliverChildDelegate delegate
    + SliverGridDelegate gridDelegate
  }

  GridView ..> SliverGrid
```

示意代码:

```dart
class GridView extends BoxScrollView {
  final SliverGridDelegate gridDelegate;
  final SliverChildDelegate childrenDelegate;

  Widget buildChildLayout(BuildContext context) {
    return SliverGrid(delegate: childrenDelegate, gridDelegate: gridDelegate);
  }
}
```

| init             | childrenDelegate           | gridDelegate                              |
| :--------------- | :------------------------- | :---------------------------------------- |
| GridView         | SliverChildListDelegate    | o                                         |
| GridView.builder | SliverChildBuilderDelegate | o                                         |
| GridView.custom  | o                          | o                                         |
| GridView.count   | SliverChildListDelegate    | SliverGridDelegateWithFixedCrossAxisCount |
| GridView.extent  | SliverChildListDelegate    | SliverGridDelegateWithMaxCrossAxisExtent  |

### SingleChildScrollView

```mermaid
classDiagram
  class SingleChildScrollView {
    + Widget? child
    + build(context) Widget
  }
  class Scrollable {
    + ViewportBuilder viewportBuilder
  }
  class _SingleChildViewport {
    + Widget? child
  }

  SingleChildScrollView ..> Scrollable
  Scrollable ..> _SingleChildViewport
```

示意代码:

```dart
class SingleChildScrollView extends StatelessWidget {
  final Widget? child;

  Widget build(BuildContext context) {
    Widget scrollable = Scrollable(
      viewportBuilder: (context, offset) {
        return _SingleChildViewport(child: child);
      }
    );
    return scrollable;
  }
}
```

### PageView

```mermaid
classDiagram
  class PageView {
    + SliverChildDelegate childrenDelegate
    + createState() State~PageView~
  }
  class _PageViewState {
    + build(context) Widget
  }
  class Scrollable {
    + ViewportBuilder viewportBuilder
  }
  class Viewport {
    + List~Widget~ slivers
  }
  class SliverFillViewport {
    + SliverChildDelegate delegate
  }

  PageView ..> _PageViewState : creates
  _PageViewState ..> Scrollable
  Scrollable ..> Viewport
  Viewport ..> SliverFillViewport
```

示意代码:

```dart
class PageView extends StatefulWidget {
  final SliverChildDelegate childrenDelegate;

  State<PageView> createState() => _PageViewState();
}

class _PageViewState extends State<PageView> {
  Widget build(BuildContext context) {
    return Scrollable(
      viewportBuilder: (context, offset) {
        return Viewport(slivers: <Widget>[
          SliverFillViewport(delegate: widget.childrenDelegate),
        ]);
      }
    );
  }
}
```

| init             | childrenDelegate           |
| :--------------- | :------------------------- |
| PageView         | SliverChildListDelegate    |
| PageView.builder | SliverChildBuilderDelegate |
| PageView.custom  | o                          |

### ReorderableListView

```mermaid
classDiagram
  class ReorderableListView {
    + IndexedWidgetBuilder itemBuilder
    + Widget? header
    + Widget? footer
    + createState() State~ReorderableListView~
  }
  class _ReorderableListViewState {
    + build(context) Widget
  }
  class CustomScrollView {
    + List~Widget~ slivers
  }
  class SliverToBoxAdapter {
    + Widget? child
  }
  class SliverReorderableList {
    + IndexedWidgetBuilder itemBuilder
  }

  ReorderableListView ..> _ReorderableListViewState : creates
  _ReorderableListViewState ..> CustomScrollView
  CustomScrollView ..> SliverToBoxAdapter
  CustomScrollView ..> SliverReorderableList
```

示意代码:

```dart
class ReorderableListView extends StatefulWidget {
  final IndexedWidgetBuilder itemBuilder;
  final Widget? header;
  final Widget? footer;

  State<ReorderableListView> createState() => _ReorderableListViewState();
}

class _ReorderableListViewState extends State<ReorderableListView> {
  Widget build(BuildContext context) {
    return CustomScrollView(
      slivers: <Widget>[
        SliverToBoxAdapter(child: widget.header),
        SliverReorderableList(itemBuilder: widget.itemBuilder),
        SliverToBoxAdapter(child: widget.footer),
      ],
    );
  }
}
```

### ListWheelScrollView

```mermaid
classDiagram
  class ListWheelScrollView {
    + ListWheelChildDelegate childDelegate
    + createState() State~ListWheelScrollView~
  }
  class _ListWheelScrollViewState {
    + build(context) Widget
  }
  class Scrollable {
    + ViewportBuilder viewportBuilder
  }
  class _FixedExtentScrollable {}
  class ListWheelViewport {
    + ListWheelChildDelegate childDelegate
  }

  Scrollable <|-- _FixedExtentScrollable
  ListWheelScrollView ..> _ListWheelScrollViewState : creates
  _ListWheelScrollViewState ..> _FixedExtentScrollable
  _FixedExtentScrollable ..> ListWheelViewport
```

示意代码:

```dart
class ListWheelScrollView extends StatefulWidget {
  final ListWheelChildDelegate childDelegate;

  State<ListWheelScrollView> createState() => _ListWheelScrollViewState();
}

class _ListWheelScrollViewState extends State<ListWheelScrollView> {
  Widget build(BuildContext context) {
    return _FixedExtentScrollable(
      viewportBuilder: (context, offset) {
        return ListWheelViewport(childDelegate: widget.childDelegate);
      }
    );
  }
}
```

### NestedScrollView

```mermaid
classDiagram
  class NestedScrollView {
    + NestedScrollViewHeaderSliversBuilder headerSliverBuilder
    + Widget body
    + createState() NestedScrollViewState
  }
  class NestedScrollViewState {
    - _NestedScrollCoordinator? coordinator
    + build(context) Widget
  }
  class CustomScrollView {
    + List<Widget> slivers
  }
  class _NestedScrollViewCustomScrollView {
  }
  class SliverFillRemaining {
    + Widget? child
  }
  
  CustomScrollView <|-- _NestedScrollViewCustomScrollView
  NestedScrollView ..> NestedScrollViewState : creates
  NestedScrollViewState ..> _NestedScrollViewCustomScrollView
  _NestedScrollViewCustomScrollView ..> SliverFillRemaining
```

示意代码:

```dart
class NestedScrollView extends StatefulWidget {
  final NestedScrollViewHeaderSliversBuilder headerSliverBuilder;
  final Widget body;

  NestedScrollViewState createState() => NestedScrollViewState();
}

class NestedScrollViewState extends State<NestedScrollView> {
  _NestedScrollCoordinator? _coordinator;

  Widget build(BuildContext context) {
    return _NestedScrollViewCustomScrollView(
      slivers: <Widget>[
        ...widget.headerSliverBuilder(context, _coordinator.hasScrolledBody),
        SliverFillRemaining(child, widget.body),
      ],
    );
  }
}
```

## Others

### DraggableScrollableSheet

```mermaid
classDiagram
  class DraggableScrollableSheet {
    + createState() State~DraggableScrollableSheet~
  }
  class _DraggableScrollableSheetState {
    + build(context) Widget
  }

  DraggableScrollableSheet ..> _DraggableScrollableSheetState : creates
```

示意代码:

```dart
class DraggableScrollableSheet extends StatefulWidget {
  State<DraggableScrollableSheet> createState() => _DraggableScrollableSheetState();
}

class _DraggableScrollableSheetState extends State<DraggableScrollableSheet> {
  Widget build(BuildContext context) {
  }
}
```

### InteractiveViewer

```mermaid
classDiagram
  class InteractiveViewer {
    + createState() State~InteractiveViewer~
  }
  class _InteractiveViewerState {
    + build(context) Widget
  }

  InteractiveViewer ..> _InteractiveViewerState : creates
```

示意代码:

```dart
class InteractiveViewer extends StatefulWidget {
  State<InteractiveViewer> createState() => _InteractiveViewerState();
}

class _InteractiveViewerState extends State<InteractiveViewer> with TickerProviderStateMixin {
  Widget build(BuildContext context) {
  }
}
```
