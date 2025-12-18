# Dio

## Usages

### Dio

```dart
final Dio dio = Dio();
```

### Interceptor

```dart
dio.interceptors.add(LogInterceptor());
```

### Request

```dart
final Response response = await dio.request('url');
```

## Blueprint

```mermaid
classDiagram
  class Dio {
    <<abstract>>
    + BaseOptions options
    + Interceptors interceptors *
    + HttpClientAdapter httpClientAdapter
    + Transformer transformer
    + close(force) void *
    + request~T~(path) Future~Response~T~~ *
    + fetch~T~(requestOptions) Future~Response~T~~ *
    + clone(options,interceptors,httpClientAdapter,transformer) Dio *
  }
  class DioMixin {
    <<abstract>>
    + BaseOptions options
    + Interceptors interceptors
    + HttpClientAdapter httpClientAdapter
    + Transformer transformer
    + close(force) void
    + request~T~(path) Future~Response~T~~
    + fetch~T~(requestOptions) Future~Response~T~~
    + clone(options,interceptors,httpClientAdapter,transformer) Dio 
  }
  class DioForNative {
    + download() Future~Response~
  }
  class HttpClientAdapter {
    <<abstract>>
    + fetch(options,requestStream,cancelFuture) Future~ResponseBody~ *
    + close(force) void *
  }
  class IOHttpClientAdapter {
    + fetch(options,requestStream,cancelFuture) Future~ResponseBody~
    + close(force) void
  }

  Dio <|.. DioMixin
  Dio <|.. DioForNative
  DioMixin <|.. DioForNative : mixin
  HttpClientAdapter <|.. IOHttpClientAdapter
  DioForNative *-- IOHttpClientAdapter
```

## Principle
