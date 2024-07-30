# `json5`
**`json5`** is a small header only C++ library for parsing [JSON](https://en.wikipedia.org/wiki/JSON) or [**JSON5**](https://json5.org/) data. It also comes with a simple reflection system for easy serialization and deserialization of C++ structs.

### Quick Example:
```cpp
#include <json5/json5_input.hpp>
#include <json5/json5_output.hpp>

struct Settings
{
  int x = 0;
  int y = 0;
  int width = 0;
  int height = 0;
  bool fullscreen = false;
  std::string renderer = "";

  JSON5_MEMBERS(x, y, width, height, fullscreen, renderer)
};

Settings s;

// Fill 's' instance from file
json5::FromFile("settings.json", s);

// Save 's' to file
json5::ToFile("settings.json", s);
```

## `json5.hpp`
Provides the basic types of `json5::Document`, `json5::Value`, and `json5::IndependentValue`.  A `Value` represents a value within JSON but it relies on a `Document` for storage of non-primitive (string, array, object) data.  A `Value` may be more difficult to manipulate which is why the `IndependentValue` is provided.  An `IndependentValue` is self-contained and indepenedent of any other object.  It contains a variant which respresents all possible JSON value types.  Parsing is faster with a `Document` than with an `IndependentValue` due to its compact nature and small number of memory allocations.

## `json5_input.hpp`
Provides functions to load `json5::Document` or `json5::IndependentValue` from string, stream or file.

## `json5_output.hpp`
Provides functions to convert `json5::Document` or `json5::IndependentValue` into string, stream or file.

## `json5_builder.hpp`
Defines `Builder`s for building `Document`s and `IndependentValue`s.  Also provides the basis for building arbitrary objects via reflection.

## `json5_reflect.hpp`

### Basic supported types:
- `bool`
- `int`, `float`, `double`
- `std::string`
- `std::vector`, `std::map`, `std::unordered_map`, `std::array`
- `C array`

## `json5_base.hpp`
Contains `Error` definitions, `ValueType` enum, and macro definitions.

## `json5_filter.hpp`

# FAQ
TBD

# Additional examples

### Serialize custom type:
```cpp
// Let's have a 3D vector struct:
struct Vec3
{
  float x, y, z;
};

// Let's have a triangle struct with 'vec3' members
struct Triangle
{
  vec3 a, b, c;
};

JSON5_CLASS(Triangle, a, b, c)

namespace json5::detail {

// Write Vec3 as JSON array of 3 numbers
inline void Write(writer &w, const vec3 &in)
{
  Write(w, std::array<float, 3>{in.x, in.y, in.z});
}

// Read Vec3 from JSON array
inline error Read(const json5::value &in, vec3 &out)
{
  return Read(json5::ArrayView(in), out.x, out.y, out.z);
}

} // namespace json5::detail
```

### Serialize enum:
```cpp
enum class MyEnum
{
  Zero,
  First,
  Second,
  Third
};

// (must be placed in global namespce, requires C++20)
JSON5_ENUM(MyEnum, Zero, First, Second, Third)
```
