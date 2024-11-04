# ZIG_Static_Lib_Exp

This repo will show an example on  
- Adding a local dependency on a C-only static library
- Adding a local dependency on a C-only "object library" in the style of CMake's object library
- Consider also that even if not provided for a transitive property we can have a  Local dependency on mixed Zig/C code that exposes a zig "module"
- Local dependency on mixed Zig/C code that exposes a zig "module" this examplke could also be extended to add a static library of mixed C/Zig and import it in a C project built using the zig build system, (we need to do a easy c build for **MCU**).

## In the end the important thing to note is that we can import a Zig Module of mixed C/Zig in a C/Zig project as a Zig Module the only constraint is to use the Zig Build System (we Need to master it)

### Other notes and example

1) Static Library
Demonstrates creating a dependency on a static library consisting of C code/headers.

This example, once added as a dependency in a top level package's `build.zig.zon`, would be added as follows to `build.zig`:
```zig
const static_lib = b.dependency("staticlib", .{ .target = target, .optimize = optimize }).artifact("staticlib");
exe.linkLibrary(static_lib);
```

Note that due to an installation path of "staticlib" being specified in the call to `installHeadersDirectory()`, headers referenced in the consuming package are referenced via:
```c
#include "staticlib/[library header].h"
```

2) "Object Library"

Demonstrates creating a dependency in the style of CMake's [object library](https://cmake.org/cmake/help/latest/command/add_library.html#object-libraries). This is useful if you would like to depend on a collection of C sources/headers, but would *not* like to compile these into a static/shared library. This is a relatively niche use-case, but is useful for specific cases where compiling sources into library may produced undesired behavior. For instance, an embedded vendor's HAL code that defines both weak *and* strong symbols in source code, creating un-intuitive behavior for symbol resolution:
https://community.st.com/t5/stm32cubemx-mcus/solved-build-hal-as-static-library-systick-handler-removed-from/td-p/594624

This example, once added as a dependency in a top level package's `build.zig.zon`, would be added as follows to `build.zig`:
```zig
const object_lib = b.dependency("objlib", .{ .target = target, .optimize = optimize }).artifact("objlib");
exe.addObject(object_lib);
```

This produces the behavior of adding sources/headers to the compilation of `exe`, rather than first compiling sources contained within `object_library` to a static library + linking into `exe`.
