# C++

This document outlines the coding standards and best practices for C++ development in our projects.

## Naming Conventions

### Classes and Structs
- Use **PascalCase** for class and struct names
- Names should be descriptive and represent the purpose

```cpp
class DataProcessor {
    // Implementation
};

struct UserProfile {
    std::string name;
    int age;
};
```

### Functions and Methods
- Use **camelCase** for function and method names
- Function names should be verbs or verb phrases

```cpp
void processData();
int calculateTotal(int a, int b);
bool isValid() const;
```

### Variables
- Use **camelCase** for local variables
- Use **snake_case** with prefix `m_` for member variables
- Use **ALL_CAPS** for constants

```cpp
int itemCount = 0;
std::string userName;

class Example {
private:
    int m_member_variable;
    std::string m_user_name;
};

const int MAX_BUFFER_SIZE = 1024;
```

## Infinity C++ SDK
Infinity provides a header-only [C++ library](https://github.com/infinity-MSFS/infinity-cpp-sdk) that allows developers to leverage many of the error handling abstractions that Rust offers. see the [Infinity C++ SDK documentation](C-SDK.md) for more details.



## Modern C++ Practices

### Use Smart Pointers
Prefer smart pointers over raw pointers to manage memory automatically.

```cpp
#include <memory>

// Prefer unique_ptr for exclusive ownership
std::unique_ptr<DataProcessor> processor = std::make_unique<DataProcessor>();

// Use shared_ptr when ownership needs to be shared
std::shared_ptr<Resource> resource = std::make_shared<Resource>();
```

### Use `auto` Keyword
Use `auto` for type deduction when the type is obvious or overly verbose.

```cpp
auto count = 42;  // int
auto name = std::string("John");
auto it = container.begin();
```

### Range-Based For Loops
Prefer range-based for loops for iterating over containers.

```cpp
std::vector<int> numbers = {1, 2, 3, 4, 5};

// Good
for (const auto& num : numbers) {
    std::cout << num << std::endl;
}

// Use & for references, const for read-only
for (auto& item : modifiableList) {
    item *= 2;
}
```

## Error Handling

### Use Exceptions
Use exceptions for error handling instead of error codes.

```cpp
class FileOpenException : public std::runtime_error {
public:
    explicit FileOpenException(const std::string& filename)
        : std::runtime_error("Failed to open file: " + filename) {}
};

void openFile(const std::string& filename) {
    if (!fileExists(filename)) {
        throw FileOpenException(filename);
    }
    // Open file
}
```

## Code Organization

### Header Guards
Use `#pragma once` or include guards in header files.

```cpp
// MyClass.h
#pragma once

class MyClass {
    // Declaration
};
```

### Include Order
1. Related header file
2. C system headers
3. C++ standard library headers
4. Other libraries' headers
5. Your project's headers

```cpp
#include "MyClass.h"

#include <cstdio>
#include <iostream>
#include <vector>

#include <boost/algorithm/string.hpp>

#include "utils/Helper.h"
```

## Best Practices

- **Use `const` wherever possible** - Make functions and variables const when they don't modify state
- **Avoid global variables** - Use namespaces, classes, or singletons instead
- **Follow RAII** - Resource Acquisition Is Initialization
- **Prefer composition over inheritance**
- **Write self-documenting code** - Use clear names instead of excessive comments
- **Keep functions small** - Each function should do one thing well

## Documentation

Use Doxygen-style comments for public APIs:

```cpp
/**
 * @brief Processes the input data and returns the result
 * 
 * @param input The input data to process
 * @param options Processing options
 * @return Processed result as a string
 * @throws ProcessingException if the input is invalid
 */
std::string processInput(const Data& input, const Options& options);
```

## Formatting
- Follow the published [clang-format](https://raw.githubusercontent.com/infinity-MSFS/InfinityLauncher/refs/heads/master/.clang-format) style for consistent code formatting across the codebase.
- All code must be properly formatted before committing.