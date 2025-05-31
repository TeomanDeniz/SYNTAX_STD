# Syntax Standards

First of all, I should mention that I always use **SCREAMING_SNAKE_CASE** to naming variable/function/object while coding in every programming language. However, I will not use that in my examples here because it is a personal habit.

Habit: Readability on Low-Res Displays. UPPER_CASE was more legible on primitive screens and printouts.

Language list:

[C Language](#C-Language)

## C Language

I am always using my **[LIBCMT](https://github.com/TeomanDeniz/LIBCMT)** library while creating projects with using C Language.

Note: Check pre-defined macros if necessary in [here](https://sourceforge.net/p/predef/wiki/Compilers/).

The entire file must be written and could be fit within 80 characters in a single line (TABs counted as 4 characters).

If the code overflows 80 characters, then please use "\\" for shred the code.

### For macros and pre-processing commands

Always using TABs between `#` and `command` inside macro blocks.

And at the macros' `#endif`s: Always writing the statement itself in a comment line to 

```c
#ifdef ███_A
#	ifdef ███_B
#		ifndef ███_C
...
#		endif /* !███_C */
#	else /* REASON FOR ELSE IF NEEDED */
...
#	endif /* ███_B */
#endif /* ███_A */
```
or
```c
#if (███_A == 0)
#	if (defined(███_B) && ███_B == 42)
...
#	endif /* ███_B && ███_B == 42 */
#endif /* ███_A == 0 */
```
or
```c
#if (\
	███_A == 0 && \
	███_B == 0 && \
	███_C == 0 && \
	███_D == 0 && \
	███_E == 0 && \
	███_F == 0 && \
	(\
		███_F_1 == 1 || \
		███_F_1 == 2\
	)\
	███_G == 0\
)
...
#endif /* ███_A == 0 */
```

On header guards, while defining the guard macro, I am also including a date of the header in here for make more efficient controllability.

Only adding year and month in this place.
```c
#ifndef ███_H
#	define ███_H 202505 /* VERSION */
//              ^^^^^^
//              YYYYMM
...
#endif /* !███_H */
```

For supporting really old compilers, I am stritchly avoiding using macros like `#if`, `#elif`, `#line`, `#warning`, and `#pragma`.

But I am still using them if I am clearly sure that the compiler is totally supports it.

### Code Syntax

The order of syntax is as follows:

```ini
[MISRA RULE SETS (PUSH)]

[C++ (PUSH)]

[MVS LINKER]

[INCLUDES]

[MACROS]

[PROTOTYPES]

[STRUCTS]

[UNIONS]

[ENUMS]

[TYPEDEFS]

[GLOBAL VARIABLES (NOT PROTOTYPES)]

{CODE ITSELF}

[C++ (POP)]

[MISRA RULE SETS (POP)]
```

#### `[MISRA RULE SETS (PUSH)]`
[MISRA](https://misra.org.uk/) (Motor Industry Software Reliability Association) is a set of coding guidelines for the C and C++ languages, designed to improve safety, reliability, and maintainability, especially in embedded and critical systems.

For see the rules, check the [misra.txt](https://github.com/TeomanDeniz/SYNTAX_STD/blob/main/misra.txt).

Syntax:
```c
/* *********************** [v] TI CGT CCS (PUSH) [v] ************************ */
#ifdef __TI_COMPILER_VERSION__
#	pragma diag_push /* TI CGT CCS COMPILER DIRECTIVES */
#	pragma CHECK_MISRA("-5.4") /* TAG NAMES SHALL BE A UNIQUE IDENTIFIER */
#	pragma CHECK_MISRA("-19.3") /*
#		THE #INCLUDE DIRECTIVE SHALL BE FOLLOWED BY EITHER A <FILENAME> OR
#		"FILENAME" SEQUENCE
#	*/
#endif /* __TI_COMPILER_VERSION__ */
/* *********************** [^] TI CGT CCS (PUSH) [^] ************************ */
```

#### `[MISRA RULE SETS (POP)]`
Syntax:
```c
/* ************************ [v] TI CGT CCS (POP) [v] ************************ */
#ifdef __TI_COMPILER_VERSION__
#	pragma diag_pop /* TI CGT CCS COMPILER DIRECTIVES */
#endif /* __TI_COMPILER_VERSION__ */
/* ************************ [^] TI CGT CCS (POP) [^] ************************ */
```

#### `[C++ (PUSH)]`

Since it's C Language, user may want to use the code or header in a C++ compiler.

So make the user use the C features, we are setting an extern to achieve that.

```c
/* *************************** [v] C++ (PUSH) [v] *************************** */
#	ifdef __cplusplus /* C++ */
extern "C" {
#	endif /* __cplusplus */
/* *************************** [^] C++ (PUSH) [^] *************************** */
```

#### `[C++ (POP)]`

```c
/* *************************** [v] C++ (POP) [v] **************************** */
#	ifdef __cplusplus /* C++ */
}
#	endif /* __cplusplus */
/* *************************** [^] C++ (POP) [^] **************************** */
```

#### `[MVS LINKER]`

On IBM mainframe systems (e.g. z/OS), the MVS Linker imposes a strict limitation: external symbol names (like function names) must not exceed 8 characters. To maintain compatibility, we remap longer function names to short 8-character aliases using `#pragma map`.

```c
/* *************************** [v] MVS LINKER [v] *************************** */
/* **** MVS LINKER DOES NOT SUPPORT EXTERNAL NAMES LARGER THAN 8 BYTES!! **** */
// NOTE: TARGETING IBM MAINFRAME SYSTEMS (Z/OS)
#	ifdef __MVS__
#		pragma map(LONG_FUNCTION_NAME, "SHORTNM1")
#		pragma map(LONG_FUNCTION_NAME_2, "SHORTNM2")
...
#	endif /* __MVS__ */
/* *************************** [^] MVS LINKER [^] *************************** */
```

#### `[INCLUDES]`

I am writing the prototype/short version of what I am using from a header I am including. And I am writing only the prototypes of objects that I am used inside the file.

For example, If I used `printf()` in my C file, and I am included `<stdio.h>` for that, I am only writing `int printf(char *, ...);` on my comment line. Not the WHOLE stuffs `stdio.h` has.

And I am telling only the stuffs that I am using in my file. If I am using `malloc()` in my file, and the prototype is like `void *malloc(size_t);`, I am not mentioning `size_t` in `<stdlib.h>` comment line.

And there's multiple syntax versions for `#include <...>` macro because of the ways we are gonna use.

Info: `<...>` includes are going to be always at the top of `"..."` includes.

Sytnax:
```c
/* **************************** [v] INCLUDES [v] **************************** */
#include <stdio.h> /*
#    int printf(char *, ...);
#        */
#include "..." /*
# define ███
# define ███(__███_NAME__, __███_OTHER__)
# struct s_███;
#  union u_███;
#   enum e_███;
#    int g_███;
#typedef t_███;
#   void test(void);
#   void test2(char *);
#   void test3(uint);
#   void test4(uint, ushort, double, float, char, char *****, long, long,
#        	double, int, ...);
#>>>>>>> struct s_test
#^^^^^^^ test5(void);
#        */
/* **************************** [^] INCLUDES [^] **************************** */
```

#### [MACROS]

Macros are classic macros. Nothing specific about them.

```c
/* ***************************** [v] MACROS [v] ***************************** */
#define MACRO 42
/* ***************************** [^] MACROS [^] ***************************** */
```

#### [PROTOTYPES]

```
/* *************************** [v] PROTOTYPES [v] *************************** */
extern int			g_variable;
extern void			test_func(void);
extern void			test_func2(int, char *);
extern static int	static_func(void);
extern static int	really_long_static_func(double, double, double, double,
	double, double);
/* *************************** [^] PROTOTYPES [^] *************************** */
```

#### [STRUCTS]

```c
/* ***************************** [v] STRUCT [v] ***************************** */
struct s_test
{
	int	a;
};
/* ***************************** [^] STRUCT [^] ***************************** */
```

#### [UNIONS]

```c
/* ***************************** [v] UNIONS [v] ***************************** */
union u_test
{
	char	*a;
	long	b;
};
/* ***************************** [^] UNIONS [^] ***************************** */
```

#### [ENUMS]

```c
/* ****************************** [v] ENUM [v] ****************************** */
enum e_test
{
	TEST_1,
	TEST_2
};
/* ****************************** [^] ENUM [^] ****************************** */
```

#### [TYPEDEFS]

```c
/* **************************** [v] TYPEDEFS [v] **************************** */
typedef char			*t_string;
typedef struct s_test	*t_test;
/* **************************** [^] TYPEDEFS [^] **************************** */
```

#### [GLOBAL VARIABLES]

No, they are not prototypes. Just direct creation.

```c
/* ************************ [v] GLOBAL VARIABLES [v] ************************ */
int			g_var;
const int	g_var_wow = 42;
/* ************************ [^] GLOBAL VARIABLES [^] ************************ */
```

#### {CODE ITSELF}

While creating a function on C, for implement my function to both K&R and ANSI C syntax, I am using `KNR_STYLE` from **[KNR_STYLE.h](https://github.com/TeomanDeniz/LIBCMT/blob/main/ENVIRONMENTS/KNR_STYLE.h)**.

Note: On K&R version, please avoid using `const`, `restrict`, and other C89/C90 above syntaxes on the variables.

```c
#ifndef KNR_STYLE /* STANDARD C */
void
	FUNCTION(void)
#else /* K&R */
void
	FUNCTION()
#endif /* !KNR_STYLE */
{
...
}

#ifndef KNR_STYLE /* STANDARD C */
void
	FUNCTION2(int VAR)
#else /* K&R */
void
	FUNCTION2(VAR)
	int	VAR;
#endif /* !KNR_STYLE */
{
...
}
```

In scope, while creating a variable; Never create your variable on the middle of the scope unless it's a `const`.

And when created the variable, do not give it a value right after creation. (If it's not a `const` or `static` variable).

```c
{
	int				test;
	unsigned int	test;

	test = 42;
	...
}

{
	static int	test = 0;

	...
}
```

For variables that's only going to be used on a small area of the code, I am going to create a block to handle that local variable at it's place.

This is actually improves the readability and **compiling** performance.
```c
{
	int	test = 0;

	{
		register int	a_local_variable;

		a_local_variable = 42;
		...
	}

	...
}
```

Awlays create an extra space between blocks. I mean, I am putting spaces on outside of the blocks.

Even if it's an `if`, `while`, and `for` statement without blocks, you **must** put spaces around them!

```c
A;

{
	B;
	C;
}

D;
```

```c
A

if ()
	B;

C
```

Example:
```c
{
	int	a;

	a = 1;

	if (a)
	{
		if (a)
		{
			if (!a)
			{
				...
			}
			else
			{
				...
			}
		}

		a = 0;
	}

	if (a)
		a = 0;

	test(a);
}
```

I am generally avoiding using keywords in my codes like: `for`, `do->while`, `goto`, and `?: (ternary operator)` to make readibility more clear.

For `switch` & `case`, `return`, `break`, and `continue` syntaxes, I am using them in this style: (Note that I am also trying to avoid using `continue` keyword)

```c
switch (VAR)
{
	case (0):
	{
		...
	}
	break ;

	case (1):
	{
		...
	}
	break ;

	default:
	{
		...
	}
}

while (1)
{
	break ;
}

while (1)
{
	continue ;
}

return (VAR);
```
