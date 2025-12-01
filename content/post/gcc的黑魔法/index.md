---
title: "Gcc的黑魔法"
description: 
date: 2025-11-30T00:10:08+08:00
image: 
math: 
license: 
hidden: false
comments: false
draft: false
categories:
    - c++
---
测试文件：
```c
#include <stdio.h>
inline auto add(int x, int y) { return x + y; }
auto add_xy(int x, int y) { return x + y; }
int main() {
  int a, b;
  int c = add(a, b);
  int d = add(a, b);
}
```
c++的编译过程：预处理，编译，汇编，链接

## 环境与配置：空手套白狼
- 指令：g++ -dM -E -x  c++ /dev/null
- 解释： 
   - -dM:倾倒出所有的宏
   - -E:只做预处理
   - -x c++:把文件当作cpp
   - /dev/null 空输入文件
- 技巧：用grep命令读取想要的宏
   - 查 C++ 版本：... | grep __cplusplus
   - 查平台架构：... | grep -E "linux|x86|arm"
   -   查类型大小：... | grep SIZEOF
- 示例：
```c
g++ -dM -E -x c++ /dev/null            (base)
#define __DBL_MIN_EXP__ (-1021)
#define __cpp_nontype_template_parameter_auto 201606L
#define __UINT_LEAST16_MAX__ 0xffff
#define __FLT16_HAS_QUIET_NAN__ 1
#define __ATOMIC_ACQUIRE 2
#define __FLT128_MAX_10_EXP__ 4932
#define __FLT_MIN__ 1.17549435082228750796873653722224568e-38F
#define __GCC_IEC_559_COMPLEX 2
#define __cpp_aggregate_nsdmi 201304L
#define __UINT_LEAST8_TYPE__ unsigned char
#define __SIZEOF_FLOAT80__ 16
#define __BFLT16_DENORM_MIN__ 9.18354961579912115600575419704879436e-41BF16
#define __INTMAX_C(c) c ## L
#define __CHAR_BIT__ 8
#define __UINT8_MAX__ 0xff
#define __SCHAR_WIDTH__ 8
#define __WINT_MAX__ 0xffffffffU
#define __FLT32_MIN_EXP__ (-125)
#define __cpp_static_assert 201411L
#define __BFLT16_MIN_10_EXP__ (-37)
#define __ORDER_LITTLE_ENDIAN__ 1234
#define __WCHAR_MAX__ 0x7fffffff
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_2 1
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_4 1
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_8 1
#define __GCC_ATOMIC_CHAR_LOCK_FREE 2
#define __GCC_IEC_559 2
#define __FLT32X_DECIMAL_DIG__ 17
#define __FLT_EVAL_METHOD__ 0
#define __cpp_binary_literals 201304L
#define __FLT64_DECIMAL_DIG__ 17
#define __CET__ 3
#define __cpp_noexcept_function_type 201510L
#define __GCC_ATOMIC_CHAR32_T_LOCK_FREE 2
#define __cpp_variadic_templates 200704L
#define __UINT_FAST64_MAX__ 0xffffffffffffffffUL
#define __SIG_ATOMIC_TYPE__ int
#define __DBL_MIN_10_EXP__ (-307)
#define __FINITE_MATH_ONLY__ 0
#define __cpp_variable_templates 201304L
#define __FLT32X_MAX_EXP__ 1024
#define __GCC_HAVE_SYNC_COMPARE_AND_SWAP_1 1
#define __FLT32_HAS_DENORM__ 1
#define __UINT_FAST8_MAX__ 0xff
#define __cpp_rvalue_reference 200610L
#define __cpp_nested_namespace_definitions 201411L
#define __DEC64_MAX_EXP__ 385
#define __INT8_C(c) c
#define __LDBL_HAS_INFINITY__ 1
#define __INT_LEAST8_WIDTH__ 8
#define __cpp_variadic_using 201611L
#define __UINT_LEAST64_MAX__ 0xffffffffffffffffUL
#define __INT_LEAST8_MAX__ 0x7f
#define __cpp_attributes 200809L
#define __cpp_capture_star_this 201603L
#define __SHRT_MAX__ 0x7fff
#define __LDBL_MAX__ 1.18973149535723176502126385303097021e+4932L
#define __FLT64X_MAX_10_EXP__ 4932
#define __cpp_if_constexpr 201606L
#define __BFLT16_MAX_10_EXP__ 38
#define __BFLT16_MAX_EXP__ 128
#define __LDBL_IS_IEC_60559__ 1
#define __FLT64X_HAS_QUIET_NAN__ 1
#define __UINT_LEAST8_MAX__ 0xff
#define __GCC_ATOMIC_BOOL_LOCK_FREE 2
#define __FLT128_DENORM_MIN__ 6.47517511943802511092443895822764655e-4966F128
#define __UINTMAX_TYPE__ long unsigned int
#define __cpp_nsdmi 200809L
#define __BFLT16_DECIMAL_DIG__ 4
#define __linux 1
#define __DEC32_EPSILON__ 1E-6DF
#define __FLT_EVAL_METHOD_TS_18661_3__ 0
#define __UINT32_MAX__ 0xffffffffU
#define __GXX_EXPERIMENTAL_CXX0X__ 1
#define __DBL_DENORM_MIN__ double(4.94065645841246544176568792868221372e-324L)
#define __FLT128_MIN_EXP__ (-16381)
#define __WINT_MIN__ 0U
#define __FLT128_MIN_10_EXP__ (-4931)
#define __FLT32X_IS_IEC_60559__ 1
#define __INT_LEAST16_WIDTH__ 16
#define __SCHAR_MAX__ 0x7f
#define __FLT128_MANT_DIG__ 113
#define __WCHAR_MIN__ (-__WCHAR_MAX__ - 1)
#define __INT64_C(c) c ## L
#define __SSP_STRONG__ 3
#define __GCC_ATOMIC_POINTER_LOCK_FREE 2
#define __ATOMIC_SEQ_CST 5
#define __unix 1
#define __INT_LEAST64_MAX__ 0x7fffffffffffffffL
#define __FLT32X_MANT_DIG__ 53
#define __GCC_ATOMIC_CHAR16_T_LOCK_FREE 2
#define __cpp_aligned_new 201606L
#define __FLT32_MAX_10_EXP__ 38
#define __FLT64X_EPSILON__ 1.08420217248550443400745280086994171e-19F64x
#define __STDC_HOSTED__ 1
#define __DEC64_MIN_EXP__ (-382)
#define __cpp_decltype_auto 201304L
#define __DBL_DIG__ 15
#define __FLT_EPSILON__ 1.19209289550781250000000000000000000e-7F
#define __GXX_WEAK__ 1
#define __SHRT_WIDTH__ 16
#define __FLT32_IS_IEC_60559__ 1
#define __LDBL_MIN__ 3.36210314311209350626267781732175260e-4932L
#define __DBL_IS_IEC_60559__ 1
#define __DEC32_MAX__ 9.999999E96DF
#define __cpp_threadsafe_static_init 200806L
#define __cpp_enumerator_attributes 201411L
#define __FLT64X_DENORM_MIN__ 3.64519953188247460252840593361941982e-4951F64x
#define __FLT32X_HAS_INFINITY__ 1
#define __unix__ 1
#define __INT_WIDTH__ 32
#define __STDC_IEC_559__ 1
#define __STDC_ISO_10646__ 201706L
#define __DECIMAL_DIG__ 21
#define __STDC_IEC_559_COMPLEX__ 1
#define __FLT64_EPSILON__ 2.22044604925031308084726333618164062e-16F64
#define __gnu_linux__ 1
#define __INT16_MAX__ 0x7fff
#define __FLT64_MIN_EXP__ (-1021)
#define __FLT64X_MIN_10_EXP__ (-4931)
#define __LDBL_HAS_QUIET_NAN__ 1
#define __cpp_return_type_deduction 201304L
#define __FLT16_MIN_EXP__ (-13)
#define __FLT64_MANT_DIG__ 53
#define __FLT64X_MANT_DIG__ 64
#define __BFLT16_DIG__ 2
#define __GNUC__ 13
#define __GXX_RTTI 1
#define __pie__ 2
#define __MMX__ 1
#define __FLT_HAS_DENORM__ 1
#define __SIZEOF_LONG_DOUBLE__ 16
#define __BIGGEST_ALIGNMENT__ 16
#define __STDC_UTF_16__ 1
#define __FLT64_MAX_10_EXP__ 308
#define __BFLT16_IS_IEC_60559__ 0
#define __FLT16_MAX_10_EXP__ 4
#define __cpp_delegating_constructors 200604L
#define __DBL_MAX__ double(1.79769313486231570814527423731704357e+308L)
#define __cpp_raw_strings 200710L
#define __INT_FAST32_MAX__ 0x7fffffffffffffffL
#define __DBL_HAS_INFINITY__ 1
#define __INT64_MAX__ 0x7fffffffffffffffL
#define __SIZEOF_FLOAT__ 4
#define __HAVE_SPECULATION_SAFE_VALUE 1
#define __cpp_fold_expressions 201603L
#define __DEC32_MIN_EXP__ (-94)
#define __INTPTR_WIDTH__ 64
#define __UINT_LEAST32_MAX__ 0xffffffffU
#define __FLT32X_HAS_DENORM__ 1
#define __INT_FAST16_TYPE__ long int
#define __MMX_WITH_SSE__ 1
#define __LDBL_HAS_DENORM__ 1
#define __SEG_GS 1
#define __BFLT16_EPSILON__ 7.81250000000000000000000000000000000e-3BF16
#define __cplusplus 201703L
#define __cpp_ref_qualifiers 200710L
#define __DEC32_MIN__ 1E-95DF
#define __DEPRECATED 1
#define __cpp_rvalue_references 200610L
#define __DBL_MAX_EXP__ 1024
#define __WCHAR_WIDTH__ 32
#define __FLT32_MAX__ 3.40282346638528859811704183484516925e+38F32
#define __DEC128_EPSILON__ 1E-33DL
#define __FLT16_DECIMAL_DIG__ 5
#define __SSE2_MATH__ 1
#define __ATOMIC_HLE_RELEASE 131072
#define __PTRDIFF_MAX__ 0x7fffffffffffffffL
#define __amd64 1
#define __ATOMIC_HLE_ACQUIRE 65536
#define __GNUG__ 13
#define __LONG_LONG_MAX__ 0x7fffffffffffffffLL
#define __SIZEOF_SIZE_T__ 8
#define __BFLT16_HAS_INFINITY__ 1
#define __FLT64X_MIN_EXP__ (-16381)
#define __SIZEOF_WINT_T__ 4
#define __FLT32X_DIG__ 15
#define __LONG_LONG_WIDTH__ 64
#define __cpp_initializer_lists 200806L
#define __FLT32_MAX_EXP__ 128
#define __cpp_hex_float 201603L
#define __GXX_ABI_VERSION 1018
#define __FLT_MIN_EXP__ (-125)
#define __GCC_HAVE_DWARF2_CFI_ASM 1
#define __x86_64 1
#define __cpp_lambdas 200907L
#define __INT_FAST64_TYPE__ long int
#define __BFLT16_MAX__ 3.38953138925153547590470800371487867e+38BF16
#define __FLT64_DENORM_MIN__ 4.94065645841246544176568792868221372e-324F64
#define __cpp_template_auto 201606L
#define __FLT16_DENORM_MIN__ 5.96046447753906250000000000000000000e-8F16
#define __FLT128_EPSILON__ 1.92592994438723585305597794258492732e-34F128
#define __FLT64X_NORM_MAX__ 1.18973149535723176502126385303097021e+4932F64x
#define __SIZEOF_POINTER__ 8
#define __SIZE_TYPE__ long unsigned int
#define __LP64__ 1
#define __DBL_HAS_QUIET_NAN__ 1
#define __FLT32X_EPSILON__ 2.22044604925031308084726333618164062e-16F32x
#define __LDBL_MAX_EXP__ 16384
#define __DECIMAL_BID_FORMAT__ 1
#define __FLT64_MIN_10_EXP__ (-307)
#define __FLT16_MIN_10_EXP__ (-4)
#define __FLT64X_DECIMAL_DIG__ 21
#define __DEC128_MIN__ 1E-6143DL
#define __REGISTER_PREFIX__
#define __UINT16_MAX__ 0xffff
#define __FLT128_HAS_INFINITY__ 1
#define __FLT32_MIN__ 1.17549435082228750796873653722224568e-38F32
#define __UINT8_TYPE__ unsigned char
#define __FLT_DIG__ 6
#define __NO_INLINE__ 1
#define __DEC_EVAL_METHOD__ 2
#define __FLT_MANT_DIG__ 24
#define __LDBL_DECIMAL_DIG__ 21
#define __VERSION__ "13.3.0"
#define __UINT64_C(c) c ## UL
#define __cpp_unicode_characters 201411L
#define _STDC_PREDEF_H 1
#define __INT_LEAST32_MAX__ 0x7fffffff
#define __GCC_ATOMIC_INT_LOCK_FREE 2
#define __FLT128_MAX_EXP__ 16384
#define __FLT32_MANT_DIG__ 24
#define __FLOAT_WORD_ORDER__ __ORDER_LITTLE_ENDIAN__
#define __FLT32X_MIN_EXP__ (-1021)
#define __STDC_IEC_60559_COMPLEX__ 201404L
#define __cpp_aggregate_bases 201603L
#define __BFLT16_MIN__ 1.17549435082228750796873653722224568e-38BF16
#define __FLT128_HAS_DENORM__ 1
#define __FLT32_DECIMAL_DIG__ 9
#define __FLT128_DIG__ 33
#define __INT32_C(c) c
#define __DEC64_EPSILON__ 1E-15DD
#define __ORDER_PDP_ENDIAN__ 3412
#define __DEC128_MIN_EXP__ (-6142)
#define __DEC128_MAX__ 9.999999999999999999999999999999999E6144DL
#define __INT_FAST32_TYPE__ long int
#define __UINT_LEAST16_TYPE__ short unsigned int
#define __DEC128_MAX_EXP__ 6145
#define unix 1
#define __DBL_HAS_DENORM__ 1
#define __cpp_rtti 199711L
#define __UINT64_MAX__ 0xffffffffffffffffUL
#define __FLT_IS_IEC_60559__ 1
#define __GNUC_WIDE_EXECUTION_CHARSET_NAME "UTF-32LE"
#define __FLT64X_DIG__ 18
#define __INT8_TYPE__ signed char
#define __cpp_digit_separators 201309L
#define __ELF__ 1
#define __GCC_ASM_FLAG_OUTPUTS__ 1
#define __UINT32_TYPE__ unsigned int
#define __BFLT16_HAS_QUIET_NAN__ 1
#define __FLT_RADIX__ 2
#define __INT_LEAST16_TYPE__ short int
#define __LDBL_EPSILON__ 1.08420217248550443400745280086994171e-19L
#define __UINTMAX_C(c) c ## UL
#define __FLT16_DIG__ 3
#define __k8 1
#define __FLT32X_MIN__ 2.22507385850720138309023271733240406e-308F32x
#define __SIG_ATOMIC_MAX__ 0x7fffffff
#define __cpp_constexpr 201603L
#define __GCC_ATOMIC_WCHAR_T_LOCK_FREE 2
#define __USER_LABEL_PREFIX__
#define __STDC_IEC_60559_BFP__ 201404L
#define __SIZEOF_PTRDIFF_T__ 8
#define __FLT64X_HAS_INFINITY__ 1
#define __SIZEOF_LONG__ 8
#define __LDBL_DIG__ 18
#define __FLT64_IS_IEC_60559__ 1
#define __x86_64__ 1
#define __FLT16_IS_IEC_60559__ 1
#define __FLT16_MAX_EXP__ 16
#define __DEC32_SUBNORMAL_MIN__ 0.000001E-95DF
#define __INT_FAST16_MAX__ 0x7fffffffffffffffL
#define __GCC_CONSTRUCTIVE_SIZE 64
#define __FLT64_DIG__ 15
#define __UINT_FAST32_MAX__ 0xffffffffffffffffUL
#define __UINT_LEAST64_TYPE__ long unsigned int
#define __FLT16_EPSILON__ 9.76562500000000000000000000000000000e-4F16
#define __FLT_HAS_QUIET_NAN__ 1
#define __FLT_MAX_10_EXP__ 38
#define __LONG_MAX__ 0x7fffffffffffffffL
#define __FLT64X_HAS_DENORM__ 1
#define __DEC128_SUBNORMAL_MIN__ 0.000000000000000000000000000000001E-6143DL
#define __FLT_HAS_INFINITY__ 1
#define __GNUC_EXECUTION_CHARSET_NAME "UTF-8"
#define __cpp_unicode_literals 200710L
#define __UINT_FAST16_TYPE__ long unsigned int
#define __DEC64_MAX__ 9.999999999999999E384DD
#define __INT_FAST32_WIDTH__ 64
#define __CHAR16_TYPE__ short unsigned int
#define __PRAGMA_REDEFINE_EXTNAME 1
#define __SIZE_WIDTH__ 64
#define __SEG_FS 1
#define __INT_LEAST16_MAX__ 0x7fff
#define __FLT16_NORM_MAX__ 6.55040000000000000000000000000000000e+4F16
#define __DEC64_MANT_DIG__ 16
#define __FLT32_DENORM_MIN__ 1.40129846432481707092372958328991613e-45F32
#define __SIG_ATOMIC_WIDTH__ 32
#define __INT_LEAST64_TYPE__ long int
#define __INT16_TYPE__ short int
#define __INT_LEAST8_TYPE__ signed char
#define __FLT16_MAX__ 6.55040000000000000000000000000000000e+4F16
#define __FLT128_MIN__ 3.36210314311209350626267781732175260e-4932F128
#define __cpp_structured_bindings 201606L
#define __SIZEOF_INT__ 4
#define __DEC32_MAX_EXP__ 97
#define __INT_FAST8_MAX__ 0x7f
#define __FLT128_MAX__ 1.18973149535723176508575932662800702e+4932F128
#define __INTPTR_MAX__ 0x7fffffffffffffffL
#define __cpp_sized_deallocation 201309L
#define __cpp_guaranteed_copy_elision 201606L
#define linux 1
#define __FLT64_HAS_QUIET_NAN__ 1
#define __FLT32_MIN_10_EXP__ (-37)
#define __EXCEPTIONS 1
#define __UINT16_C(c) c
#define __PTRDIFF_WIDTH__ 64
#define __LDBL_MANT_DIG__ 64
#define __cpp_range_based_for 201603L
#define __INT_FAST16_WIDTH__ 64
#define __FLT64_HAS_INFINITY__ 1
#define __FLT64X_MAX__ 1.18973149535723176502126385303097021e+4932F64x
#define __FLT16_HAS_INFINITY__ 1
#define __STDCPP_DEFAULT_NEW_ALIGNMENT__ 16
#define __SIG_ATOMIC_MIN__ (-__SIG_ATOMIC_MAX__ - 1)
#define __code_model_small__ 1
#define __GCC_ATOMIC_LONG_LOCK_FREE 2
#define __cpp_nontype_template_args 201411L
#define __DEC32_MANT_DIG__ 7
#define __k8__ 1
#define __INTPTR_TYPE__ long int
#define __UINT16_TYPE__ short unsigned int
#define __WCHAR_TYPE__ int
#define __pic__ 2
#define __UINTPTR_MAX__ 0xffffffffffffffffUL
#define __INT_FAST64_WIDTH__ 64
#define __cpp_decltype 200707L
#define __INT_FAST64_MAX__ 0x7fffffffffffffffL
#define __GCC_ATOMIC_TEST_AND_SET_TRUEVAL 1
#define __FLT_NORM_MAX__ 3.40282346638528859811704183484516925e+38F
#define __FLT32_HAS_INFINITY__ 1
#define __FLT64X_MAX_EXP__ 16384
#define __UINT_FAST64_TYPE__ long unsigned int
#define __cpp_inline_variables 201606L
#define __BFLT16_MIN_EXP__ (-125)
#define __INT_MAX__ 0x7fffffff
#define __linux__ 1
#define __INT64_TYPE__ long int
#define __FLT_MAX_EXP__ 128
#define __ORDER_BIG_ENDIAN__ 4321
#define __DBL_MANT_DIG__ 53
#define __cpp_inheriting_constructors 201511L
#define __SIZEOF_FLOAT128__ 16
#define __BFLT16_MANT_DIG__ 8
#define __DEC64_MIN__ 1E-383DD
#define __WINT_TYPE__ unsigned int
#define __UINT_LEAST32_TYPE__ unsigned int
#define __SIZEOF_SHORT__ 2
#define __FLT32_NORM_MAX__ 3.40282346638528859811704183484516925e+38F32
#define __SSE__ 1
#define __LDBL_MIN_EXP__ (-16381)
#define __FLT64_MAX__ 1.79769313486231570814527423731704357e+308F64
#define __amd64__ 1
#define __WINT_WIDTH__ 32
#define __INT_LEAST64_WIDTH__ 64
#define __FLT32X_MAX_10_EXP__ 308
#define __cpp_namespace_attributes 201411L
#define __SIZEOF_INT128__ 16
#define __FLT16_MIN__ 6.10351562500000000000000000000000000e-5F16
#define __FLT64X_IS_IEC_60559__ 1
#define __LDBL_MAX_10_EXP__ 4932
#define __ATOMIC_RELAXED 0
#define __DBL_EPSILON__ double(2.22044604925031308084726333618164062e-16L)
#define __INT_LEAST32_TYPE__ int
#define _LP64 1
#define __UINT8_C(c) c
#define __FLT64_MAX_EXP__ 1024
#define __SIZEOF_WCHAR_T__ 4
#define __GNUC_PATCHLEVEL__ 0
#define __FLT128_NORM_MAX__ 1.18973149535723176508575932662800702e+4932F128
#define __FLT64_NORM_MAX__ 1.79769313486231570814527423731704357e+308F64
#define __FLT128_HAS_QUIET_NAN__ 1
#define __INTMAX_MAX__ 0x7fffffffffffffffL
#define __INT_FAST8_TYPE__ signed char
#define __FLT64X_MIN__ 3.36210314311209350626267781732175260e-4932F64x
#define __STDCPP_THREADS__ 1
#define __BFLT16_HAS_DENORM__ 1
#define __GNUC_STDC_INLINE__ 1
#define __FLT64_HAS_DENORM__ 1
#define __FLT32_EPSILON__ 1.19209289550781250000000000000000000e-7F32
#define __FLT16_HAS_DENORM__ 1
#define __DBL_DECIMAL_DIG__ 17
#define __STDC_UTF_32__ 1
#define __INT_FAST8_WIDTH__ 8
#define __FXSR__ 1
#define __FLT32X_MAX__ 1.79769313486231570814527423731704357e+308F32x
#define __DBL_NORM_MAX__ double(1.79769313486231570814527423731704357e+308L)
#define __BYTE_ORDER__ __ORDER_LITTLE_ENDIAN__
#define __GCC_DESTRUCTIVE_SIZE 64
#define __INTMAX_WIDTH__ 64
#define __cpp_runtime_arrays 198712L
#define __FLT32_DIG__ 6
#define __UINT64_TYPE__ long unsigned int
#define __UINT32_C(c) c ## U
#define __cpp_alias_templates 200704L
#define __FLT_DENORM_MIN__ 1.40129846432481707092372958328991613e-45F
#define __FLT128_IS_IEC_60559__ 1
#define __INT8_MAX__ 0x7f
#define __LONG_WIDTH__ 64
#define __DBL_MIN__ double(2.22507385850720138309023271733240406e-308L)
#define __PIC__ 2
#define __INT32_MAX__ 0x7fffffff
#define __UINT_FAST32_TYPE__ long unsigned int
#define __FLT16_MANT_DIG__ 11
#define __FLT32X_NORM_MAX__ 1.79769313486231570814527423731704357e+308F32x
#define __CHAR32_TYPE__ unsigned int
#define __FLT_MAX__ 3.40282346638528859811704183484516925e+38F
#define __SSE2__ 1
#define __cpp_deduction_guides 201703L
#define __BFLT16_NORM_MAX__ 3.38953138925153547590470800371487867e+38BF16
#define __INT32_TYPE__ int
#define __SIZEOF_DOUBLE__ 8
#define __cpp_exceptions 199711L
#define __FLT_MIN_10_EXP__ (-37)
#define __FLT64_MIN__ 2.22507385850720138309023271733240406e-308F64
#define __INT_LEAST32_WIDTH__ 32
#define __INTMAX_TYPE__ long int
#define __GLIBCXX_BITSIZE_INT_N_0 128
#define __FLT32X_HAS_QUIET_NAN__ 1
#define __ATOMIC_CONSUME 1
#define __GNUC_MINOR__ 3
#define __GLIBCXX_TYPE_INT_N_0 __int128
#define __UINTMAX_MAX__ 0xffffffffffffffffUL
#define __PIE__ 2
#define __FLT32X_DENORM_MIN__ 4.94065645841246544176568792868221372e-324F32x
#define __cpp_template_template_args 201611L
#define __DBL_MAX_10_EXP__ 308
#define __LDBL_DENORM_MIN__ 3.64519953188247460252840593361941982e-4951L
#define __INT16_C(c) c
#define __STDC__ 1
#define __PTRDIFF_TYPE__ long int
#define __FLT32X_MIN_10_EXP__ (-307)
#define __UINTPTR_TYPE__ long unsigned int
#define __DEC64_SUBNORMAL_MIN__ 0.000000000000001E-383DD
#define __DEC128_MANT_DIG__ 34
#define __LDBL_MIN_10_EXP__ (-4931)
#define __cpp_generic_lambdas 201304L
#define __SSE_MATH__ 1
#define __SIZEOF_LONG_LONG__ 8
#define __cpp_user_defined_literals 200809L
#define __FLT128_DECIMAL_DIG__ 36
#define __GCC_ATOMIC_LLONG_LOCK_FREE 2
#define __FLT32_HAS_QUIET_NAN__ 1
#define __FLT_DECIMAL_DIG__ 9
#define __UINT_FAST16_MAX__ 0xffffffffffffffffUL
#define __LDBL_NORM_MAX__ 1.18973149535723176502126385303097021e+4932L
#define __GCC_ATOMIC_SHORT_LOCK_FREE 2
#define __SIZE_MAX__ 0xffffffffffffffffUL
#define __UINT_FAST8_TYPE__ unsigned char
#define _GNU_SOURCE 1
#define __cpp_init_captures 201304L
#define __ATOMIC_ACQ_REL 4
#define __ATOMIC_RELEASE 3
```
##  预处理
预处理：展开#include，替换#define，处理#ifdef

###  头文件引用树 -H
- 用法：g++ -H 文件名
- 示例：
``` 
 g++ -H simple.cpp                                                             (base)
. /usr/include/stdio.h
.. /usr/include/x86_64-linux-gnu/bits/libc-header-start.h
... /usr/include/features.h
.... /usr/include/features-time64.h
..... /usr/include/x86_64-linux-gnu/bits/wordsize.h
..... /usr/include/x86_64-linux-gnu/bits/timesize.h
...... /usr/include/x86_64-linux-gnu/bits/wordsize.h
.... /usr/include/x86_64-linux-gnu/sys/cdefs.h
..... /usr/include/x86_64-linux-gnu/bits/wordsize.h
..... /usr/include/x86_64-linux-gnu/bits/long-double.h
.... /usr/include/x86_64-linux-gnu/gnu/stubs.h
..... /usr/include/x86_64-linux-gnu/gnu/stubs-64.h
.. /usr/lib/gcc/x86_64-linux-gnu/13/include/stddef.h
.. /usr/lib/gcc/x86_64-linux-gnu/13/include/stdarg.h
.. /usr/include/x86_64-linux-gnu/bits/types.h
... /usr/include/x86_64-linux-gnu/bits/wordsize.h
... /usr/include/x86_64-linux-gnu/bits/timesize.h
.... /usr/include/x86_64-linux-gnu/bits/wordsize.h
... /usr/include/x86_64-linux-gnu/bits/typesizes.h
... /usr/include/x86_64-linux-gnu/bits/time64.h
.. /usr/include/x86_64-linux-gnu/bits/types/__fpos_t.h
... /usr/include/x86_64-linux-gnu/bits/types/__mbstate_t.h
.. /usr/include/x86_64-linux-gnu/bits/types/__fpos64_t.h
.. /usr/include/x86_64-linux-gnu/bits/types/__FILE.h
.. /usr/include/x86_64-linux-gnu/bits/types/FILE.h
.. /usr/include/x86_64-linux-gnu/bits/types/struct_FILE.h
.. /usr/include/x86_64-linux-gnu/bits/types/cookie_io_functions_t.h
.. /usr/include/x86_64-linux-gnu/bits/stdio_lim.h
.. /usr/include/x86_64-linux-gnu/bits/floatn.h
... /usr/include/x86_64-linux-gnu/bits/floatn-common.h
.... /usr/include/x86_64-linux-gnu/bits/long-double.h
Multiple include guards may be useful for:
/usr/include/features-time64.h
/usr/include/x86_64-linux-gnu/bits/libc-header-start.h
/usr/include/x86_64-linux-gnu/bits/time64.h
/usr/include/x86_64-linux-gnu/bits/typesizes.h
/usr/include/x86_64-linux-gnu/gnu/stubs-64.h
/usr/include/x86_64-linux-gnu/gnu/stubs.h
/usr/lib/gcc/x86_64-linux-gnu/13/include/stddef.h
```

###  gcc的冗长模式：一网打尽 (-v)

- 命令：g++ -v 文件名
- 示例：
```shell
 g++ -v simple.cpp                                                             (base)
Using built-in specs.
COLLECT_GCC=g++
COLLECT_LTO_WRAPPER=/usr/libexec/gcc/x86_64-linux-gnu/13/lto-wrapper
OFFLOAD_TARGET_NAMES=nvptx-none:amdgcn-amdhsa
OFFLOAD_TARGET_DEFAULT=1
Target: x86_64-linux-gnu
Configured with: ../src/configure -v --with-pkgversion='Ubuntu 13.3.0-6ubuntu2~24.04' --with-bugurl=file:///usr/share/doc/gcc-13/README.Bugs --enable-languages=c,ada,c++,go,d,fortran,objc,obj-c++,m2 --prefix=/usr --with-gcc-major-version-only --program-suffix=-13 --program-prefix=x86_64-linux-gnu- --enable-shared --enable-linker-build-id --libexecdir=/usr/libexec --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --enable-bootstrap --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes
--with-default-libstdcxx-abi=new --enable-libstdcxx-backtrace --enable-gnu-unique-object --disable-vtable-verify --enable-plugin --enable-default-pie --with-system-zlib --enable-libphobos-checking=release --with-target-system-zlib=auto --enable-objc-gc=auto --enable-multiarch --disable-werror --enable-cet --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-offload-targets=nvptx-none=/build/gcc-13-fG75Ri/gcc-13-13.3.0/debian/tmp-nvptx/usr,amdgcn-amdhsa=/build/gcc-13-fG75Ri/gcc-13-13.3.0/debian/tmp-gcn/usr --enable-offload-defaulted --without-cuda-driver --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu --with-build-config=bootstrap-lto-lean --enable-link-serialization=2
Thread model: posix
Supported LTO compression algorithms: zlib zstd
gcc version 13.3.0 (Ubuntu 13.3.0-6ubuntu2~24.04)
COLLECT_GCC_OPTIONS='-v' '-shared-libgcc' '-mtune=generic' '-march=x86-64' '-dumpdir' 'a-'
 /usr/libexec/gcc/x86_64-linux-gnu/13/cc1plus -quiet -v -imultiarch x86_64-linux-gnu -D_GNU_SOURCE simple.cpp -quiet -dumpdir a- -dumpbase simple.cpp -dumpbase-ext .cpp -mtune=generic -march=x86-64 -version -fasynchronous-unwind-tables -fstack-protector-strong -Wformat
-Wformat-security -fstack-clash-protection -fcf-protection -o /tmp/cclUwaRn.s
GNU C++17 (Ubuntu 13.3.0-6ubuntu2~24.04) version 13.3.0 (x86_64-linux-gnu)
        compiled by GNU C version 13.3.0, GMP version 6.3.0, MPFR version 4.2.1, MPC version 1.3.1, isl version isl-0.26-GMP

GGC heuristics: --param ggc-min-expand=100 --param ggc-min-heapsize=131072
ignoring duplicate directory "/usr/include/x86_64-linux-gnu/c++/13"
ignoring nonexistent directory "/usr/local/include/x86_64-linux-gnu"
ignoring nonexistent directory "/usr/lib/gcc/x86_64-linux-gnu/13/include-fixed/x86_64-linux-gnu"
ignoring nonexistent directory "/usr/lib/gcc/x86_64-linux-gnu/13/include-fixed"
ignoring nonexistent directory "/usr/lib/gcc/x86_64-linux-gnu/13/../../../../x86_64-linux-gnu/include"
#include "..." search starts here:
#include <...> search starts here:
 /usr/include/c++/13
 /usr/include/x86_64-linux-gnu/c++/13
 /usr/include/c++/13/backward
 /usr/lib/gcc/x86_64-linux-gnu/13/include
 /usr/local/include
 /usr/include/x86_64-linux-gnu
 /usr/include
End of search list.
Compiler executable checksum: c81c05345ce537099dafd5580045814a
COLLECT_GCC_OPTIONS='-v' '-shared-libgcc' '-mtune=generic' '-march=x86-64' '-dumpdir' 'a-'
 as -v --64 -o /tmp/ccKEJBaY.o /tmp/cclUwaRn.s
GNU assembler version 2.42 (x86_64-linux-gnu) using BFD version (GNU Binutils for Ubuntu) 2.42
COMPILER_PATH=/usr/libexec/gcc/x86_64-linux-gnu/13/:/usr/libexec/gcc/x86_64-linux-gnu/13/:/usr/libexec/gcc/x86_64-linux-gnu/:/usr/lib/gcc/x86_64-linux-gnu/13/:/usr/lib/gcc/x86_64-linux-gnu/
LIBRARY_PATH=/usr/lib/gcc/x86_64-linux-gnu/13/:/usr/lib/gcc/x86_64-linux-gnu/13/../../../x86_64-linux-gnu/:/usr/lib/gcc/x86_64-linux-gnu/13/../../../../lib/:/lib/x86_64-linux-gnu/:/lib/../lib/:/usr/lib/x86_64-linux-gnu/:/usr/lib/../lib/:/usr/lib/gcc/x86_64-linux-gnu/13/../../../:/lib/:/usr/lib/
COLLECT_GCC_OPTIONS='-v' '-shared-libgcc' '-mtune=generic' '-march=x86-64' '-dumpdir' 'a.'
 /usr/libexec/gcc/x86_64-linux-gnu/13/collect2 -plugin /usr/libexec/gcc/x86_64-linux-gnu/13/liblto_plugin.so -plugin-opt=/usr/libexec/gcc/x86_64-linux-gnu/13/lto-wrapper -plugin-opt=-fresolution=/tmp/ccWd4zaK.res -plugin-opt=-pass-through=-lgcc_s -plugin-opt=-pass-through=-lgcc -plugin-opt=-pass-through=-lc -plugin-opt=-pass-through=-lgcc_s -plugin-opt=-pass-through=-lgcc --build-id --eh-frame-hdr -m elf_x86_64 --hash-style=gnu --as-needed -dynamic-linker /lib64/ld-linux-x86-64.so.2 -pie -z now -z relro /usr/lib/gcc/x86_64-linux-gnu/13/../../../x86_64-linux-gnu/Scrt1.o /usr/lib/gcc/x86_64-linux-gnu/13/../../../x86_64-linux-gnu/crti.o /usr/lib/gcc/x86_64-linux-gnu/13/crtbeginS.o -L/usr/lib/gcc/x86_64-linux-gnu/13 -L/usr/lib/gcc/x86_64-linux-gnu/13/../../../x86_64-linux-gnu -L/usr/lib/gcc/x86_64-linux-gnu/13/../../../../lib -L/lib/x86_64-linux-gnu -L/lib/../lib -L/usr/lib/x86_64-linux-gnu -L/usr/lib/../lib -L/usr/lib/gcc/x86_64-linux-gnu/13/../../.. /tmp/ccKEJBaY.o -lstdc++ -lm -lgcc_s -lgcc -lc -lgcc_s -lgcc /usr/lib/gcc/x86_64-linux-gnu/13/crtendS.o /usr/lib/gcc/x86_64-linux-gnu/13/../../../x86_64-linux-gnu/crtn.o
COLLECT_GCC_OPTIONS='-v' '-shared-libgcc' '-mtune=generic' '-march=x86-64' '-dumpdir' 'a.'
```

## 编译阶段
编译：将代码汇编成汇编代码，然后翻译成机器码

### 汇编显微镜(-S)

- 命令：g++ -S -fverbose-asm file1.cpp -o file2.s
- 解释：
    - -S:到生成汇编这一步
    - -fverbose-asm:它会把原本的 C++ 变量名、函数名作为注释写在汇编指令旁边。
- 示例：
```c
g++ -S -fverbose-asm simple.cpp -o simple.s
  21   │     movl    -8(%rbp), %eax  # b, tmp89
  22   │     addl    %edx, %eax  # tmp88, tmp87
  23   │     movl    %eax, -4(%rbp)  # tmp87, c
  24   │ # simple.cpp:5: }
  25   │     movl    $0, %eax    #, _4
  26   │     popq    %rbp    #
  27   │     .cfi_def_cfa 7, 8
  28   │     ret
  29   │     .cfi_endproc
  30   │ .LFE0:
  31   │     .size   main, .-main
  32   │     .ident  "GCC: (Ubuntu 13.3.0-6ubuntu2~24.04) 13.3.0"
  33   │     .section    .note.GNU-stack,"",@progbits
  34   │     .section    .note.gnu.property,"a"
  35   │     .align 8
  36   │     .long   1f - 0f
  37   │     .long   4f - 1f
  38   │     .long   5
  39   │ 0:
  40   │     .string "GNU"
  41   │ 1:
  42   │     .align 8
  43   │     .long   0xc0000002
  44   │     .long   3f - 2f
  45   │ 2:
  46   │     .long   0x3
  47   │ 3:
  48   │     .align 8
  49   │ 4:
```
    



## 链接阶段
C++ 支持**函数重载**（Overloading，即多个函数可以叫同一个名字，只要参数不同）。
为了让链接器（Linker）区分 `void foo(int)` 和 `void foo(double)`，编译器会把函数名改成一串独一无二的“乱码”。

*   `foo(int)`  可能变成 -> `_Z3fooi`
*   `foo(double)` 可能变成 -> `_Z3food`



### 1. `c++filt`：乱码翻译机

**它的作用**：把那串看不懂的“乱码”还原回人类能读懂的 C++ 函数名。

*   **场景**：
    你编译时报错了：
    `undefined reference to '_Z3addii'`
    你一脸懵逼，`_Z3addii` 是什么鬼？我代码里没写过这个啊。

*   **用法**：
    ```bash
    c++filt _Z3addii
    ```

*   **输出**：
    ```text
    add(int, int)
    ```
    **真相大白**：原来是你调用了 `add(int, int)` 这个函数，但是链接器找不到它的实现。

---

### 2. `nm`：透视眼（查看符号表）

**它的作用**：列出一个二进制文件（`.o` 目标文件, `.a` 静态库, `.so` 动态库, 或者可执行文件）里**到底包含哪些函数和变量**。

`nm` 是 "Name List" 的缩写。

*   **场景**：
    你明确知道自己写了 `add(int, int)`，并且也编译成了 `libmath.a`，但链接器死活报错说找不到（Undefined reference）。你想确认一下：**我的库文件里到底有没有把这个函数编译进去？**

*   **用法**：
    通常配合 `-C` 参数使用（`-C` 相当于自动调用了 `c++filt`，直接显示人话）。
    ```bash
    nm -C libmath.a
    ```

*   **输出解读（最关键的部分）**：
    输出会有三列：`[地址] [类型] [名字]`。你只需要关注中间的**类型字母**：

    *   **`T` (Text)**：**我有这个函数！**
        *   表示这个函数是在当前文件里**定义（实现了）**的。
        *   *例*：`0000000 T add(int, int)` -> 说明 `libmath.a` 里确实有 `add` 函数的实体。
    *   **`U` (Undefined)**：**我用到了它，但我没它！**
        *   表示当前文件调用了这个函数，但是没找到它的定义，需要**去别的地方链接**。
        *   *例*：`                 U printf` -> 说明你的代码用了 `printf`，链接器需要去系统库里找它。


## 优化阶段 (-fopt-info)

- 命令：
    - g++ -O3 -fopt-info-vec test.cpp
    - g++ -O3 -fopt-info-vec-missed test.cpp
    - g++ -O3 -fopt-info-inline test.cpp
    - g++ -O3 -fopt-info-all test.cpp
- 示例：
```bash
g++ -O3 -fopt-info-all simple.cpp                                             (base)
simple.cpp:7:14: note: Considering inline candidate auto add(int, int)/30.
simple.cpp:7:14: optimized:  Inlining auto add(int, int)/30 into int main()/32.
simple.cpp:6:14: note: Considering inline candidate auto add(int, int)/30.
simple.cpp:6:14: optimized:  Inlining auto add(int, int)/30 into int main()/32.
Unit growth for small function inlining: 7->7 (0%)

Inlined 2 calls, eliminated 1 functions

simple.cpp:3:40: note: ***** Analysis failed with vector mode VOID
simple.cpp:8:1: note: ***** Analysis failed with vector mode VOID
```


## 总结
### 极简速查表

| 场景              | 命令核心                      | 作用                                   |
| :---------------- | :---------------------------- | :------------------------------------- |
| **查环境/版本**   | `g++ -dM -E -x c++ /dev/null` | 打印预定义宏、版本号、平台信息         |
| **查头文件路径**  | `g++ -v -x c++ /dev/null`     | 显示 `#include` 搜索目录列表           |
| **查引用关系**    | `g++ -H ...`                  | 打印头文件包含树状图                   |
| **查汇编代码**    | `g++ -S -fverbose-asm ...`    | 生成带 C++ 变量注释的汇编代码          |
| **查库/对象符号** | `nm -C ...`                   | 查看 .o 或 .a 文件里有哪些函数(T/U)    |
| **解乱码符号**    | `c++filt ...`                 | 将 `_Z...` 还原为人类可读函数名        |
| **查优化细节**    | `g++ -fopt-info ...`          | 让编译器报告向量化/内联结果            |
| **查完整流水线**  | `g++ -v ...`                  | 显示预处理、编译、汇编、链接全过程指令 |