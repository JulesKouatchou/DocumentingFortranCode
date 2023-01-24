# Comparing ProTex, Doxygen and FORD

## Introduction

We are looking for the appropriate tool to automatically create documenation from the source code.
Through the dcumentation, we want to have a good understanding of all the public routines (API) and how they interact.
We expect the documentation to:

- Provide a description of the purpose and input/output arguments of the API.
- Provide a description of the module containing the API.

The tool creating the documentation needs to:

- Provide features for specially placed and formatted comments around modules/subroutines.
- Extract source code comments to gererate documentation
- Create directory structure and call graphs.
- Generate documentation that can be inspected without looking into the code (e.g. HTML-pages, PDF-document, etc.)
- Provide enough information on modules so that they can be reused without knowing the internal code details.
- Generate searchable and browsable documentation.

With such documentation, code developers and users should be able to have a description of the main components of the code and 
how those components interact together.

We are considering `ProTex`, `Doxygen` and `FORD`. 
We provide here a brief description of the three tools and compare their main features.

## [ProTex](http://wiki.seas.harvard.edu/geos-chem/index.php/Automatic_documentation_with_protex)

- ProTeX was developed by the NASA GMAO with the following goals in mind:
    - The documented code should be written in a text form: easily reproducible and readable by developers.
    - The documented code should be directly compilable: the documentation should be entirely contained as code comments.
    - Automatically produce documentation in LaTeX and HTML formats.
    - Ensure good software engineering practices: avoid the possibility that changes in the code results in inconsistencies in the documentation. 
- Consists of a Perl script which extracts the prologues (contain Fortran Comments) from Fortran source files and converts them into a LaTeX file.
- Users inserts markers (which indicate the start and end of certain regions of code) and keywords (to determine the mode or section the code is describing) into the code: `!BOP`/`!EOP`, `!BOC`/`!EOC`, `! !USES`, `! !TO DO:`, `! !SEE ALSO:`, etc. 
- The markers and keywords are used by the Perl script to extract documentation from the code.
- The main program, each function, subroutine, or module will include a prologue instrumented for use with the ProTeX auto-documentation script. The purpose is to describe what the code does.
- Within the prologues, user can include any LaTeX syntax, especially for mathematical equations.

Here is a sample Fortran code with ProTex prologues:

```fortran
!------------------------------------------------------------------------------
!BOP
!
#include "MAPL_ErrLog.h"
#include "unused_dummy.H"
program pfio_standalone_test
!
! !USES:
      use, intrinsic :: iso_fortran_env, only: REAL32
      use mpi
      use MAPL
      use pFIO_UnlimitedEntityMod
      use pFIO_ClientManagerMod, only: o_Clients
      ...
!
! !DESCRIPTION:
! Program to write out several records of 2D & 3D geolocated variables in a netCDF file.
! It mimics the prgramming steps of MAPL_Cap and can be used as reference to implement
! PFIO in a non-GEOS model.
!\\
!\\
! \textbf{Usage:}
!\\
!   If we reserve 2 haswell nodes (28 cores in each), want to run the model on 28 cores
!   and use 1 MultiGroup with 5 backend processes, then the execution command is:
!\\
!  \texttt{   mpiexec -np 56 pfio_MAPL_demo.x --npes_model 28 --oserver_type multigroup --nodes_output_server 1 --npes_backend_pernode 5}
!EOP
!------------------------------------------------------------------------------
!BOC
...
!EOC
!------------------------------------------------------------------------------
CONTAINS
!------------------------------------------------------------------------------
...
!------------------------------------------------------------------------------
!BOP
!
! !IROUTINE: decompose_dim
!
! !DESCRIPTION:
! For a given number of grid points along a dimension and a number of
! available processors for that diemsion, determine the number of
! grid points assigned to each processor.
!\\
!\\
! !INTERFACE:
!
      subroutine decompose_dim(dim_world, dim_array, num_procs )
!
! !INPUT PARAMETERS:
      integer, intent(in)  :: dim_world ! total number of grid points
      integer, intent(in)  :: num_procs ! number of processors
!
! !OUTPUT PARAMETERS:
      integer, intent(out) :: dim_array(0:num_procs-1)
!
! !LOCAL VARIABLES:
      integer ::   n, im, rm
!EOP
!------------------------------------------------------------------------------
!BOC
      ....
      end subroutine decompose_dim
!EOC
!------------------------------------------------------------------------------
BOP
!
! !IROUTINE: set_temperature
!
! !DESCRIPTION:
! Arbitrary set values for the temperature field as:
! $$ T_{i,j} = 2.0 \cos(2\pi \frac{lon_i}{\max(lon)})\times ( \cos(\pi \frac{lat_j}{\max(lat)}))^2$$
!\\
!\\
! !INTERFACE:
!
      subroutine set_temperature(var)
!
! !OUPUT PARAMETERS:
         real , intent(out) ::    var(i1:i2, j1:j2)
!
! !LOCAL VARIABLES:
         integer :: i, j
!EOP
!------------------------------------------------------------------------------
!BOC

         do j = j1, j2
            do i = i1,i2
               var(i,j) = 2.0 + cos(2.0*lons(i)/lon_max*PI) &
                                     *cos(lats(j)/lat_max*PI)   &
                                     *cos(lats(j)/lat_max*PI)
            enddo
         enddo

      end subroutine set_temperature
!EOC
!------------------------------------------------------------------------------
end program pfio_standalone_test
```

## [Doxygen](https://www.doxygen.nl/index.html)

- An automatic documentation tool.
- Flexible comment placement: Allows you to put documentation in the header file (before the declaration of an entity), source file (before the definition of an entity) or in a separate file.
- Supports pretty printing, call graph generation, man page generation, and LaTeX and HTML documentation files.
- Uses the dot tool of the Graphviz tool kit to generate include dependency graphs, collaboration diagrams, call graphs, directory structure graphs, and graphical class hierarchy graphs.
- The generated HTML documentation can be viewed by pointing a HTML browser to the index.html file in the html directory. For the best results a browser that supports cascading style sheets (CSS) should be used.
- You can type normal HTML tags in your documentation. Doxygen will convert them to their equivalent LaTeX, RTF, and man-page counterparts automatically.
- Allows inclusion of source code examples that are automatically cross-referenced with the documentation.
- Generated man pages that can be viewed using the man program. Note that there are some limitations to the capabilities of the man page format, so some information (like class diagrams, cross references and formulas) will be lost.
- Has problems with very Fortran specific constructs:
     - [interface](https://stackoverflow.com/questions/68968973/writing-doxygen-documentation-for-a-fortran-module-interface)
     - [`public/private` statements inside of types are not supported](https://github.com/doxygen/doxygen/issues/5273)


#### Examples of Doxygen Tags for Fortran Codes
- [Examples of Doxygen Tags in Fortran Code](https://selalib.github.io/doxygen.html)
- [Tutorial OOP(V): Documenting Fortran 2003 Classes](https://dannyvanpoucke.be/oop-fortran-tut6-en/)

#### Limitations
- Seems to lag a "bit" in respect to the more modern Fortran features.
- [how to get Doxygen to work with Fortran interface statements?](https://stackoverflow.com/questions/59970141/how-to-get-doxygen-to-work-with-fortran-interface-statements)
- [Writing Doxygen documentation for a Fortran module interface](https://stackoverflow.com/questions/68968973/writing-doxygen-documentation-for-a-fortran-module-interface)
    
#### Sample Projects using Doxygen
- [CDSLAB](https://cdslaborg.github.io/paramonte-kernel-doc/html/index.html)

## [FORD (Fortran Documenter)](https://github.com/cmacmackin/ford)

> The goal of FORD is to be able to reliably produce documentation for modern Fortran software which is informative and nice to look at. The documentation should be easy to write and non-obtrusive within the code. While it will never be as feature-rich as Doxygen, hopefully FORD will be able to provide a good alternative for documenting Fortran projects.

- An automatic documentation generator for modern (1990 onward) Fortran code.
- Was created to fixed the limitation of Doxygen to handle new features of Fortran.
- Able to extract information about variables, procedures (functions and subroutines), procedure arguments, derived types, programs, and modules from the source code.
- Source code can also be preprocessed;.
- Able to extract documentation from comments in the source code.
- Uses Markdown to type-set documentation.
- Able to use LaTeX (through [MathJax](http://www.mathjax.org/))
- Able to create a hierarchical set of pages containing general information, not associated with any particular part of the source code.
- Able to include the contents of other files within the documentation.
- Symbols (e.g. main code, modules, derived data types) are appropriately coloured.
- Provide links:
   - to download the source code.
   - to individual files, both in their raw form or in HTML with syntax highlighting.
   - between related parts of the software.
- Use configurable settings to generate documentation. The settings allow users to define which types of comments can be used for documentation.
- Can add GitHub (Bitbucket), Twitter and LinkedIn pages.
- Searchable documentation using Tipue Search which supports a wide range of Web browsers. Can search source code as well as procedures, variables, comments, etc.

FORD usage is based on projects. A project is just whatever piece of software you want to document. Normally it would either be a program or a library. Each project will have its own [Markdown](https://daringfireball.net/projects/markdown/syntax) file which contains a description of the project.  Various [options](https://github.com/Fortran-FOSS-Programmers/ford/wiki/Project-File-Options) can be specified in this file, such as where to look for your projects source files, where to output the documentation, and information about the author. Some non-Markdown syntax can also be used with FORD.

Much like in Doxygen, you can use a `@note` environment to place the succeeding documentation into a special boxed paragraph. 
This syntax may be used at any location in the documentation comment and it will include as the note's contents anything until 
the first use of `@endnote` (provided there are no new `@note` or other environments, described below, started before then). 
If no such `@endnote` tag can be found then the note's contents will include until the end of the paragraph where the environment was activated. Other environments which behave the same way are `@warning`, `@todo`, and `@bug`.

As of version 4.5.0, FORD now offers limited support for non-Fortran source files. While it will not analyze the code within such files, it can extract documentation for the file as a whole and display it on its own page, as done for Fortran source files. An attempt will also be made to apply syntax highlighting to the contents of the file (although this may fail if non-standard file extensions are used). This may be useful for documenting build scripts or C wrappers.

#### Examples of FORD Tags
- [GS 2 Writing Documentation](https://gyrokinetics.gitlab.io/gs2/page/developer_manual/writing_documentation.html)
- [Using Ford to document Fortran programs](https://blog.hpc.qmul.ac.uk/using-ford-to-document-fortran-programs.html)

#### Limitations
- Ignores FORTRAN 77 data and equivalence statements.
- Does not recognise implicit types. It is important to always use `implicit none` in your code 
- Only recognises UTF-8 character encoding. Characters with accents in comments will cause FORD to crash.
- Can only use the GNU Fortran preprocessor

FORD promises that over time, some or all of these issues may be fixed.

#### Sample Projects using FORD

The page [Some Projects Using FORD](https://github.com/Fortran-FOSS-Programmers/ford/wiki) contains a list of projects that relay oon FORD for documentation.


## Where Doxygen and FORD Overlap

By conforming to the following style useful developer documentation may be created automatically using either FORD or Doxygen.

- Comments attached to program units, variables and derived types may be automatically documented.
- Documentation must precede the declaration of the unit, variable or derived type.
- Comments to be documented must use the tag `!>`. This is default tag in FORD for a comment preceding the content. In Doxygen, `!>` is the only tag which can both start and continue a comment. So this seems to be the best compromise to make the source compatible with both systems. FORD does not like inline comments.
- To insert a line break in a multiline comment use a blank comment line.
- Comments use markdown syntax for formatting.
- LaTeX style maths may be used to include equations in the documentation.

```fortran
!------------------------------------------------------------------------------
!># Standalone Program for Testing PFIO
!
! Program to write out several records of 2D & 3D geolocated variables in a netCDF file.
! It mimics the prgramming steps of MAPL_Cap and can be used as reference to implement
! PFIO in a non-GEOS model.
!
! @note
! `Usage`:
!
!   If we reserve 2 haswell nodes (28 cores in each), want to run the model on 28 cores
!   and use 1 MultiGroup with 5 backend processes, then the execution command is:
!
!       mpiexec -np 56 pfio_MAPL_demo.x --npes_model 28 --oserver_type multigroup --nodes_output_server 1 --npes_backend_pernode 5
! @endnote
!------------------------------------------------------------------------------
#include "MAPL_ErrLog.h"
#include "unused_dummy.H"

program pfio_standalone_test
      implicit none

      ! PARAMETERS:
      real,              PARAMETER ::          pfio_vmin = -MAPL_UNDEF
      real,              PARAMETER ::          pfio_vmax =  MAPL_UNDEF
      real,              PARAMETER :: pfio_missing_value =  MAPL_UNDEF
      real,              PARAMETER ::    pfio_fill_value =  MAPL_UNDEF
      real,           DIMENSION(2) ::   pfio_valid_range = (/-MAPL_UNDEF, MAPL_UNDEF/)
      integer,           parameter ::  MAX_STRING_LENGTH = 256
      real(kind=REAL64), parameter ::                 PI = 4.0d0*ATAN(1.0d0)
      integer,           parameter ::   num_time_records = 6
      integer,           parameter ::           num_dims = 2  !! number of dimension to decompose

      ! PFIO specific variables
      type(MAPL_FlapCLI)      :: cli
      type(MAPL_CapOptions)   :: cap_options
      type(ServerManager)     :: ioserver_manager
      type(SplitCommunicator) :: split_comm
      type(ArrayReference)    :: ref
      type(FileMetadata)      :: fmd
      Type(Variable)          :: v
      type(StringVariableMap) :: var_map
      ...     
!------------------------------------------------------------------------------
CONTAINS
!------------------------------------------------------------------------------
     ...
!------------------------------------------------------------------------------
!> For a given number of grid points and a number of available processors,
! this subroutines determines the number of grid points assigned to each
! processor.
      subroutine decompose_dim(dim_world, dim_array, num_procs )
!
      integer, intent(in)  :: dim_world         !! total number of grid points
      integer, intent(in)  :: num_procs         !! number of processors
      integer, intent(out) :: dim_array(0:num_procs-1)
      ...
      
      end subroutine decompose_dim    
!------------------------------------------------------------------------------
!> Arbitrary set values for the temperature field as:
! $$ T_{i,j} = 2.0 \cos(2\pi \frac{lon_i}{\max(lon)})\times ( \cos(\pi \frac{lat_j}{\max(lat)}))^2$$
   subroutine set_temperature(var)

      real , intent(out) ::    var(i1:i2, j1:j2)   !! Variable containing the temperature field.
      integer :: i, j

      do j = j1, j2
         do i = i1,i2
            var(i,j) =  2.0 + cos(2.0*lons(i)/lon_max*PI) &
                        *cos(lats(j)/lat_max*PI)*cos(lats(j)/lat_max*PI)
         enddo
      enddo

   end subroutine set_temperature
!------------------------------------------------------------------------------
end program pfio_standalone_test

```

## Comparison



| Features | ProTex | Doxygen | FORD |
| --- | --- | --- | --- |
| Supported languages | Fortran, C | Fortran, C/C++, Python, etc. | Fortran, C, Python, Shell, TeX |
| Fortran File Extension | .f .f90 .F90 | .f .for .f90 .f95 .F90 | .F .pf f90, f95, f03, f08, f15, F90, F95, F03, F08, F15 |
| Documentation Type | LaTex, HTML | LaTex, HTML, RTF| HTML |
| Platforms  | | Mac OS X, Linux, Windows | Mac OS X, Linux, Windows |
| Modern Fortran feautures? | no | no (making improvement) | yes |
| Call graph? | no | yes | yes |
| UML Class Diagram? | no | yes | yes |
| Create directory structure? | no | yes | yes |
| CMake Integration? | ? | yes | yes |
| Markdown support? | no | yes | yes |
| CSS Support? | no | yes | yes |
| Link to download source code | no | yes | yes |
| Links between related parts of the code | no | yes | yes |
| Use of aliases? | no | | yes |
| Use of environment variables | no | | yes |
| Configurable settings | no | yes | yes |

With the configurable options inside its project file, FORD is able to process Doxygen-styled comments which are before the documented object.

## Recommendations

This is my personal opinion. I would prefer FORD for the following reasons:


- FORD generates readable pages out of the box.
- Comments are written in Markdown.
    - We can use the existing README.md files that are already available for the MAPL component descriptions.
- The configurable settings allow users to define which comments will be used in the documentation.
- FORD has the capbility to:
    - Display a Python backtrace if an error is encountered when parsing a file.
    - Use multithreading to generate the documentation.
- With FORD, you can start using it on a program without modifying any source file, and then add inline documentation over time.
- With FORD, we can define simple and non-intrusive documentation standards for APIs.
- The use Doxygen will require more work for inline documentation.
- MAPL relies more on modern Fortran features.
    - FORD is aware of new Fortran features such as type-bound procedures, interfaces, etc, and is much nicer than Doxygen in that regard.
    - FORD is actively developed and focuses on modern Fortran. If we encounter a bug or limitation, we can contact the developers and receive the help we need.
