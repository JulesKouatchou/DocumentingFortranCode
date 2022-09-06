# Comparing ProTEX, Doxygen and FORD

## Introduction

We are looking for the appropriate tool to automatically documenation from the source code.
Through the dcumentation, we want to have a good understanding of all the public routines (API) and how they interact.
We expect the documentation to:

- Provide a description of the purpose and input/output arguments of the API.
- Provide a description of the module containg the API.

The tool creating the documentation needs to:

- Provide features for specially placed and formatted comments around modules/subroutines.
- Extract source code comments to gererate documenation
- Generate documentation that can be inspected without looking into the code (e.g. HTML-pages, PDF-document, etc.)
- Provide enough information on modules so that they can be reused without knowing the internal code details.
- Generate documentation in a variety of formats (specially browsable).

We are considering ProTex, Doxygen and FORD. 
We here provide a brief description of the three tools and compare their main features.

## [ProTEX](http://wiki.seas.harvard.edu/geos-chem/index.php/Automatic_documentation_with_protex)

- Perl script that can strip information from a standard Fortran document header and save that to a LaTeX file.


## [Doxygen](https://www.doxygen.nl/index.html)

- An automatic documentation tool
- Supports pretty printing, call graph generation, man page generation, and LaTeX and HTML documentation files.
- Has problems with very Fortran specific constructs (e.g. [interface](https://stackoverflow.com/questions/68968973/writing-doxygen-documentation-for-a-fortran-module-interface))

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

FORD usage is based on projects. A project is just whatever piece of software you want to document. Normally it would either be a program or a library. Each project will have its own [Markdown](https://daringfireball.net/projects/markdown/syntax) file which contains a description of the project.  Various [options](https://github.com/Fortran-FOSS-Programmers/ford/wiki/Project-File-Options) can be specified in this file, such as where to look for your projects source files, where to output the documentation, and information about the author. Some non-Markdown syntax can also be used with FORD.

Much like in Doxygen, you can use a `@note` environment to place the succeeding documentation into a special boxed paragraph. 
This syntax may be used at any location in the documentation comment and it will include as the note's contents anything until 
the first use of `@endnote` (provided there are no new @note or other environments, described below, started before then). 
If no such `@endnote` tag can be found then the note's contents will include until the end of the paragraph where the environment was activated. Other environments which behave the same way are `@warning`, `@todo`, and `@bug`.

As of version 4.5.0, FORD now offers limited support for non-Fortran source files. While it will not analyze the code within such files, it can extract documentation for the file as a whole and display it on its own page, as done for Fortran source files. An attempt will also be made to apply syntax highlighting to the contents of the file (although this may fail if non-standard file extensions are used). This may be useful for documenting build scripts or C wrappers.

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
| Supported languages | Fortran, C | Fortran, C/C++, Python, etc. | Fortran, C (limited) |
| Documentation Type | LaTex, HTML | LaTex, HTML | HTML |
| Fortran 2003 feautures? | no | no | yes |
| Call graph? | no | yes | yes |
