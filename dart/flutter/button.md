# Button

```mermaid
classDiagram
    direction LR
    class StatefulWidget {
        <<abstract>>
        #createState() State*
    }
    class State ~T~ {
        <<abstract>>
        +T widget
        #build(BuildContext) Widget*
    }
    class ButtonStyleButton {
        <<abstract>>
        +ButtonStyle? style
        +Widget? child
        #defaultStyleOf(BuildContext) ButtonStyle*
        #themeStyleOf(BuildContext) ButtonStyle?*
        +createState() State~ButtonStyleButton~
    }
    class _ButtonStyleState {
        +ButtonStyleButton widget
        +build(BuildContext) Widget
    }

    StatefulWidget *--> State
    StatefulWidget <|-- ButtonStyleButton
    State <|-- _ButtonStyleState : T = ButtonStyleButton
    ButtonStyleButton *--> _ButtonStyleState

    class ElevatedButton {
        +styleFrom() ButtonStyle$
        +defaultStyleOf(BuildContext) ButtonStyle
        +themeStyleOf(BuildContext) ButtonStyle?
    }
    class FilledButton {
        -_FilledButtonVariant variant
        +styleFrom() ButtonStyle$
        +defaultStyleOf(BuildContext) ButtonStyle
        +themeStyleOf(BuildContext) ButtonStyle?
    }
    class OutlinedButton {
        +styleFrom() ButtonStyle$
        +defaultStyleOf(BuildContext) ButtonStyle
        +themeStyleOf(BuildContext) ButtonStyle?
    }
    class TextButton {
        +styleFrom() ButtonStyle$
        +defaultStyleOf(BuildContext) ButtonStyle
        +themeStyleOf(BuildContext) ButtonStyle?
    }

    ButtonStyleButton <|-- ElevatedButton
    ButtonStyleButton <|-- FilledButton
    ButtonStyleButton <|-- OutlinedButton
    ButtonStyleButton <|-- TextButton
```

## ButtonStyleButton

按钮样式优先级:<br/>
widget.style? > widget.themeStyleOf()? > widget.defaultStyleOf()

文本样式优先级:<br/>
style.textStyle? > Theme.textTheme.bodyMedium!

文本颜色优先级:<br/>
style.foregroundColor? > style.textStyle.color?

图标颜色优先级:<br/>
style.iconColor? > style.foregroundColor? > IconThemeData.color?

边框样式优先级:<br/>
style.side? > style.shape.side

阴影颜色优先级:<br/>
style.shadowColor? > M3: Theme.colorScheme.shadow | M2: Theme.shadowColor

叠加颜色优先级:<br/>
style.overlayColor? > Theme.splashColor

水波纹效果优先级:<br/>
style.splashFactory? > Theme.splashFactory

## ElevatedButton

## FilledButton

## OutlinedButton

## TextButton
