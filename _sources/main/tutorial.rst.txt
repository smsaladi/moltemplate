.. _sec:tutorial:

Introductory tutorial
=====================

*Summary*
---------

*Moltemplate is based on a very simple text generator (wrapper) which
repetitively copies short text fragments into one (or more) files and
keeps track of various kinds of counters.*

LAMMPS is a powerful but complex program with many contributors.
Moltemplate is a front-end for LAMMPS. Moltemplate users will have to
tackle the same steep learning-curve (and occasional bugs) that other
LAMMPS users must face. Moltemplate files (LT files) share the same file
format and syntax structure as LAMMPS DATA files and INPUT scripts.
Moltemplate will attempt to correct user mistakes, however users must
still learn LAMMPS syntax and write LT files which obey it. For users
who are new to LAMMPS, the easiest way to do this is to modify an
existing example (such as the water box example in this section). (The
official LAMMPS documentation http://lammps.sandia.gov/doc/Manual.html
is an excellent reference to look up LAMMPS commands you see in these
examples that you are not familiar with.)

.. _sec:spce_example:

Simulating a box of water using moltemplate and LAMMPS
------------------------------------------------------

.. raw:: latex

   \centering

.. figure:: /_static/single_water_LR.jpg
   :alt:  single water molecule
   :width: 2.4cm

   Coordinates of a single water molecule in our example. (Atomic radii not to scale.) 

Here we show an example of a lammps-template file for water. (The
settings shown here are borrowed from the simple-point-charge
:cite:`BerendsenStraatsmaJPhysChem1987` SPC/E model.) In
addition to coordinates, topology and force-field settings, “LT” files
can optionally include any other kind of LAMMPS settings including SHAKE
constraints, k-space settings, and even group definitions.

::

   # (NOTE: Text following '#' characters are comments)
   #
   # file "spce_simple.lt" 
   #
   #    H1     H2
   #      \   /
   #        O
   #

   SPCE {

     ## Atom properties and molecular topology go in the various "Data ..." sections

     # We selected "atom_style full".  That means we use this column format:
     # atomID      molID         atomType  charge  coordX    coordY    coordZ

     write("Data Atoms") {
       $atom:o      $mol:.       @atom:O  -0.8476  0.0000000 0.000000  0.00000
       $atom:h1     $mol:.       @atom:H   0.4238  0.8164904 0.5773590 0.00000
       $atom:h2     $mol:.       @atom:H   0.4238  -0.8164904 0.5773590 0.00000
     }

     # Variables beginning with $ or @ will be replaced by numbers LAMMPS will
     # eventually read.  Each of the three atoms" will be assigned unique
     # atomIDs (denoted here by "$atom:o", "$atom:h1", "$atom:h2"), even if
     # they belong to different molecules.  However, the atom types
     # (denoted "@atom:O", "@atom:H") are shared for atoms in all molecules.
     # All 3 atoms share same molID number (represeted here by "$mol:.")
     # however that number is different for different water molecules.

     write_once("Data Masses") {
       # atomType  mass
       @atom:O    15.9994
       @atom:H    1.008
     }

     write("Data Bonds") {
       #  bondID  bondType  atomID1  atomID2
       $bond:oh1  @bond:OH  $atom:o  $atom:h1
       $bond:oh2  @bond:OH  $atom:o  $atom:h2
     }

     write("Data Angles") {
       # angleID  angleType  atomID1  atomID2 atomID3
       $angle:hoh @angle:HOH $atom:h1 $atom:o $atom:h2
     }

     # --- Force-field parameters go in the "In Settings" section: ---

     write_once("In Settings") {
       # -- Non-bonded (Pair) interactions --
       #          atomType1 atomType2  parameter-list (epsilon, sigma)
       pair_coeff  @atom:O  @atom:O    0.1553 3.166 
       pair_coeff  @atom:H  @atom:H    0.0    2.058
       # (mixing rules determine interactions between types @atom:O and @atom:H)

       # -- Bonded interactions --
       #             bondType   parameter list (k_bond, r0)
       bond_coeff   @bond:OH    1000.00 1.0 
       #             angleType  parameter-list (k_theta, theta0)
       angle_coeff  @angle:HOH  1000.0   109.47

       # Group definitions and constraints can also go in the "In Settings" section
       group spce type  @atom:O  @atom:H
       fix fSHAKE spce shake 0.0001 10 100 b @bond:OH a @angle:HOH
       # (lammps quirk: Remember to "unfix fSHAKE" during minimization.)
     }

     # LAMMPS supports a large number of force-field styles. We must select
     # which ones we need. This information belongs in the "In Init" section.

     write_once("In Init") {
       units        real                 # angstroms, kCal/mole, Daltons, Kelvin
       atom_style   full                 # select column format for Atoms section
       pair_style   lj/charmm/coul/long 9.0 10.0 10  # params needed: epsilon sigma
       bond_style   harmonic             # parameters needed: k_bond, r0
       angle_style  harmonic             # parameters needed: k_theta, theta0
       kspace_style pppm 0.0001          # long-range electrostatics sum method
       pair_modify  mix arithmetic       # using Lorenz-Berthelot mixing rules
     }

   } # SPCE

Words which are preceded by “$” or “@” characters are counter variables
and will be replaced by integers. (See section :ref:`sec:variables`
for details.) Users can include SPCE water in their simulations using
commands like these:

::

   # -- file "system.lt" --
   import "spce_simple.lt"
   wat = new SPCE [1000]

You can now use “moltemplate.sh” to create simulation input files for
LAMMPS

::

   moltemplate.sh -pdb coords.pdb -atomstyle "full" system.lt

This command will create lammps input files for the molecular system
described in “system.lt”, using the desired atom style (“full” by
default). In this example, moltemplate is relying on an external file
(“coords.pdb”) to supply the atomic coordinates of the water molecules,
as well as the periodic boundary conditions. Coordinates in XYZ format
are also supported using “-xyz coords.xyz”.

*Details*
~~~~~~~~~

*Note that since XYZ files lack boundary information, you must also
include a “Boundary” section in your “.lt” file, as demonstrated in
section*\ `[sec:pbc] <#sec:pbc>`__\ *. In both cases, the order of the
atom types in a PDB or XYZ file (after sorting) should match the order
they are created by moltemplate (which is determined by the order of the
“new” commands in the LT file). Unfortunately this may require careful
manual editing of the PDB or XYZ file.*

.. _sec:coords_intro:

Coordinate generation
---------------------

It is not necessary to provide a separate file with atomic coordinates.
It is more common to manually specify the location (and orientation) of
the molecules in your system using the “.move()” and “.rot()” commands
in the LT file itself (discussed in section :ref:`sec:coordinates`).
For example you can replace the line:

::

   wat = new SPCE [1000]

from the example above with 1000 lines:

::

   wat1    = new SPCE
   wat2    = new SPCE.move(3.1034, 0.00, 0.00)
   wat3    = new SPCE.move(6.2068, 0.00, 0.00)
   wat4    = new SPCE.move(9.3102, 0.00, 0.00)
     :           :
   wat1000 = new SPCE.move(31.034, 31.034, 31.034)

Specifying geometry this way is tedious. Alternatively, moltemplate has
simple commands for arranging multiple copies of a molecule in periodic,
crystalline, toroidal, and helical 1-D, 2-D, and 3-D lattices. For
example, you can generate a simple cubic lattice of
10\ :math:`\times`\ 10\ :math:`\times`\ 10 water molecules (with a
3.1034 Angstrom spacing) using a single command (which in this example
we split into multiple lines)

::

   wat  = new SPCE [10].move(0.0, 0.0, 3.1034) 
                   [10].move(0.0, 3.1034, 0.0) 
                   [10].move(3.1034, 0.0, 0.0)

(See section :ref:`sec:coordinates` for more details and examples.)
This will create 1000 molecules with names like “wat[0][0][0]”,
“wat[0][0][1]”,\ :math:`\ldots`, “wat[9][9][9]”. You can always access
individual atomIDs, molIDs, bondIDs, angleIDs, and dihedralIDs (if
present), for any molecule elsewhere in your LT files using this
notation: “$atom:wat[2][3][4]/h1”, “$bond:wat[0][5][1]/oh1”,
“$angle:wat[2][8][3]/hoh”, “$mol:wat[0][1][2]”. This allows you to
define interactions which link different molecules together (see section
:ref:`sec:coordinates`).

A list of available coordinate transformations is provided in section
:ref:`sec:xforms_table`.

.. _sec:pbc:

Boundary Conditions:
~~~~~~~~~~~~~~~~~~~~

LAMMPS simulations have finite volume and are usually periodic. We must
specify the dimensions of the simulation boundary using the
“write_once(“Data Boundary”)” command.

::

   write_once("Data Boundary") {
      0.0  31.034  xlo xhi
      0.0  31.034  ylo yhi
      0.0  31.034  zlo zhi
   }

This is usually specified in the outermost LT file (“system.lt” in this
example). *(Note: Boundary conditions do not have to be rectangular or
even periodic. For triclinic cells, additional “xy”, “xz”, and “yz” tilt
parameters can be added. For details, lookup the “read_data” and
“boundary” commands in the official LAMMPS documentation.)*

This system is shown in figure :numref:`fig_spce_x_1000_before`
and :numref:`fig_spce_x_1000_after`. After you have specified
the geometry, then you can run moltemplate.sh this way:

::

   moltemplate.sh -atomstyle "full" system.lt

.. raw:: latex

   \centering


.. _fig_spce_x_1000_before:
.. figure:: /_static/waterSPCEx1000_LR.jpg
   :alt: water before equilibration
   :align: center
   :width: 5cm

   A box of 1000 water molecules (before pressure equilibration),
   generated by moltemplate and visualized
   by VMD with the topotools plugin. (The VMD console commands used for
   visualization were: “topo readlammpsdata system.data full”, “animate
   write psf system.psf”, “pbc wrap -compound res -all”, and “pbc box”. See
   :ref:`sec:vmd_topotools` and :ref:`sec:vmd_advanced` for details.


.. _fig_spce_x_1000_after:
.. figure:: /_static/waterSPCEx1000_t=25_LR.jpg
   :alt: water after equilibration
   :align: center
   :width: 5cm

   A box of 1000 water molecules (after pressure equilibration).



.. _sec:vmd_topotools:

Visualization using VMD & topotools
-----------------------------------

When you run moltemplate, it generates a LAMMPS *data* file. This file
is usually called “system.data”. Geometric information, and bonded
topology are stored in this file. After you have run moltemplate, you
should look at your system to check it for mistakes. Problems can easily
occur with overlapping atoms (missing molecules), periodic boundaries,
incorrectly bonded atoms, incorrect rotation and movement. Sometimes
many iterations of running moltemplate and visualization are necessary.

*Optional:* If you have VMD installed, you can automatically visualize
the system you have just created automatically by invoking moltemplate
with the **-vmd** command line argument. (In other words invoke
moltemplate.sh using *moltemplate.sh -vmd* instead of *moltemplate.sh*.
VMD must be installed.) If you don’t use the -vmd command line argument,
you can always view the system in VMD later manually. For instructions
how to do that, keep reading...

Some very basic instructions how to use VMD are provided below: *(Note:
These instructions were written for VMD 1.9 and topotools 1.2)*

To view a *data* file:

a) start VMD

b) from the menu, select **Extensions** :math:`\rightarrow`

c) enter:

::

           topo readlammpsdata system.data full
           animate write psf system.psf

The first command will display all of the atoms and bonds in your system
in VMD’s 3-D window. (We use “**full**” because we are using the “full”
atom_style in this particular example. If you are using a different
atom_style, then change the command above accordingly.)

The second command will create a PSF file (“system.psf”) which will be
useful later for viewing a trajectory file created during a LAMMPS
simulation. (See section `4.5 <#sec:vmd_trajectory>`__.)

Most likely, atoms and bonds will be represented by ugly dots and lines
by default. To change the way molecules are displayed, control their
color, display periodic boundaries, and wrap atomic coordinates, read
the short VMD tutorial in appendix :ref:`sec:vmd_advanced`.

*(Note: As of 2019-9-03, VMD does not have built-in support for exotic
atom_styles such as ellipsoids and dipoles, but their are 3rd-party
scripts, plugins and settings you can use. Search the VMD and LAMMPS
mailing lists for help.*)

.. _sec:run:

Running a LAMMPS simulation (after using moltemplate)
-----------------------------------------------------

To run a simulation of one or more molecules, LAMMPS requires an *input
script* and a *data file*. Input scripts typically contain force field
styles, parameters and run settings. (They sometimes also contain atom
coordinates.) Data files typically contain atom coordinates and bonded
topology data. (They sometimes also contain force-field parameters.)

Moltemplate will create the following files: “system.data”, “system.in”,
“system.in.init”, “system.in.settings”, (and possibly other files
including “system.in.coords”). These are LAMMPS input/data files, and
they can be run in LAMMPS with minimal modification (see below). The
main input script file is named “system.in”, and it usually contains
just three lines:

::

   include   "system.in.init"
   read_data "system.data"
   include   "system.in.settings"

To *run* a simulation, you will have to edit this file in order to add a
couple of run commands. These commands tell LAMMPS about the simulation
conditions you want to use (temperature, pressure), how long to run the
simulation, how to integrate the equations of motion, and how to write
the results to a file (file format, frequency, etc). Moltemplate.sh can
not do this for you. Some simple examples (which you can paste into your
input script) are provided in the *online examples* which can be
downloaded from http://moltemplate.org. (These example input scripts
typically have names like “run.in.nvt” and “run.in.npt”.)

In addition to the examples, an introduction to LAMMP input scripts is
provided at these links:
http://lammps.sandia.gov/doc/Section_commands.html#cmd_1.
http://lammps.sandia.gov/doc/Section_howto.html and
http://lammps.sandia.gov/doc/Section_howto.html#howto_15

Here is a list of basic input script commands used in the moltemplate
examples (and links to their documentation):

**run**  http://lammps.sandia.gov/doc/run.html

**timestep**  http://lammps.sandia.gov/doc/timestep.html

**thermo**  http://lammps.sandia.gov/doc/thermo.html

**dump**  http://lammps.sandia.gov/doc/dump.html

**read_data**  http://lammps.sandia.gov/doc/read_data.html

**restart**  http://lammps.sandia.gov/doc/restart.html

**include**  http://lammps.sandia.gov/doc/include.html

**fix nve**  http://lammps.sandia.gov/doc/fix_nve.html

**fix nvt**  http://lammps.sandia.gov/doc/fix_nh.html

**fix npt**  http://lammps.sandia.gov/doc/fix_nh.html

**fix langevin**  http://lammps.sandia.gov/doc/fix_langevin.html

**fix**  http://lammps.sandia.gov/doc/fix.html

**group**  http://lammps.sandia.gov/doc/group.html

**compute**  http://lammps.sandia.gov/doc/compute.html

**print**  http://lammps.sandia.gov/doc/print.html

**variable**  http://lammps.sandia.gov/doc/variable.html

**rerun**  http://lammps.sandia.gov/doc/rerun.html

**fix shake**  http://lammps.sandia.gov/doc/fix_shake.html

**fix rigid**  http://lammps.sandia.gov/doc/fix_rigid.html

In addition, all users should be familiar with the following commands:
(These appear in the “In Init” section of most LT files.)

**atom_style**  http://lammps.sandia.gov/doc/atom_style.html

**pair_style**  http://lammps.sandia.gov/doc/pair_style.html

**bond_style**  http://lammps.sandia.gov/doc/bond_style.html

**angle_style**  http://lammps.sandia.gov/doc/angle_style.html

.. _sec:vmd_trajectory:

Visualizing Trajectories
------------------------

After you have run a simulation in LAMMPS, there are several programs
which can visualize the system. If you have saved your trajectory in
LAMMPS “dump” format, later you can view it in VMD
:cite:`VMD`. For the purpose of viewing trajectories in
LAMMPS, I recommend using the following style of “dump” commands in the
LAMMPS input-script that you use when you run LAMMPS:

::

   dump 1 all custom 1000 DUMP_FILE.lammpstrj id mol type x y z ix iy iz

(The “all” and “1000”, refer to the atom selection and save interval,
which may differ depending on the kind of simulation you are running.
See http://lammps.sandia.gov/doc/dump.html for details.)

Once you have a dump file, you can view it in VMD using:

a) Start VMD From the menu in the upper-left, select
**File**\ :math:`\rightarrow`

b) Browse to select the PSF file you created above, and load it. (Don’t
close the window yet.)

c) Browse to select the trajectory file. If necessary, for "file type"
select: "LAMMPS Trajectory". Click on **OK**.

d) Click on the **Load** button.

Again, to customize molecule appearance, display periodic boundary
conditions and wrap molecule coordinates, see the commands discussed in
appendix :ref:`sec:vmd_advanced`.

*(Note: VMD may not be able to correctly visualize simulations which do
not preserve the number of atoms and bonds over time, such as those run
using*\ **fix bond/create**\ *,*\ **fix bond/break**\ *, or*\ **fix
gcmc**\ *.)*

.. bibliography:: /refs.bib
   :filter: docname in docnames