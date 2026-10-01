* NCIPLOT is a program for revealing non covalent interactions based on the reduced density gradient. Please cite:
R. A. Boto, F. Peccati, R. Laplaza, C. Quan, A. Carbone, J.-P. Piquemal, Y. Maday, J. Contreras-Garcia\*, “NCIPLOT4: Fast, Robust, and Quantitative Analysis of Noncovalent Interactions”, J. Chem. Theory Comput. 16, 7, 4150 (2020)

! Julia Conteras-Garcia <julia.contreras.garcia@gmail.com>,

! Erin R. Johnson <ejohnson29@ucmerced.edu>,

! A. Otero-de-la-Roza <aoterodelaroza@ucmerced.edu>

! Weitao Yang <weitao.yang@duke.edu>,

! Chaoyou Quan  <quanchaoyu@gmail.com>

! Kate Zator <k.zator97@gmail.com>

! Roberto A. Boto <[robalboto@gmail.co](mailto:robalboto@gmail.com)m>

* Overview

NCIPLOT is a program that enables graphical visualization of inter and intramolecular
non-covalent interactions (i.e. hydrogen bonds, steric clashes and van der Waals)
in systems ranging from small molecules to large biosystems.

NCI (Non-Covalent Interactions) is a visualization index based on the
electron density and its derivatives. It enables identification of non-covalent
interactions, based on the peaks that appear in the reduced density gradient (RDG)
at low densities. RDG isosurfaces for these peaks enable the visualization of weak
interactions. The isosurfaces correspond to both favorable and unfavorable interactions,
as differentiated by the sign of the second density Hessian eigenvalue. The sign of this
eigenvalue, times the density, is able to characterize both the strength and (un)favourable
nature of the interactions and defines the isosurface colouring.
See the reference below for a thorough explanation of the details.

This program reads a gaussian-style wfn or wfx file or a geometry xyz file and
computes density and reduced density gradient (RDG) on a grid. It can provide
cube-format cube files and VMD scripts for the direct visualization of the results.

* Compilation

To install nciplot, unpack or clone the contents of the distribution and cd
into the src\_4.4/ subdirectory. Change the Makefile.inc to suit your
compiler and flags, and do:

make mrproper
make

to build the nciplot executable. To clean the object and module files,

make clean

To clean objects, modules and binaries,

make mrproper

NCIplot requires the NCIPLOT\_HOME environment variable to find the
atomic density files contained in the dat/ subdirectory. Set it to the
absolute path to NCIplot:

export NCIPLOT\_HOME=/home/xxxx/nciplot/

You may add the previous line to your .bashrc or .bash\_aliases file for
convenience.

The code has been parallelized for shared-memory architectures using
the OpenMP library. To use this feature, set the OMP\_NUM\_THREADS
environment variable to the number of cores:

export OMP\_NUM\_THREADS=4

Several tests and examples are provided in the tests/
directory. The PDF manual is simply NCIPLOT\_MANUAL.pdf
which is shipped with the code. Only the usage of the code
suggested in the manual is recommended.

As a brief outline, the accepted keywords in this version are:
!===============================================================================!
! Read optional keywords.
! Accepted keywords in this version:
! - RTHRES
! - LIGAND
! - RADIUS
! - CG2FG
! - ONAME
! - INCREMENTS
! - OUTPUT
! - RDG\_CUTOFF
! - DENS\_CUTOFF
! - INTERMOLECULAR
! - INTERMOL\_CUTOFF
! - INTEGRATE
! - RANGE
! - NCICLUSTER
! - NCIENERGY
! - FINE
! - ULTRAFINE
! - COARSE
!===============================================================================!

Have fun!

