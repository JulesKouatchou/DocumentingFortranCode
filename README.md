# Comparing ProTEX, Doxygen and FORD

## Introduction

## ProTEX

## [Doxygen](https://www.doxygen.nl/index.html)

- An automatic documentation tool
- Supports pretty printing, call graph generation, man page generation, and LaTeX and HTML documentation files.

## [FORD (Fortran Documenter)](https://github.com/cmacmackin/ford)

- An automatic documentation generator for modern (1990 onward) Fortran code.
- Was created to fixed the limitation of Doxygen to handle new features of Fortran.
- Able to extract information about variables, procedures, procedure arguments, derived types, programs, and modules from the source code.
- Able to extract documentation from comments in the source code.
- Uses Markdown to type-set documentation.
- Able to use LaTeX (through [MathJax](http://www.mathjax.org/))
- Able to create a hiearchical set of pages containing general information, not associated with any particular part of the source code.
- Able to include the contents of other files within the documentation.
- Provide links:
   - to download the source code.
   - to individual files, both in their raw form or in HTML with syntax highlighting.
   - between related parts of the software.
- Use configurable settings to generate documentation.

## Where Doxygen and FORD Overlap

By conforming to the following style useful developer documentation may be created automatically using either FORD or Doxygen.

- Comments attached to program units, variables and derived types may be automatically documented.
- Documentation must precede the declaration of the unit, variable or derived type.
- Comments to be documented must use the tag `!>`. This is default tag in FORD for a comment preceding the content. In Doxygen, `!>` is the only tag which can both start and continue a comment. So this seems to be the best compromise to make the source compatible with both systems. FORD does not like inline comments.
- To insert a line break in a multiline comment use a blank comment line.
- Comments use markdown syntax for formatting.
- LaTeX style maths may be used to include equations in the documentation.

```fortran
!># Example
!>
!> Author - GMAO SI Team
!>
!> An example program
Program example
  Use kinds, Only : wi
  Implicit None

  !> Integer counter
  Integer( Kind = wi ) :: i

  ...

  !> Calculate the factorial of n
  Function fact(n)

  ...

```

## Comparison

| Features | ProTex | Doxygen | FORD |
| --- | --- | --- | --- |
| Supported languages | Fortran, C | Fortran, C/C++, Python, etc. | Fortran |
| Documentation Type | LaTex | LaTex, HTML | HTML |
| Fortran 2003 feautures? | no | no | yes |
| Call graph? | no | yes | yes |
