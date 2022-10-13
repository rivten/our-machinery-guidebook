                       **OMG: Our Machinery Guidebook**

The purpose of this guidebook is to lay down principles and guidelines for how to write
code and work together at Our Machinery.

# OMG-META: About this guidebook

## OMG-META-1: Guidelines are identified by unique identifiers

Each guideline in this document is identified by a unique identifier (`OMG-META-1`) as well as
a name (`Guidelines are identified by unique identifiers`). Names may change, but identifiers are
permanent and can be used to permanently and uniquely refer to a specific guideline.

If a guideline is retired, its identifier will be removed and not used again for other purposes.

## OMG-META-2: Prefer to have things in writing

Rules, guidelines, and work processes should be written down instead of being kept in people's
heads. This makes it easier to refer to them, and discuss them and it makes it easier to onboard
new people to the project.

In addition to these guidelines, here are other useful resources:

* [Workflows](https://paper.dropbox.com/doc/Workflows--AU9nAzZP9QkEMPqXLd56sf~ZAg-i7exDDLxypZmlxf2g24gY)

## OMG-META-3: Purpose of the guidelines

The purpose of this guide is to help programmers who are new to *The Machinery* write code in *The
Machinery* style and to answer questions programmers might face about how to design or implement
certain things.

* These guidelines are intended to be a practical help, not to cover every possible situation. We
  want to keep this document succinct, so we avoid discussing minutiae or things that are already
  widely known. If something isn't covered by the guidelines and you are uncertain about the best
  approach, look at the surrounding code and follow its example or talk it through with a colleague.

* This is not intended to be a static document. If we notice a problematic pattern in the code or
  think of new advice that will be helpful, we will extend this document with that information.

* Some of the guidelines in this document have good rationales behind them, others are just
  arbitrary choices, but it's better to pick one alternative and be consistent than to have different
  choices all over the code.

* You may find existing code in the codebase that conflicts with the guidelines. That is usually
  because the code was written before the guideline. Resist the urge to go in and "fix" all that
  code. Such fixes do not add much value by themselves and risk introducing new bugs. Instead, only
  fix the code that needs to be updated for other reasons (bug fixes, new features, etc). Fixing
  code that you are already working on anyway is a lot safer because you already have a good
  understanding of the code which makes you less likely to introduce bugs. Also, since you are
  actively working on the code, chances are that if you introduce a bug you will spot it quickly.

# OMG-DESIGN: Design Principles

## OMG-DESIGN-1: Code must be easy to change

The primary purpose of these guidelines is to design code that is easy to change
and adapt to various situations and circumstances.

Being easy to change is the most important property of a codebase as it is the
basis for everything else we want to do with the code. Code that is easy to
change can adapt to new technical requirements or business goals with ease. Code
that can’t change is doomed to stagnate, become technically irrelevant and then
die (when it is easier to throw everything away and start from scratch than to
keep modifying it).

## OMG-DESIGN-2: Less is more

Code is not an asset, it is a liability. The more code you have, the more you have to optimize,
debug, modernize, refactor, understand. In short, the more code you have — the harder it is to
change. A smaller and simpler codebase is easier to understand, improve and extend.

Strategies for keeping the codebase small:

- Be minimalistic: Implement the tiniest thing that solves the current problem. Don’t expect future
  needs. If the code is flexible you can add the functionality when the need occurs.

- Build more advanced things out of a few simple building blocks. Arrays and hash tables should
  cover almost all of your data type needs.

- Actively prune unused code paths.

- Refactor out unneeded complexity whenever you can. Strive to make the code as small and simple as
  possible.

- Never leave commented out code in the project. That’s what version history is for.

“Less is more” is not just about physical lines of code, it is also about adding other unnecessary
stuff. An external library does not add any code to the repository, but it adds dependencies and
conceptual complexity — more things that you need to understand and more things that can go wrong.

Strategies for keeping conceptual complexity down:

- Don’t introduce unnecessary abstraction layers, prefer simpler concepts (`char *` over
  `std::string`). Only abstract when it brings clear value.

- Don’t add new programming languages to the codebase. We strive to do everything with C, so
  developers only need to learn one language, one debugger, etc.

- Minimize the use of external libraries. Prefer minimalistic one-file libraries over bigger ones.

It can be tempting to rely on an external library as a quick way of implementing a feature. But be
wary. Code in external libraries is hard to modify, which violates our first principle. First, you
have to understand the external codebase, which can be daunting, and then you either have to fork,
which means you take over the maintenance burden or deal with the politics of pushing the changes
upstream. The bigger and more complicated the library is, the harder this problem gets.

If you only need a small functionality, consider just implementing that instead of adding a library
dependency.

## OMG-DESIGN-3: Keep it simple

Simpler solutions are easier to understand, easier to reason about, and easier
to modify.

What is “simple” can be discussed. From our perspective it means:

- Fewer levels of abstraction.
- Easier to understand *completely* (performance implications, threading implications, etc).
- Easier to debug.
- More straightforward and easier to follow logic.
- Closer to the metal.

Examples:

- C is simpler than C++.
- `const char *` is simpler than `std::string`.
- `int[]` is simpler than `std::vector`
- Immutable is simpler than mutable.
- Non-template code is simpler than template code.
- Single-threaded is simpler than multi-threaded.
- boost libraries are not simple.

Sometimes complexity is needed. For example, you can’t get good performance on
modern hardware without multi-threading and some mutability. The point is to
keep things as simple *as possible.*

Use a limited set of core data structures and concepts. Use established
standards *when they are simple:*

- Simple standards: JSON, Web sockets, UTF-8.
- Complicated standards: XML, Corba, UTF-16.

## OMG-DESIGN-4: Explicit is better than implicit

It is better when programmers can see what is going on than when it is hidden. Avoid doing cute
tricks with templates and macros. Think about code that is easy to understand and step through in a
debugger.

## OMG-DESIGN-5: Design with performance in mind

The performance of a system is a part of its design. When you design a new
system you should have clear performance goals in mind — how many objects the
system should be able to handle, how many milliseconds it should take to update,
etc. A system that doesn’t live up to its performance goals is not finished.

Remember that the performance of a platform such as *The Machinery* is
significantly different from the performance of an end-user application.
Application software only has to be “fast enough” for the user not to become
annoyed. In contrast, a platform needs to stay performant across a wide range of
use cases, as we cannot predict what the end users will want to do with the
platform.

In other words, performance is a primary feature of the engine and should always
be in the back of your mind. Avoid hidden, unexpected costs — the users should
only pay for the features they use. System update costs should be linear in the
number of active objects. Profile your code so you know where time is being
spent.

## OMG-DESIGN-6: Design for fast iterations

For modifications to be easy, the modify-build-test cycle must be fast.

Minimizing build times ensures that programmers stay productive. This is accomplished by minimizing
header inclusion and avoiding known costly constructs, such as heavy use of C++ templates.

Unit tests should be used as much as possible to verify the correctness of the code. This allows
changes to be made with greater confidence and less manual testing, which means changes can be made
faster. These unit tests should be fast enough to run as a part of the standard build.

Unit tests provide the most value for *complicated low-level code*, such as JSON parsing and
complicated data structures (*The Truth*). *Simple* low-level code (e.g., vec3 addition) is unlikely
to be changed a lot and see regression bugs. And even if it does have bugs, they are usually easy to
spot and fix. Thus, unit tests provide limited value. *High-level* code (e.g., UI) is hard to test
with unit tests and usually better tested in other ways (hands-on or through recorded UI interaction
sessions).

Hot-reloading of DLLs should be supported to provide quick testing of changes in a running
application.

Be mindful of the application's boot time. The boot time is a fixed cost that everyone has to pay
whenever they want to test anything in the application. Faster boot times means faster iterations.

## OMG-DESIGN-7: Deliver changes quickly

The path from implementing a feature to delivering it to the end user should be short.

Regardless of the benefits to end users, there are also many advantages to delivering changes fast
*internally.* When we integrate often and quickly, designs can be validated earlier, bugs can be
discovered sooner and everybody gets on the same page about the current status of the project.

To achieve fast delivery we discourage long-lived branches. Users should be working against the
trunk as much as possible. Instead of using branches, experimental and unfinished features should be
protected by feature flags. Such feature flags can also be used for quick rollback if critical bugs
are discovered.

## OMG-DESIGN-8: Avoid coupling

Avoid complicated dependencies between systems. These make the codebase harder
to understand and modify. It should be possible to modify, optimize or replace
each system on its own.

Use abstract interfaces to access shared services such as logging, file systems,
memory allocation, etc. That way, these systems can be replaced or mocked for
unit tests.

## OMG-DESIGN-9: Everything is a plugin

*The Machinery* is a platform built from a toolbox of collaborating plugins.
Each plugin is defined by a pure C11 API and a common mechanism is provided for
querying for and using these APIs. The plugins are built on a common
minimalistic foundation layer that provides useful utility functions (math,
containers, etc) as well as interfaces for reasoning about core concepts such as
memory, threading, logging, error handling, etc.

A product built on top of *The Machinery* can freely mix and match from existing
plugins to create an adaptable and custom tailored user experience without extra
bloat. It also allows teams to work in isolation on a plugin without having to
worry about breaking things for other teams. Plugins can be shared without the
need for merging code bases.

New functionality should almost always be added as a plugin — be wary about
extending the foundation. Also, take care to minimize the dependencies between
your plugin and other plugins. Each such dependency is a coupling that will, in
the long run, make the code harder to modify.

## OMG-DESIGN-10: Follow Data Oriented Design principles

Design your systems around data layouts and data flows first. Memory throughput
is often the limiting factor of software performance. Make sure your data
layouts and access patterns are cache friendly. Don’t abstract away the nature
of the hardware.

Avoid object-oriented design principles. They encourage heap allocated
individual objects with data hidden behind accessor functions. This leads to bad
data access patterns and code flows that are hard to optimize and multi-thread.
Instead, lay out the data in the most efficient way and then write functions to
operate on that data.

Multi-threaded, job-based parallelism should be considered the norm for any
high-throughput system.

## OMG-DESIGN-11: Take advantage of modern rendering architecture features

All core rendering APIs in The Machinery are architected to run on top of
explicit graphics APIs such as Vulkan or DX12. The graphics API agnostic
building blocks exposed are more minimalistic than what you typically would find
in engines built on top of older generation graphics APIs.

A few examples:

- Instead of exposing a bunch of different domain-specific resource types for
  reasoning about buffers (e.g vertex, index buffers, etc.) we only have two
  types: buffer & image buffer.

- Instead of using a slot based binding model for exposing resources to a
  shader, you bind sets of resources to a resource binder that later gets bound
  to the shader.

- Resource state transitions are the responsibility of the user.

- All APIs are Multi-GPU aware allowing them to broadcast commands to any
  configuration of active devices in a render backend using a device affinity
  bit-mask.

When designing new rendering features it is important to keep this in mind:

- Instead of thinking about traditional concepts such as vertex, index and
  constant buffers think "raw memory".

- Design around update frequency and data flows. Keep shuffling memory over
  PCI-E to a minimum.

- Utilize "bindless" concepts, avoid unnecessary updates of resource binders.

## OMG-DESIGN-12: Shared responsibility

The codebase should be the shared responsibility and pride of every developer
that works on it.

If you see a problem, it is your problem. Don’t wait for somebody else to fix
it. Fixing issues in parts of the code you’re not familiar with is a good way of
understanding more of the codebase. Of course, reach out to the people who know
that part of the code, to help you understand it better, and verify that your
fix is the right one.

When you deliver a feature or a new system, you are responsible for the whole
delivery. Design, clean code, documentation, testing, performance, knowledge
sharing, etc. A feature is not done just because it works. When you go in to
make a change, you should always leave the code in a better state than you found
it: cleaner, simpler, faster, easier to understand.

When you are fixing a bug, don’t just fix the bug. Ask yourself what the
underlying cause was that made the bug slip through compilation, unit tests,
developer tests, and QA. Can you somehow write the code so that bugs like this
can be detected at compile time? Can you add a unit test that detects the bug?
Can you add an assert that prevents misuse of the functions? Can you improve the
function names and/or the documentation to make misuse less likely?

When you are responding to questions, consider adding them to an FAQ or
clarifying the code documentation, so that you won’t have to answer the same
question again.

## OMG-DESIGN-13: Write for readability

Code is read more often than it is written. Write your code so that it is easy to read and
understand for a new programmer (or for yourself coming back to the code later).

Code should aim to be plain and straight forward. Avoid tricky constructs, trying to be clever and
showing off your programming skills.

It is not always 100 % clear cut what is an "unreadable clever hack" and what is a "common
programming idiom". For example, consider this code for setting a bit in an integer:

~~~c
flags |= (1ULL << TM_RENDER_FLAG__CAST_SHADOWS);
~~~

For someone not used to bit twiddling this may seem like a "hack". However, for someone used to bit
twiddling, this is just the standard way of setting a bit and adding an abstraction around it (such
as a `SET_BIT()` macro) is just obfuscating things.

Familiarize yourself with the idioms that are commonly used in *The Machinery* by reading the code.

# OMG-CODEORG: Code organization

## OMG-CODEORG-1: All repository code should be written in C

At Our Machinery, we use C/C++ for everything. This includes things like command-line tools and
documentation generators which would be tempting to write in a scripting language, such as Ruby
or Python.

The reason we are using C/C++ for everything is:

* Anybody can work on any part of the code.
* The same debugger can be used for everything.
* Minimize dependencies and version hell.
* It's a good test run of our C APIs — they should be easy to work with, if not, something is wrong.
* Avoid writing the same code multiple times in different languages.

The only exception to this rule is when a platform requires us to use a specific language. E.g.
Cocoa on OS X requires us to use Objective-C, while Android requires us to use Java. We will minimize
the interface code written in these languages and write the bulk of the code in C/C++.

## OMG-CODEORG-2: Header files (.h) should be written in C11

Header files (`.h`) define public interfaces between modules. All header files
should be strict C11 (not C++).

We use C rather than C++ as our *interface* language for multiple reasons:

* C is a lot simpler than C++. Reading a C header file is easier than reading a C++ header file.
* C has a smaller design space than C++. C++ can be written in lots of different "styles", which
  leads to confusion and fragmentation.
* We want to support developing in C — i.e. it should be possible to write modules entirely in C.
* C has a stable ABI. Our system is plugin-based and using C interfaces allows us to compile DLLs
  without worrying about which compiler version and flags we are using.
* Most scripting languages are easy to bind to a C API. In contrast, when trying to bind to a C++
  API, you can end up with the C++ object model and the scripting language object model fighting
  each other.

To allow header files to be loaded both from C and C++ they should follow the pattern:


~~~c
#pragma once

#ifdef __cpluspus
extern "C" {
#endif

#include "api_types.h"

// ...

#ifdef __cplusplus
}
#endif
~~~

We use `#pragma once` rather than include guards because it is simpler to write and works with all modern compilers.

## OMG-CODEORG-3: Header files can only include `"api_types.h"`

Header files (.h) should not be dependent on other header files and there should be no `#include` statements in header files.

The only exception to this is `"api_types.h"` that may be included in header files in order to
get access to standard API types (`bool`, `uint32_t`, etc).

The reasons for this rule are:

* Better modularity — each header file can be understood in isolation.
* Faster compile times — headers including other headers can create massive amounts of code
  to compile.

Look at some of the `.h` files in the project to see how to write headers in this style. Some of
the techniques used are:

* **Forward declared opaque structs:** (`struct tm_allocator_i;`). This is used to refer to objects
  defined in other modules. Since we only refer to them by pointers, the header file does not
  need the full struct definition.

* **Opaque data blobs:** (`struct tm_file_o { uint8_t opaque[64]; }`). This is used to represent things
  like file handles, that we want to keep on the stack but that use different types on different
  platforms. We use an opaque data blob that is big enough to hold the largest type size and then
  `memcpy()` data between the opaque type and the platform-specific types.

One tricky construct is when you want to implement some form of "inheritance" in C. I.e., something
like this:

a.h:
~~~c
struct foo_base {
    int x;
};
~~~

b.h:
~~~c
struct foo_impl {
    struct foo_base;
    int y;
};
~~~

In this case `b.h` cannot be compiled without the definition in `a.h` and it might be tempting to
put `#include "a.h"` in `b.h`. Instead, you should make sure that you include `a.h` before `b.h` in
any `*.c` file that uses `b.h`. Like this:

c.c:
~~~c
#include "a.h"
#include "b.h"

...
~~~

This might seem overly complicated, but we believe it is preferable to opening the door to header
inclusion. Virtual inheritance should be a rare thing and there are advantages to exposing its
complexity.

To make sure the user gets better error messages if she forgets to include `a.h` you can do
something like this:

a.h:
~~~c
#define FOO_BASE
struct foo_base {
    int x;
};
~~~

b.h:
~~~c
#ifndef FOO_BASE
    #error "You must include \"a.h\" before including \"b.h\"."
#endif
struct foo_impl {
    struct foo_base;
    int y;
};
~~~

## OMG-CODEORG-4: Implementation files (.c) should be written in C, but C++ can be used in rare cases

Interfaces *must* be written in C. Implementations *should* be written in C. It is a smaller and
simpler language and writing the code in C probably makes it easier to understand. Programmers
working in *The Machinery* codebase should not have to learn C++.

However, we acknowledge that there are a few rare cases where the greater expressive power of C++
can simplify the code, or where C++ code is needed to interface with an external C++ based library.
In those few, very rare cases, we allow the implementation to be written in C++.

Currently the code base constists of is 98.9 % C and 1.1 % C++. We feel that this is a good balance.

Note that this rule about prefering C only applies to our *internal* code. Clients and customers
may have many good reasons to prefer other languages (for example consistency with an existing code
base). They can use any language that can interface with our C ABI: C, C++, D, Rust, ML, etc.

## OMG-CODEORG-5: Shared non-API code goes in `.inl` files

Header files (`.h`) are only allowed to contain public C11 interfaces. However, there is often
a need for other kinds of shared code.

One example is internal headers within a large module. These headers are not intended for
public use, just for the different parts of the module to communicate with each other.

For this kind of shared code, that is not a part of the public APIs we use the `.inl` extension,
to keep them physically distinct from the public `.h` files. `.inl` files may include `.h` files
and other `.inl` files but you should use this sparingly in order to avoid increasing compile times.

## OMG-CODEORG-6: Most functionality should be built as plugins

The design of The Machinery is to have a small core (the `foundation`) that can be easily extended
with plugins. Most functionality added to The Machinery should be written as a plugin rather than
as a part of the `foundation` since it gives better modularity.

## OMG-CODEORG-7: File names should be unique

Avoid having files with the same name in different directories. (For example `math/types.h` and
`config/types.h`.) Unique file names are easier to search for.

## OMG-CODEORG-8: Document third-party code

All use of third-party code should be documented in the `third-party-code.md` file.

# OMG-API: API design

## OMG-API-1: Externally visible names should start with `tm_` or `TM_`

This is our namespace prefix and is used to avoid name clashes.

`tm_` should be followed by the system name. So functions related to input should be prefixed
by `tm_input_` while enums related to input are prefixed `TM_INPUT_`.

Static functions and other objects that don't have global visibility don't need to be and shouldn't
be prefixed.

## OMG-API-2: APIs should use explicit width integers

APIs should use `uint16_t`, `uint32_t`, etc rather than `int`, `short`, etc.

We want to always know the exact integer sizes and have them be consistent across platforms.

## OMG-API-3: Unsigned integers are preferred over signed

Signed integers should only be used for values that can be negative. Values that can only be
positive should be represented by unsigned integers.

Be careful when using subtraction operations on unsigned integers -- it is easy to slip up and
underflow the integer into `0xffffffff`, for example with code like this:

~~~c
for (uint32_t i = n-1; i>0; --i)
    // Do stuff
~~~

When you are doing stuff like this, onsider casting to signed integers unless you're 100 % sure the
subtraction won't underflow.

## OMG-API-4: `bool` from `stdbool.h` should be used for boolean types

Using an explicit `bool` type for boolean values is clearer than representing them by an integer
value. You should include `api_types.h` to get access to `bool` and other standard types.

## OMG-API-6: Strings should be UTF-8 encoded

All strings used in APIs should be UTF-8 encoded.

## OMG-API-7: Unchanged pointers should be `const` declared

If an API function takes a pointer and doesn't modify the value pointed to, the pointer should be
declared `const` in the API.

## OMG-API-8: Use 0 to indicate "no value"

API functions that return an integer or a floating point value and want to indicate "no result"
should do so by returning `0` and not some other value, such as `-1` or `0xffffffff`. Using `0`
for "no value" is less ambiguous and makes it easy to test the value with a simple if-statement.

A useful technique is to reserve the first slot in arrays of objects, etc, so that the index
`0` will never be used. That way we can use it for the `null` value.

If you are unable to use `0` to mean "no value" because it is a valid result of the function you
are writing, use a second `bool` return value to indicate whether the value returned is valid
or not.

## OMG-API-9: memset(0) should be a valid data initialization

Aim to design data structures so that `0` represents the default value. This makes it possible to
initialize structs with a simple memory clear or by a `= {0}` assignment.

There are some situations where this is not possible. For example, a `tm_vec3_t` representing scale
has a natural default value of `{1,1,1}` and a `tm_vec4_t` representing a quaternion has a natural
default of `{0,0,0,1}`. Don't try to fight this by adding a weird bias to the values. Just accept
that in these cases we cannot achieve our goal of zero-initialization.

## OMG-API-10: Prefer polling over callbacks

If your system needs to notify other parts of the code when events occur, it should do so through
polling rather than callbacks.

Callbacks often create subtle problems because it is hard to know which state the system is in
when the callback occurs, what it is safe to do from within the callback, what thread the callback
occurs on, etc. Polling does not have these issues.

In a real-time application, there is always an update loop, so polling can be done without incurring
any big overhead.

In cases where you have a lot of objects that might need to be polled, use aggregate polling to
reduce the cost. E.g., to avoid having to poll thousands of individual physics objects to see if
they collided, expose a single aggregated polling function that returns an array of the physics
objects that collided during the last physics update.

## OMG-API-11: Modules are structs with function pointers

The Machinery is built as a number of interacting but independent modules. A module is defined by
its name and its API. The API is a struct that can contain variables and function pointers:

~~~c
#define TM_LOGGER_API_NAME "tm_logger_api"

// The logger registry stores a list of active loggers. You can register
// more loggers to add more backend outputs for log messages.
struct tm_logger_api
{
    // Adds a new logger to the registry.
    void (*add_logger)(tm_logger_i *logger);

    // Removes a previously added logger from the registry.
    void (*remove_logger)(tm_logger_i *logger);

    // Sends a log message to all registered loggers.
    void (*print)(enum tm_log_type log_type, const char *msg);

    // Convenience function for printing a formatted string (using `printf` syntax)
    // to the default logger registry.
    int (*printf)(enum tm_log_type log_type, const char *format, ...);

    // A default logger that will print log messages using `printf(...)`.
    tm_logger_i *printf_logger;
};
~~~
[Sample module.]

The API registry keeps track of all current modules. You can query the registry for a module with
a specific name. The registry will return a `void *` which can be cast to a struct of the appropriate
type.

Note that the `void *` returned is permanent. If the module is reloaded, the function pointers in
the module will change, but the pointer to the module is still the same. This means it is legal to
query for the API of a module before it is even loaded. Later, when it is loaded, the API will be
filled in.

## OMG-API-12: create_xxx(), destroy_xxx()

Functions for creating objects that allocate the object's memory should be called `create_xxx()`.
The corresponding function for destroying the object should be called `destroy_xxx()`.

If the functions don't allocate memory, but just initialize an object that already exists, they
should be called `init_xxx()` and `shutdown_xxx()` instead.

## OMG-API-13: Counting functions are prefixed with `num_`

Functions that return a count should be prefixed with `num_`, for example, `num_buckets()`.

## OMG-API-14: Type naming conventions

* Externally visible objects must be named with `tm_` prefix.
* Internal objects should not have `tm_` prefix (to avoid confusion).

Every type should have a suffix. We use the following suffixes:

* `_api` — API with function pointers that can be queried through the API registry.
* `_i` — object interface — struct with function pointers and an `inst` member.
* `_vt` — vtable, for objects using vtables in their interface
* `_t` — simple/raw/POD data that you can copy freely and create on the stack.
* `_o` — classes, opaque data, structs that need initialization, etc.

## OMG-API-15: Use of `char *`, `unsigned char *`, `uint8_t *`, `void *`

* An API parameter representing a traditional string (zero-terminated, UTF-8 literal)
  should use `char *` (for compatibility with C literals).

* String processing code may want to cast from `char *` to `uint8_t *` to deal with bytes
  outside the 0--127 range.

* An API parameter representing a block of bytes (not a string) should use `uint8_t *`.

* An API parameter representing an untyped pointer to memory should use `void *`.

* The internal storage format for arbitrary data buffers should be `uint8_t[]`.

## OMG-API-16: Parameter order for functions

The typical parameter order for a function should look something like this:

~~~
return_value = f(object, parameters, options, helpers, more_return_values)
~~~

* `object` is the "object" (if any) that the function operates on (e.g. for `fread`, its the file
  handle).

* `parameters` are the primary parameters that the function uses (e.g. for `fopen`, its the file
  name).

* `options` are any options that modify how the function operates (e.g. open for reading or
  writing).

* `helpers` are any additional helper objects needed by the function to do its job (e.g. memory
   allocators).

* `more_return_values` -- if the function needs to return more than one value, the extra return
  values are passed as pointers at the end of the parameter list.

  Note that often readability is improved by returning multiple values as a `struct` instead of
  using output parameters. However, adding that extra struct increases the cognitive load a little
  bit, especially if it is only used in one place. You have to decide on a case-by-case basis
  whether returning a struct or using return value pointers makes more sense.

When passing pointers and sizes we use the `memcpy` parameter order:

* Destination pointer comes before source pointer.
* Pointer comes before byte counter.

## OMG-API-17: Use SI units

Units in APIs should be SI units: seconds, meters, kilograms, etc.

I.e., use a `double` parameter that specifies seconds, instead of an `uint32_t` that
specifies milliseconds.

Buffer sizes should be specified in bytes (not kilobytes or megabytes). Prefer to use `_bytes` rather
than `_size` in the name to indicate byte count rather than element count.

## OMG-API-18: API Documentation

API documentation is automatically generated using the `docgen` executable — it is built automatically
by `tmbuild`.

`docgen` parses `.h` files and finds `//` comment blocks that precede code blocks (`#define`,
`struct` or function definitions) and turns those comment blocks into comments for the code.
Freestanding comment blocks become freestanding text in the documentation.

Tips for using `docgen`:

* Comments should be written in Markdown ([Markdeep](https://casual-effects.com/markdeep/), to be specific).
* Quote variables using backticks.
* If you want to exclude something from the documentation, use `#pragma tm_docgen(off)` to turn
  off documentation generation and `#pragma tm_docgen(on)` to turn it back on again.
* Use H3 headings (`###`) for sections within a `.h` file.
* Use bold (`**`) for sections within a `struct`.

API documentation should aim to be succinct and provide the user with just the information that they
need. Avoid documentation boilerplate.

## OMG-API-19: Opaque object representations

Some modules are capable of creating opaque objects. We support four different kinds of object
representations.

**Simple objects** are represented by an opaque forward declared struct pointer:

~~~c
typedef struct tm_object_o tm_object_o;

struct tm_api
{
    tm_object_o *(*create)();
    void (*do_stuff)(tm_object_o *o);
    void (*destroy)(tm_object_o *o);
};
~~~

Functions in the API allow you to manipulate the object.

A variation on simple objects, **simple POD objects** are used for objects that are manipulated by
value rather than by pointer. It looks something like this:

~~~c
typedef struct tm_object_o
{
    uint8_t bytes[32];
} tm_object_o;

struct tm_api
{
    void (*init)(tm_object_o *o);
    void (*do_stuff)(tm_object_o o);
};
~~~

With this approach, the caller is responsible for managing the memory of the object and the object
can live on the stack. It can be compared and copied with `memcmp()` and `memcpy()`. On the inside,
the API implementation will cast the raw bytes to a concrete `object_t` representation of the
object. This representation is not exposed in the API either because it is platform-specific,
contains types that we don't want to expose, etc.

**Object interfaces** are used when we want several different implementations that implement the
same interface. They look like this:

~~~c
typedef struct tm_allocator_i
{
    tm_allocator_o *inst;

    void *(*realloc)(tm_allocator_o *inst, void *ptr,
        uint64_t old_size, uint64_t new_size,
        const char *file, uint32_t line, uint32_t scope);

    struct tm_memory_tracker_i *(*tracker)(tm_allocator_o *inst);
} tm_allocator_i;
~~~

Here `tm_allocator_o *` is an opaque implementation-specific representation of the data for the
object. Each implementation of the interface will cast this pointer to a specific type for that
implementation. In addition to the data pointer, the interface also contains (implementation
specific) function pointers that manipulate the data. Note that these functions all take the
instance data as an argument. You would use the interface like this:

~~~c
tm_allocator_i *a;

void *p = a->realloc(a->inst, 0, 0, 1024, __FILE__, __LINE__, scope);
~~~

Note that with this approach, each object contains all the function pointers of the interface, which
consumes a bit of memory (not a lot, since interfaces should typically be small). A more serious
drawback is that reloading code is tricky with this approach because all the function pointers in
all the objects must be replaced to point to the new reloaded version.

**Object interfaces with vtables** can be used to address this. They work as object interfaces,
except they put all the function definitions in a vtable, so they can be shared by multiple objects:

~~~c
typedef struct tm_allocator_vt tm_allocator_vt;

typedef struct tm_allocator_i
{
    tm_allocator_o *inst;
    tm_allocator_vt *vt;
} tm_allocator_i;

struct tm_allocator_vt
{
    void *(*realloc)(tm_allocator_o *inst, void *ptr,
        uint64_t old_size, uint64_t new_size,
        const char *file, uint32_t line, uint32_t scope);

    struct tm_memory_tracker_i *(*tracker)(tm_allocator_o *inst);
};
~~~

And using it becomes:

~~~c
void *p = a->vt->realloc(a->inst, 0, 0, 1024, __FILE__, __LINE__, scope);
~~~

Vtables also allow an object to implement an interface and extend it with additional functionality:

~~~c
struct tm_my_allocator_vt
{
    struct tm_allocator_vt;
    void (*set_chunk_size)(tm_allocator_o *inst, uint64_t chunk_size);
};
~~~

This kind of inheritance should be used *very sparingly* as it can create complicated couplings,
bad cache behavior and hard to read code. Prefer composition over inheritance. However, there are
a few special cases where inheritance can be a good solution.

## OMG-API-20: Enum types should be named

Enum types in APIs should be named. Note that since we are using C, this does not provide a
namespace for the constants, the constant names still need to be fully qualified:

~~~c
enum tm_log_type {
    TM_LOG_TYPE_INFO,
    TM_LOG_TYPE_DEBUG,
    TM_LOG_TYPE_ERROR
};
~~~

If an enum is used just to define a constant, the enum does not need to be named. In this case the
enum should be written on a single line, or a `#define` statement should be used:

~~~c
enum { TM_UI_MAX_TEXT_INPUT = 32 };

#define TM_UI_MAX_TEXT_INPUT 32
~~~

Functions that take an enum are recommended to take the enum type rather than an `uint32_t`,
however, structs that include an enum should specify an integer type for the struct member:

~~~c
void f(enum tm_log_type log_type);

struct data_t
{
    /* enum tm_log_type */ uint32_t log_type;
};
~~~

If we use the `enum` type in the struct what integer width it actually ends up with depends on
the platform/compiler, and this means that the layout of the struct is no longer ABI compatible
across different platforms and compilers. Having a predictable binary layout of the struct and ABI
compatibility is more important than the type safety you get from using the `enum` type in the
struct.

When passing an enum as an argument, smaller types are promoted to ints anyway, which means ABI
compatibility is conserved. So, in this case, the type safety and clarity of using the `enum` type
is worth it.

## OMG-API-21: Stable interfaces

With our module architecture, changing an API interface will break compatibility with DLLs and plugins
using the old interface. You should be conservative and not change the interfaces more than
necessary. For example, add new functions at the end of the interface rather than at the beginning,
that way existing users are unaffected by the change.

However, in a long term perspective, making sure the code can evolve, improve and adapt is more
important than keeping backward compatibility with really old code.

Currently, since the project is in early development, we don't attempt to keep the APIs stable and
you may change them freely.


# OMG-STYLE: Source code style rules

## OMG-STYLE-1: Source code should be formatted using `clang-format`

The formatting done by our `clang-format` rules is not perfect, but having an automatic formatting
system is more important than having a perfect one. It allows developers to focus on the code rather
than formatting issues.

* Set up your editor to automatically run `clang-format` on save (both Atom and Visual Studio Code
  have plugins for this).

* Install the pre-commit hook `utils/githooks/pre-commit` to format the source code automatically
  on commit (this is done automatically by `tmbuild`).

!!! note: Note
    The most annoying thing about `clang-format` is that it left-flushes `#if` statements
    rather than indenting them. This makes the code harder to read — especially when there are
    multiple nested statements. We should consider patching `clang-format` to fix this in the future.

    I.e. instead of this:

    ~~~c dont
    #if defined(TM_OS_WINDOWS)
    #define DIRSEP "\\"
    #else
    #define DIRSEP "/"
    #endif

    static void log_output_debug_string(struct tm_logger_o *inst,
        enum tm_log_type log_type, const char *msg)
    {
    #if defined(TM_OS_WINDOWS)
        void OutputDebugStringA(const char *s);
        OutputDebugStringA(msg);
    #endif
    }
    ~~~

    We want this:

    ~~~c
    #if defined(TM_OS_WINDOWS)
        #define DIRSEP "\\"
    #else
        #define DIRSEP "/"
    #endif

    static void log_output_debug_string(struct tm_logger_o *inst,
        enum tm_log_type log_type, const char *msg)
    {
        #if defined(TM_OS_WINDOWS)
            void OutputDebugStringA(const char *s);
            OutputDebugStringA(msg);
        #endif
    }
    ~~~

Tips if you find yourself fighting with `clang-format`:

* When formatting inlined struct initializers, you can make clang-format split it over multiple
  lines by putting a `,` after the last element, or keep it as one line by omitting the last `,`:

  ~~~c
  a = { .x = 1, .y = 2 };
  b = {
      .x = 1,
      .y = 2,
  };
  ~~~

* If you can't get the line breaking you want, you can prevent clang-format from merging your lines
  by putting an empty comment `//` at the end of the line.

## OMG-STYLE-2: If you are writing C++, prefer simple C++

If you are writing code in C++ (C is preferred) you should use a simple style. Avoid class
hierarchies, multiple inheritance, template metaprogramming, virtual functions, operator overloading
etc.

Prefer public data members over accessor functions. In most cases, accessor functions are
unnecessary boilerplate and the "encapsulation" they provide does not add any real value.

Avoid using `auto`. `auto` makes it harder to see what types are actually being used in the code and
leads to more cryptic error messages. Only use `auto` for complicated types, such as lambdas or
iterators.

Prefer using `static` rather than anonymous namespaces (`namespace { }`) to prevent external
linkage. The reason we prefer `static` is that it doesn't create huge namespace scopes that make it
difficult to see where the scope begins and ends. For local classes and other things that have
external linkage in C++, but cannot use the `static` keyword, use a small `namespace {}` scope
wrapped around just that object.

## OMG-STYLE-4: Line length is up to the discretion of the programmer

Monitors are wider these days and nobody prints source code, so there is no need to enforce a line
width of 80 characters. Long lines can be hard to read, but using short variable names and chopping
up a line with line breaks can also make it hard to read -- we leave it up to each programmer to
find the right balance. Try to avoid super long lines, but don't religiously line break your code at
a fixed width at the cost of readability.

If you want a guide for line length -- we think line breaking at 100 characters makes more sense
than line breaking at 80. 80 is unnecessarily restrictive and 100 gives a bit more room for longer
variables.

Documentation should always be line-broken at 100 characters. Reading text is different than reading
source code. Source code is read line by line, while text is read as continuous paragraphs. Column
widths greater than 100 characters makes text really hard to read.

Make use of editor features to line break the documentation at 100 characters. For example, in
*Visual Studio Code*, set *Word Wrap Column* to 100, install the *Rewrap* extension and press
*Alt-Q* to wrap a paragraph.

!!! note: Note
    We belive that the *right* way to handle long code lines is for the *editor* to word wrap
    automatically *with intelligent indentation*. I.e., when you enable word wrap in the editor, the
    indentation should look similar to what you would get with hand-inserted line breaks.

    This way, each user can use the editor width that they prefer, and the code will look good
    everywhere. Unfortunately, editors are still pretty bad at doing this.

## OMG-STYLE-7: Unexposed functions and variables should be declared `static`

Everything that is not part of the public API should be declared `static` so that it doesn't have
external linkage.

## OMG-STYLE-9: Naming

Use short names for local variables. Use longer descriptive names for things with bigger scopes
(functions, structs, global variables).

Outside of local variables, you should not use abbreviations in variable names.

Most things are named with `lower_case`. Enums and define constants are named with `UPPER_CASE`.

Functions private to a source file can be given the prefix `private__` to emphasize their private
nature.

## OMG-STYLE-10: Typedefs

Use typedefs to define names for structs and unions that can be used without the `struct` keyword.
These names should always be the same as the name of the struct:

~~~
typedef struct tm_vec2_t tm_vec2_t;
~~~

Don't use typedefs for other things. In particular, don't typedef pointer types (hiding the fact
that they are pointers).

## OMG-STYLE-11: Goto

Use of `goto` should be limited to early exiting of functions that need to do some cleanup before
returning.

## OMG-STYLE-12: Commenting

Exposed API functions need documentation comments, other code only needs to be commented as
necessary. Write your code clearly and use sensible names to reduce the need for comments.

`//` comments should be used for all comments. `/*` should only be used to temporarily disable code.

## OMG-STYLE-13: No dead code

Unused code, commented out code, functions that are never called, etc, should be removed from the
project to reduce the cognitive load of reading the source code. Old code is available in the source
history if it is needed.

## OMG-STYLE-14: Macros

Macros should be `CAPITALIZED`.

Consider using inline functions instead of macros.

Macros with multiple statements should be enclosed in a `do {...} while (0)` block so that they can
be used as part of the normal control flow.

## OMG-STYLE-15: Conditional compilation

Prefer `#if defined(X)` to `#ifdef X`.

Avoid use of `#if` and `#ifdef` as much as possible. Source code is easier to reason about if
every compiler on every platform sees the same code.

Don't sprinkle the code with platform-specific `#ifdef`s. Instead, put all the platform-specific
stuff in its own files, one per platform: `os.win.c`, `os.osx.c`, etc

Use the `TM_IS_DEFINED` macro and a regular `if` statement instead of `#if` whenever possible:

~~~c
if (TM_IS_DEFINED(TM_OS_WINDOWS))
    do_some_stuff();
else
    do_some_other_stuff();
~~~

instead of:

~~~c dont
#if defined(TM_OS_WINDOWS)
do_some_stuff();
#else
do_some_other_stuff();
#endif
~~~

This is preferable, because when we compile, all code paths are checked for syntax errors, not just
the platform that we are compiling for.

## OMG-STYLE-16: Use `TM_STATIC_HASH()` for static hash values

To create a static hash value, you should use the `TM_STATIC_HASH()` macro, like this:

~~~c
const uint64_t name_hash = TM_STATIC_HASH("name", 0xd4c943cba60c270bULL);
~~~

The macro resoves to its second argument, so when compiled this will just be:

~~~c
const uint64_t name_hash = 0xd4c943cba60c270bULL;
~~~

`TM_STATIC_HASH()` is defined in `foundation/api_types.h`, so it can be used  to define macros in
header files.

The `hash.exe` utility program can be used to automatically check and patch the arguments of all
`TM_STATIC_HASH()` macros in the codebase. So instead of trying to figure out what the right murmur
hash value is for a particular string, you can just write:

~~~c
const uint64_t name_hash = TM_STATIC_HASH("name",);
~~~

And then run:

~~~c
bin/Debug/hash.exe
~~~

in the root of your project, and the code will automatically be patched to:

~~~c
const uint64_t name_hash = TM_STATIC_HASH("name", 0xd4c943cba60c270bULL);
~~~

## OMG-STYLE-17: Use `TM_LOCALIZE()` for localization

Every string that is visible in the UI should be wrapped in a `TM_LOCALIZE()` macro for
localization. Example:

~~~c
const tm_ui_menu_item_t items[] = {
    { .text = TM_LOCALIZE("File"), .item_id = MENU_FILE },
    { .text = TM_LOCALIZE("Edit"), .item_id = MENU_EDIT },
    { .text = TM_LOCALIZE("View"), .item_id = MENU_VIEW },
    { .text = TM_LOCALIZE("Tools"), .item_id = MENU_TOOLS },
};
~~~

The macro will call out to the localize system to translate the string to the current UI language.
If there is no translation for the string, or the UI language is English, the original string will
be returned. Thus, wrapping strings in `TM_LOCALIZE()` allows them to be localized.

The Machinery editor includes a *Gibberish* translation option to test localization. When you set
the UI language to *Gibberish*, all localized strings will be translated to slightly longer
randomized strings. This serves two purposes. First, it allows you to check that you have properly
used `TM_LOCALIZE()` everywhere. If you still see any English text after switching to Gibberish,
that text has not been localized. Second, it allows you to check that the UI doesn't break if the
translated strings are slightly longer than the original.

The `localize.exe` program can be used for translation. It will find all the `TM_LOCALIZE()` macros
in the project, check them against a list of translations and print out any translations that are
still missing.

Since we scan for `TM_LOCALIZE()` macros *statically*, `TM_LOCALIZE()` can only use *static*
strings. I.e., this is an error:

~~~c dont
// Don't do this.
sprintf(msg, "Thanks %s!");
msg = TM_LOCALIZE(msg);
~~~

Instead of trying to localize dynamic strings like this, compose dynamic strings out of localized
static parts:

~~~c
sprintf(msg, TM_LOCALIZE("Thanks %s!"), name);
~~~

!!! Tip: Localization tips
    * Be wary of concatenating strings out of too many individual parts. Word order might differ between
      languages and the way you put the string together might not make sense. E.g.:

        ~~~c dont
        // Not ideal: This locks down the sentence structure and depends on
        // "great" and "good" being  translated the same way in this sentence
        // as in all other possible sentences.
        sprintf(msg, TM_LOCALIZE("Thanks %s, you did %s!"), name,
            score > 100 ? TM_LOCALIZE("great") : TM_LOCALIZE("good"));
        ~~~

        Better:

        ~~~c
        sprintf(msg, score > 100 ? TM_LOCALIZE("Thanks %s, you did great!") :
            TM_LOCALIZE("Thanks %s, you did good!", name));
        ~~~

    * Make sure to clearly mark in your APIs which function return localized vs non-localized strings.
      E.g., if you just see a function:

        ~~~c
        const char *name();
        ~~~

        it is hard to know if it returns a localized name (suitable for displaying the UI), or a
        non-localized string for internal use. In some cases, you might need two separate functions for
        the two different purposes:

        ~~~c
        const char *name();
        const char *display_name();
        ~~~

## OMG-STYLE-18: All variables that are not modified should be declared `const`

Any variable in the code that is not mutated should be const-declared:

~~~c
const uint32_t x = y + 3;
~~~

Using `const` this way makes it easier for readers to understand the code. They know that `x` will
always have the value that was set on initialization and do not have to scan the code for places
where `x` might be modified.

Value parameters passed to a function are always assumed to be `const`. However, we do not explicitly
declare them as const. Never modify value parameters in your function implementation:

~~~c dont
// Don't do this:
uint32_t add(uint32_t a, uint32_t b)
{
    a += b;
    return a;
}
~~~


# OMG-BUILD: Build process

## OMG-BUILD-1: Single button builds through `tmbuild`

Running the `tmbuild` executable on a fresh machine should be enough to build the project.

## OMG-BUILD-2: Distribution packages through `tmbuild -p`

To build a package of the engine for distribution, use the `-p` option, such as:

~~~sh
tmbuild -p pre-alpha
~~~

Packages should be defined in the `utils/tmbuild/packages.json` file.

# OMG-GIT: Git workflow

## OMG-GIT-1: Use trunk-based development

At Our Machinery, we use a [trunk-based development](https://trunkbaseddevelopment.com/) workflow.
The basic idea behind trunk-based development is to not have long-lived feature branches. Instead,
work should be merged incrementally to the `master` trunk, typically at least daily. The workflow
is:

* Work on your change directly in `master` (you can create a branch if you feel you need it).
* Work on a small isolated feature in a single commit.
* When you have something to share, write a commit message that is as descriptive as possible and
  push it.
* Typically the process for this is: `git fetch`, `git rebase origin/master`, `git push`.

The advantages of using this workflow are:

* Everybody sees the same codebase — people are not off on separate branches.
* Merge conflicts are reduced.
* Integration happens sooner rather than later — problems are discovered sooner.
* Commits are smaller and easier to grasp, two months of code doesn't come in as a single chunk.
* Trunk history is branch free which simplifies the use of tools like `git-bisect`.

A disadvantage often claimed against this workflow is that the master branch becomes more unstable
when more people are committing into it. This is true. However, the stability you get with long
feature branches is often deceptive — you are just delaying the instability until the feature
branches get integrated. Usually, that happens near a release — everybody integrates their feature
branches at the same time, and you suddenly have a lot of instability. But near a release is exactly
when you don't want a lot of instability.

To make sure that you can commit often, break bigger work into smaller steps that can be worked on
and tested independently. (That is usually the only way to tackle bigger projects anyway.) Each
single step should be something that adds value to the project by moving it forward in the right
direction. This makes it easy to course-correct early if we are moving in the wrong direction.

For features that are tricky to enable incrementally, consider the use of *feature flags* — compiler
or runtime flags that enable or disable the new feature. This allows the feature to be tested as it
is being developed, instead of "after integration", as in a feature-branch workflow. But note that
developing the feature incrementally is even better than using feature flags, as feature flags
segment the developers and can hide integration problems. (Only the developers with certain feature
flags set will test your code changes.)

In the current setup, developers test and merge their branches to the trunk as soon as they are done.
Code review happens before and after:

* Code review before — make sure that the feature plan is sound.
* Code review after merge — review the changes and look for any problems.
* Code review of completed system — does it match the plan, are there changes that need to be made?

Code does not need to pass code review to get merged. Requiring code reviews before merge slows down
the merge process to take several days. This discourages the main principle of trunk-based
development — to merge often and deliver changes quickly. Lack of pre-approval code review shouldn't
be seen as a free-for-all encouragement of sloppy coding. Rather, the trust and responsibility to
produce high-quality code is put on each developer.

## OMG-GIT-2: Prefer rebase to merge

The advantage of `rebase` is that it gives a cleaner, more linear commit history than relying on
`merge`, especially when there are many contributors. A more linear commit history makes it easier
to tell what is going on in the project and use tools such as `git bisect`.

The basic rebase workflow is, instead of doing this to push your changes:

~~~sh dont
git pull
git push
~~~

Do this:

~~~sh
git fetch
git rebase origin/master
git push
~~~

Conflicts can be trickier to handle in rebase, but if you follow the rule of pushing often you
shouldn't run into conflicts too often.

Sometimes change history can get messy. For example, you work on a branch, make multiple commits,
merge in changes from the master etc. To clean it up, I suggest the following:

~~~sh
# Merge master into your branch, to make sure you have the latest changes
# and fix any issues.
git merge master

# Create a patch with your changes against the master.
git diff master > branch.patch

# Switch back to master and apply the patch
git checkout master
git apply branch.patch

# Commit the changes
git commit
~~~

Now you have all your messy changes and merges applied as a single clean commit on top of master.

This technique can also be used in case you get a conflict when using the regular rebase workflow.
