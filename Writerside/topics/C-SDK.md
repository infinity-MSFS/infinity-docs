# C++ SDK

## Infinity C++ SDK
The [Infinity C++ SDK](https://github.com/infinity-MSFS/infinity-cpp-sdk) provides developers with consistent abstractions and utilities that align with those found in Rust.

## C++ 14 Features
### Option
```c++
    using namespace Infinity;
    Option<int> o = Some(5);
    o.map([](int x){ return x * 2; });
    o.and_then([](int x){ return Some(x + 1); });
    o.unwrap_or(42);
```

### Result
```C++
    using namespace Infinity;
    auto r = Result<int, std::string>::ok(7);
    r.map([](int x){ return x + 1; });
    r.map_err([](const std::string& e){ return e + "!"; });
    r.and_then([](int x){ return Result<int, std::string>::ok(x * 2); });
```

### Configuration
```C++
#define INFINITY_LANGUAGE_STD_CPP14 // Specify C++14 standard
#define INFINITY_NO_EXCEPTIONS   // Disable exceptions for use in WASM
```
--------

## C++ 23 Features
### Option {id="option_1"}
```c++
    using namespace Infinity;
    Option<int> o = Some(5);
    if (o.is_some()) {
      int v = o.unwrap();
    }
    auto m = o.map([](int x){ return x * 2; });
    int d = Option<int>{None}.unwrap_or(42);
```

### Result {id="result_1"}

```C++
      using namespace Infinity;
      Result<int, std::string> r(123);
      if (r.is_ok()) {
        int v = r.unwrap();
      }
      auto rm = r.map([](int x){ return x + 1; });
    
      Result<int, std::string> e(std::string("err"));
      if (e.is_err()) { 
        std::string s = e.unwrap_err();
      }
```

### collect_results
```c++
  using namespace Infinity;
  std::vector<Result<int, std::string>> v = 
    { Result<int, std::string>(1), Result<int, std::string>(2) };
  auto cr = collect_results<int, std::string>(v); // Ok(vector<int>)
```

### Match 
```C++
  using namespace Infinity;
  MATCH(o, {
    SomeCase(v) { /* use v */ break; }
    NoneCase { /* none */ break; }
  });
  MATCH(r, {
    OkCase(v) { /* ok */ break; }
    ErrCase(e) { /* err */ break; }
  });
```

### Vec
```C++
  using namespace Infinity;
  auto v1 = vec_of(1,2,3);
  auto v2 = vec_repeat(std::string("x"), 3);
```