# ClangTidy代码静态检测

## clang-tidy

> clang-tidy is a clang-based C++ “linter” tool. Its purpose is to provide an extensible framework for diagnosing and fixing typical programming errors, like style violations, interface misuse, or bugs that can be deduced via static analysis. clang-tidy is modular and provides a convenient interface for writing new checks.

clang-tidy 是一个基于 clang 的 C++"linter"工具。 其用于诊断和修复典型的编程错误，如风格违规、界面误用或可通过静态分析推断出的错误。 clang-tidy 是模块化的，为编写新检查提供了一个方便的接口。包括但不限于以下内容：

- 违反代码规范的代码模式相关检测
- 不容易在编译时发现的代码错误
- CERT的代码规范相关检测
- 潜在的可能导致缓冲区溢出问题相关检查
- 潜在的可能导致内存泄漏问题相关检查
- 可能由代码导致的未定义行为（undefined behavior）

clang-tidy 实现了对代码行为的100多种检测，这里我们选择了其中涉及到`部分代码风格项`和`全部代码安全项`的审计，以避免可能带来的精度丢失，内存泄漏，缓冲区溢出，算术溢出等编译期不易发现的典型问题，具体检查地如下表所示：

| cert规范 | 描述 |
| --- | --- |
|cert-env33-c|系统调用函数检查 |
|cert-err34-c|类型转换函数是否有效|
|cert-err33-c|检查系统调用返回值是否未被使用|
|cert-err52-cpp|setjmp()，longjmp()|
|cert-dcl58-cpp | 对std posix等命名空间的修改
|cert-err34-c | 不验证转换字符串到数字的有效性的代码，如c语言atoi()
|cert-flp30-c | 检查浮点类型的循环

注：cert-err33-c 检测范围：

```c++
::aligned_alloc;::asctime_s;::at_quick_exit;::atexit;::bsearch;::bsearch_s;::btowc;::c16rtomb;::c32rtomb;::calloc;::clock;::cnd_broadcast;::cnd_init;::cnd_signal;::cnd_timedwait;::cnd_wait;::ctime_s;::fclose;::fflush;::fgetc;::fgetpos;::fgets;::fgetwc;::fopen;::fopen_s;::fprintf;::fprintf_s;::fputc;::fputs;::fputwc;::fputws;::fread;::freopen;::freopen_s;::fscanf;::fscanf_s;::fseek;::fsetpos;::ftell;::fwprintf;::fwprintf_s;::fwrite;::fwscanf;::fwscanf_s;::getc;::getchar;::getenv;::getenv_s;::gets_s;::getwc;::getwchar;::gmtime;::gmtime_s;::localtime;::localtime_s;::malloc;::mbrtoc16;::mbrtoc32;::mbsrtowcs;::mbsrtowcs_s;::mbstowcs;::mbstowcs_s;::memchr;::mktime;::mtx_init;::mtx_lock;::mtx_timedlock;::mtx_trylock;::mtx_unlock;::printf_s;::putc;::putwc;::raise;::realloc;::remove;::rename;::scanf;::scanf_s;::setlocale;::setvbuf;::signal;::snprintf;::snprintf_s;::sprintf;::sprintf_s;::sscanf;::sscanf_s;::strchr;::strerror_s;::strftime;::strpbrk;::strrchr;::strstr;::strtod;::strtof;::strtoimax;::strtok;::strtok_s;::strtol;::strtold;::strtoll;::strtoul;::strtoull;::strtoumax;::strxfrm;::swprintf;::swprintf_s;::swscanf;::swscanf_s;::thrd_create;::thrd_detach;::thrd_join;::thrd_sleep;::time;::timespec_get;::tmpfile;::tmpfile_s;::tmpnam;::tmpnam_s;::tss_create;::tss_get;::tss_set;::ungetc;::ungetwc;::vfprintf;::vfprintf_s;::vfscanf;::vfscanf_s;::vfwprintf;::vfwprintf_s;::vfwscanf;::vfwscanf_s;::vprintf_s;::vscanf;::vscanf_s;::vsnprintf;::vsnprintf_s;::vsprintf;::vsprintf_s;::vsscanf;::vsscanf_s;::vswprintf;::vswprintf_s;::vswscanf;::vswscanf_s;::vwprintf_s;::vwscanf;::vwscanf_s;::wcrtomb;::wcschr;::wcsftime;::wcspbrk;::wcsrchr;::wcsrtombs;::wcsrtombs_s;::wcsstr;::wcstod;::wcstof;::wcstoimax;::wcstok;::wcstok_s;::wcstol;::wcstold;::wcstoll;::wcstombs;::wcstombs_s;::wcstoul;::wcstoull;::wcstoumax;::wcsxfrm;::wctob;::wctrans;::wctype;::wmemchr;::wprintf_s;::wscanf;::wscanf_s;
```

| bugprone相关 | 描述 |
| --- | --- |
| bugprone-dangling-handle | 检测值句柄中的垂悬引用
| bugprone-assert-side-effect | assert() 带来的潜在副作用
| bugprone-dynamic-static-initializers | 禁止返回动态初始化的静态变量
| bugprone-exception-escape | 禁止在一些函数内抛出异常，避免带来一些风险（析构函数、移动构造函数、移动赋值运算符、main函数、swap函数等）
| bugprone-fold-init-type | 检查截断和溢出的情况
| bugprone-forward-declaration-namespace | 检查同名但未定义的命名空间
| bugprone-incorrect-roundings | 排除不正确舍入问题
| bugprone-infinite-loop | 检查容易出错的无限循环
| bugprone-integer-division | 检查会导致精度损失的整数除法
| bugprone-macro-parentheses | 查找由于缺少括号而可能具有意外行为的宏
| bugprone-misplaced-operator-in-strlen-in-alloc | 检查字符串操作的不当行为
| bugprone-misplaced-pointer-arithmetic-in-alloc | 检查错误的指针动态分配内存
| bugprone-misplaced-widening-cast | 类型转换造成的精度损失
| bugprone-no-escape | 查找容易出错的noescape
| bugprone-not-null-terminated-result | 检查可能导致非空终止结果的函数调用，使用strcpy()、strncpy()、strcpy_s()、 strncpy_s()代替memcpy()和memcpy_s()等
| bugprone-redundant-branch-condition | 检查逻辑if的重复判断冗余
| bugprone-signed-char-misuse | 检查错误的字符型与字符型、整型之间的强制转换和比较
| bugprone-sizeof-container | 检查可能出错的sizeof使用
| bugprone-string-constructor | 检查容易出错的字符串初始化
| bugprone-string-integer-assignment | 检查容易出错的字符串赋值(可能导致误判)
| bugprone-string-literal-with-embedded-nul | 检查带有嵌入'\0'字符的字符串
| ugprone-suspicious-include.HeaderFileExtensions | 检查错误的文件包含
| bugprone-suspicious-memset-usage | 检查错误的memset的使用
| bugprone-too-small-loop-variable | 检查容易出错的循环变量
| bugprone-suspicious-memset-usage | 检查错误的memset的使用
| bugprone-undefined-memory-manipulation | 查找容易出错的未定义内存操作
| bugprone-unused-return-value | 检查未使用的一些返回值（如返回指针）
| bugprone-use-after-move | 禁止使用std::move之后的对象仍被使用
| bugprone-virtual-near-miss | 检查函数名与基类虚函数类似的函数声明

| c++核心准则（cppcoreguidelines） | 描述
|---|---|
|cppcoreguidelines-avoid-goto|避免使用goto
|cppcoreguidelines-avoid-non-const-global-variables|禁止非常量全局变量
|cppcoreguidelines-interfaces-global-init|检查extern对象的全局变量的初始值设定项初始化顺序问题
|cppcoreguidelines-macro-usage|检查可能被认为有问题的宏用法
|cppcoreguidelines-narrowing-conversions|窄类型转换带来的精度丢失
|cppcoreguidelines-no-malloc|对c风格内存分配的检查
|cppcoreguidelines-owning-memory|检查内存分配时是否使用gsl的方法
|cppcoreguidelines-pro-bounds-array-to-pointer-decay|标志数组向指针的退化
|cppcoreguidelines-pro-bounds-pointer-arithmetic|标记指针的算法使用
|cppcoreguidelines-pro-type-const-cast|标记const_cast的使用
|cppcoreguidelines-pro-type-union-access|标记union成员的访问权限
|cppcoreguidelines-pro-type-vararg|标记对c样式可变参数函数调用以及对va_arg的使用
|cppcoreguidelines-slicing|检查导致slice的情况
|cppcoreguidelines-pro-type-static-cast-downcast|向下转换时将标记未使用dynamic_cast的转换

注：cppcoreguidelines-owning-memory检测范围：

资源释放: ::free;::realloc;::freopen;::fclose

资源分配: ::malloc;::aligned_alloc;::realloc;::calloc;::fopen;::freopen;::tmpfile

| Google规范 | 描述 |
| --- | --- |
|google-default-arguments|为虚函数参数添加默认值
|google-explicit-constructor |检查显式构造函数，避免隐式转换带来的风险
|google-runtime-int|检查整数定义，将short, long，long long改为intxx_类型
|google-runtime-operator|检查用户自定义表述的重载运算符
|google-readability-casting|查找 C 风格强制转换的用法

|clang分析器（clang-analyzer） | 描述 |
|---|---|
|clang-analyzer-core.uninitialized.CapturedBlockVariable|检查未初始化的块
|clang-analyzer-cplusplus.InnerPointer|检查释放后的内部指针的使用
|clang-analyzer-nullability.NullableReturnedFromNonnull|检查从非空返回类型 返回 可能空的指针
|clang-analyzer-optin.performance.Padding|检查过度填充的结构

|modernize相关 | 描述 |
|---|---|
|modernize-use-noexcept|函数定义的throw替换为noexcept
|modernize-use-bool-literals|找出所有隐式从int转成bool的literal, 使用true或者false代替
|modernize-use-nullptr|用nullptr代替NULL
|modernize-use-override|对于子类改写父类的virtual方法，在方法后面添加override, 并删掉virtual前缀
