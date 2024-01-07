# Clang Tidy Tutorial

***[Official Docs](https://clang.llvm.org/extra/clang-tidy/)***

***[ref](https://github.com/selfboot/ClangTidyChecks)***

How do we use a automatic static code analyzer to perform the check operation for our project?

Here is a way which **only** suitable for static analyzing on & only on CMake Project, mostly not suitable on windows(msvc)

## Pre-Condition

1. Install LLVM tool kit on your PC.
2. Set environment variable correctly.
3. Have some echos while typing `clang-tidy --version' on terminal.

## On Clang & GCC compiler

### SET PROPERTIES

CMake provide us a really simple way to do this since version <3.6>. Just set [`CMAKE_<LANG>_CLANG_TIDY`](https://cmake.org/cmake/help/latest/variable/CMAKE_LANG_CLANG_TIDY.html) in your CMakeLists.txt.

This will set default value for [`<LANG>_CLANG_TIDY`](https://cmake.org/cmake/help/latest/prop_tgt/LANG_CLANG_TIDY.html) target property. Actually the variable `CMAKE_CXX_CLANG_TIDY` is just used to initialize the target property `CXX_CLANG_TIDY`.
It has to be defined before the targets are added. Defining it at the end of the file is too late.

***Note!!*** it works **only** with Makefiles and Ninja. It doesn't work with msbuild.

### PASSING ARGUMENTS TO CLANG-TIDY

Specify a [semicolon-separated list](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#cmake-language-lists) containing a command line for the clang-tidy tool.

e.g.

``` CMake
set(
    CMAKE_CXX_CLANG_TIDY 
    clang-tidy 
    -checks=-*,readability-*;
    -config-file='';
    -dump-config;
    )
```

*[Here](https://clang.llvm.org/extra/clang-tidy/)* is all options for clang-tidy. These are basically tutorial on Clang & GCC.

## On MSBuild or VS

Typically follow this [Guild](https://devblogs.microsoft.com/cppblog/code-analysis-with-clang-tidy-in-visual-studio/).

And this [Guild](https://learn.microsoft.com/en-us/cpp/code-quality/clang-tidy?view=msvc-170).

Just Because MSbuild is a piece of shit. No comment.

## Export Results

Add -export-fixes option and specify the YAML file to store suggested fixes in. The stored fixes can be applied to the input source code with clang-apply-replacements.

Also, by our great CMake, you can set variable [`CMAKE_<LANG>_CLANG_TIDY_EXPORT_FIXES_DIR`](https://cmake.org/cmake/help/latest/variable/CMAKE_LANG_CLANG_TIDY_EXPORT_FIXES_DIR.html#variable:CMAKE_%3CLANG%3E_CLANG_TIDY_EXPORT_FIXES_DIR) to specify the output path, which is more recommended.

e.g.

``` CMake
set(CMAKE_CXX_CLANG_TIDY_EXPORT_FIXES_DIR $(CMAKE_SOURCE_DIR)/clang-tidy-fixes) 
```

## .clang-tidy File

When properties is to long. We'd better dump these configurations as a file named .clang-tidy.

To generate a template you can add `-dump-config` option to print current configs, write it as a YAML format file named `.clang-tide`.

``` Bash
clang-tidy -checks=* -dump-config > .clang-tidy
```

You could modify the checks that your project need by editing the `Checks` line.

Note that using `-config-file=<string>` option to specify the path of .clang-tidy.
