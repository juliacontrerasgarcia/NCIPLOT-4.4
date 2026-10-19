NCIPLOT is a program for revealing non covalent interactions based on the reduced density gradient. Please cite:

R. A. Boto, F. Peccati, R. Laplaza, C. Quan, A. Carbone, J.-P. Piquemal, Y. Maday, J. Contreras-Garcia\*, “NCIPLOT4: Fast, Robust, and Quantitative Analysis of Noncovalent Interactions”, J. Chem. Theory Comput. 16, 7, 4150 (2020)



Developers:

! Julia Conteras-Garcia <julia.contreras.garcia@gmail.com>,

! Erin R. Johnson <ejohnson29@ucmerced.edu>,

! A. Otero-de-la-Roza <aoterodelaroza@ucmerced.edu>

! Weitao Yang <weitao.yang@duke.edu>,

! Roberto A. Boto <robalboto@gmail.com>,

! Chaoyou Quan  <quanchaoyu@gmail.com>,

! Ruben Laplaza <rlaplaza@lct.jussieu.fr>

! Erna Wieduwilt <erna@sdu.dk>

! Katarzyna Zator <katarzyna.zator@sorbonne-universite.fr>



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

The division of the NCI region into clusters (CLUSTERING keyword, NCICLUSTER)
and the estimation of interaction energies (NCIENERGY keyword) are part of the
Fortran program (src\_4.4/nci\_cluster.f90 and src\_4.4/nci\_energy.f90). They
were previously Python scripts in scripts/; Python, conda and the packages of
environment.yml/requirements.txt are no longer needed. See CHANGES.md.

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

To rebuild everything with the debugging flags of Makefile.inc,

make debug

A Fortran compiler (gfortran by default, see Makefile.inc), make and the
xtb program (https://github.com/grimme-lab/xtb/releases, e.g.
xtb-6.7.1-linux-x86_64.tar.xz) are needed.

* xtb

make looks for xtb before compiling, in this order:

1. In the directory given by the XTB\_HOME environment variable, set the
   same way as NCIPLOT\_HOME (see below). XTB\_HOME is the directory of
   xtb, the one that has bin/xtb (xtb-dist/bin/xtb and xtb are also
   accepted):

   export XTB\_HOME=/home/xxxx/xtb-6.7.1-linux-x86_64/xtb-dist

2. If XTB\_HOME is not set, or has no xtb, in the src\_4.4/ directory:
   the xtb release unpacked there (for example
   src\_4.4/xtb-6.7.1-linux-x86_64/xtb-dist/bin/xtb), or xtb-dist/bin/xtb,
   xtb/bin/xtb, bin/xtb or the file xtb. To unpack it there:

   mkdir xtb-6.7.1-linux-x86_64
   tar -xJf xtb-6.7.1-linux-x86_64.tar.xz -C xtb-6.7.1-linux-x86_64

3. If xtb is in neither place, the compilation stops with

   ERROR: xtb missing.

   and says where it looked (and if an xtb archive that is not unpacked
   yet is in src\_4.4/).

make prints the xtb it uses ("xtb found (XTB\_HOME): ..."), and

make xtb

only does this check. The path is written to src\_4.4/xtb\_config.f90
(generated, do not edit) and built into nciplot, so XTB\_HOME is not needed
when nciplot is run. nciplot writes the xtb it uses at the start of the
output ("xtb : path"); with VERBOSE it also explains how it was found. If
XTB\_HOME is set when nciplot is run and has xtb, that xtb is used instead
of the one found at compilation. If the xtb found at compilation has been
moved, nciplot writes a warning: set XTB\_HOME or compile again.

NCIplot requires the NCIPLOT\_HOME environment variable to find the
atomic density files contained in the dat/ subdirectory. Set it to the
absolute path to NCIplot:

export NCIPLOT\_HOME=/home/xxxx/nciplot/

You may add the previous line (and the XTB\_HOME one) to your .bashrc or
.bash\_aliases file for convenience.

The code has been parallelized for shared-memory architectures using
the OpenMP library. To use this feature, set the OMP\_NUM\_THREADS
environment variable to the number of cores:

export OMP\_NUM\_THREADS=4

Several tests and examples are provided in the examples/
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
! - CUBE\_PARAM
! - ATCUBE
! - FRAGMENT
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
! - CLUSTERING
! - NCIENERGY
! - SUPRA
! - DENSWEIGHT
! - DGRID
! - VERBOSE
! - FINE
! - ULTRAFINE
! - COARSE
!===============================================================================!

* Clustering and energies

CLUSTERING divides the NCI region into clusters, one around each minimum
(critical point, CP) of the RDG, and integrates rho^n (n = 1, 1.5, 2, 2.5, 3,
4/3, 5/3) and the volume of each cluster in each RANGE interval (in -0.20
-0.02 if RANGE is not given). It writes oname\_CPs.xyz (positions of the CPs,
in angstrom) and, with OUTPUT 2 or 3, the cube files oname-clN-dens.cube and
oname-clN-grad.cube of each cluster N and the VMD script oname\_divided.vmd.

NCIENERGY estimates the interaction energy (kJ/mol) from the integrals of the
polar (-0.20 -0.02) and van der Waals (-0.02 0.02) ranges: for the whole
system, or cluster by cluster if CLUSTERING is also given (with dedicated
equations for sigma-hole contacts). SUPRA selects the equations for
supramolecular systems. The equations were fitted with the default
parameters, so NCIENERGY needs:

RDG\_CUTOFF 1.00 0.30
INTEGRATE
INTERMOLECULAR
INTERMOL\_CUTOFF 0.85   (0.75 for wfn/wfx)
RANGE 3
-0.20 -0.02
-0.02  0.02
 0.02  0.20

Have fun!

