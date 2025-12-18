# Dart

## 关键字

- `class`
- `mixin`

### 类型组合关键字

- `extends`
- `with`
- `implements`

```dart
class B
  extends A
  with M1, M2
  implements I1, I2 {
}
```

> M2 优先级高于 M1

- `on`

```dart
mixin M on A {
}
```

```dart
extension B on A {
}
```

```dart
try {
} on IOException catch (e) {
}
```

### 类修饰符

- `abstract`
- `base`
- `final`
- `interface`
- `sealed`
- `mixin`
