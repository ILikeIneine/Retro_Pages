# Nesessary Checks

***All checks listed [here](https://clang.llvm.org/extra/clang-tidy/checks/list.html)***

***We do NOT focus on all of them***

Here's what we checks for typically c++ standard

- bugprone-*

    检查目标容易出错的代码结构

  - bugprone-dynamic-static-initializers 禁止返回动态初始化的静态变量
  - bugprone-fold-init-type 检查截断和溢出的情况
  - bugprone-forward-declaration-namespace 检查同名但未定义的命名空间
  - bugprone-infinite-loop 检查容易出错的无限循环
  - bugprone-integer-division 检查会导致精度损失的整数除法
  - bugprone-macro-parentheses 查找由于缺少括号而可能具有意外行为的宏
  - bugprone-misplaced-operator-in-strlen-in-alloc 检查字符串操作的不当行为
  - bugprone-misplaced-pointer-arithmetic-in-alloc 检查错误的指针动态分配内存
  - bugprone-misplaced-widening-cast 类型转换造成的精度损失
  - bugprone-no-escape 查找容易出错的noescape
  - bugprone-redundant-branch-condition 检查逻辑if的重复判断冗余
  - bugprone-signed-char-misuse 检查错误的字符型与字符型、整型之间的强制转换和比较
  - bugprone-sizeof-container 检查可能出错的sizeof使用
  - bugprone-string-constructor	检查容易出错的字符串初始化
  - bugprone-string-integer-assignment 检查容易出错的字符串赋值(可能导致误判)
  - ugprone-suspicious-include.HeaderFileExtensions 检查错误的文件包含
  - bugprone-suspicious-memset-usage 检查错误的memset的使用
  - bugprone-too-small-loop-variable 检查容易出错的循环变量
  - bugprone-undefined-memory-manipulation 查找容易出错的未定义内存操作
  - bugprone-unused-return-value 检查未使用的一些返回值（如返回指针）
  - bugprone-use-after-move	禁止使用std::move之后的对象仍被使用
  - bugprone-virtual-near-miss 检查函数名与基类虚函数类似的函数声明

- cert-*

    与 CERT 安全编码指南相关的检查

- clang-analyzer-*

    Clang 静态分析器检查

- cppcoreguidelines-*

    与 C++ 核心指南相关的检查
  - cppcoreguidelines-avoid-non-const-global-variables 禁止非常量全局变量
  - cppcoreguidelines-interfaces-global-init 检查extern对象的全局变量的初始值设定项初始化顺序问题
  - cppcoreguidelines-macro-usage 检查可能被认为有问题的宏用法
  - cppcoreguidelines-narrowing-conversions
  - cppcoreguidelines-no-malloc 对c风格内存分配的检查
  - cppcoreguidelines-owning-memory 检查内存分配时是否使用gsl的方法
  - cppcoreguidelines-prefer-member-initializer
  - cppcoreguidelines-pro-bounds-array-to-pointer-decay
  - cppcoreguidelines-pro-bounds-constant-array-index
  - cppcoreguidelines-pro-bounds-pointer-arithmetic 标记指针的算法使用
  - cppcoreguidelines-pro-type-const-cast	标记const_cast的使用
  - cppcoreguidelines-pro-type-cstyle-cast
  - cppcoreguidelines-pro-type-member-init
  - cppcoreguidelines-pro-type-vararg
  - cppcoreguidelines-slicing 检查导致slice的情况
  - cppcoreguidelines-special-member-functions
  
- google-*

    与 Google 编码约定相关的检查
  - google-build-explicit-make-pair 检查make_pair是否推断出模板类型
  - google-build-namespaces
  - google-build-using-namespace 检查是否使用using namespace
  - google-default-arguments 为虚函数参数添加默认值
  - google-explicit-constructor 检查显式构造函数，避免隐式转换带来的风险
  - google-global-names-in-headers 在头文件中标记全局命名空间污染
  - google-objc-avoid-nsobject-new
  - google-objc-avoid-throwing-exception
  - google-objc-function-naming
  - google-objc-global-variable-declaration
  - google-readability-avoid-underscore-in-googletest-name
  - google-readability-casting 查找 C 风格强制转换的用法
  - google-readability-todo 查找TODO有没有署用户名、邮件等
  - google-runtime-int 检查整数定义，将short, long，long long改为intxx_类型
  - google-runtime-operator	检查用户自定义表述的重载运算符
  - google-upgrade-googletest-case 用户自定义函数名中，将带有case字符改为suite

- modernize-*

  提倡使用现代语言结构的检查
  - modernize-use-noexcept 函数定义的throw替换为noexcept
  - modernize-use-nullptr 将空指针NULL改为nullptr
  - modernize-use-override 在重载虚函数中添加override声明
