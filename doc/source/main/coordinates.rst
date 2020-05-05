.. |[fig:2bead_polymer] a)-b) Building a complex system from small pieces: Construction of a polymer (b) out of smaller (2-atom) subunits (a) using composition and rigid-body transformations. Bonds connecting different monomer together (blue) must be declared explicitly, but angle and dihedral interactions will be generated automatically. See section| image:: /_static/2bead_monomer.jpg
   :height: 3cm

.. |[fig:2bead_polymer] a)-b) Building a complex system from small pieces: Construction of a polymer (b) out of smaller (2-atom) subunits (a) using composition and rigid-body transformations. Bonds connecting different monomer together (blue) must be declared explicitly, but angle and dihedral interactions will be generated automatically. See section| image:: /_static/2bead_polymer.jpg
   :height: 3cm

.. |[fig:2bead_polymer] a)-b) Building a complex system from small pieces: Construction of a polymer (b) out of smaller (2-atom) subunits (a) using composition and rigid-body transformations. Bonds connecting different monomer together (blue) must be declared explicitly, but angle and dihedral interactions will be generated automatically. See section| image:: /_static/2bead_polymers_nopbc_t=0_LR.jpg
   :width: 4cm

.. |[fig:2bead_polymer] a)-b) Building a complex system from small pieces: Construction of a polymer (b) out of smaller (2-atom) subunits (a) using composition and rigid-body transformations. Bonds connecting different monomer together (blue) must be declared explicitly, but angle and dihedral interactions will be generated automatically. See section| image:: /_static/2bead_polymers_t=100ps_LR.jpg
   :width: 4cm

.. _sec:coordinates:

Object composition and coordinate generation 
==============================================

Objects can be connected together to form larger molecule objects. These
objects can be used to form still larger objects. As an example, we
define a small 2-atom molecule named “Monomer”, and use it to construct
a short polymer (“Polymer”).

.. raw:: latex

   \centering

**a)** |[fig:2bead_polymer] a)-b) Building a complex system from small
pieces: Construction of a polymer (b) out of smaller (2-atom) subunits
(a) using composition and rigid-body transformations. Bonds connecting
different monomer together (blue) must be declared explicitly, but angle
and dihedral interactions will be generated automatically. See
section|\ :ref:`sec:2bead`\ |for details. c) An irregular lattice of
short polymers. (See
section|\ :ref:`sec:multidimensional_arrays`\ |.) d) The same system
after 100000 time steps using Langevin dynamics. (The VMD console
commands used for visualization were: “topo readlammpsdata system.data
full”, “animate write psf system.psf”, “pbc wrap -compound res -all”,
and “pbc box”. See sections|\ :ref:`sec:vmd_topotools`\ |,
and|\ :ref::ref:`sec:vmd_advanced`\ |for details.| **b)**
|[fig:2bead_polymer] a)-b) Building a complex system from small pieces:
Construction of a polymer (b) out of smaller (2-atom) subunits (a) using
composition and rigid-body transformations. Bonds connecting different
monomer together (blue) must be declared explicitly, but angle and
dihedral interactions will be generated automatically. See
section|\ :ref:`sec:2bead`\ |for details. c) An irregular lattice of
short polymers. (See
section|\ :ref:`sec:multidimensional_arrays`\ |.) d) The same system
after 100000 time steps using Langevin dynamics. (The VMD console
commands used for visualization were: “topo readlammpsdata system.data
full”, “animate write psf system.psf”, “pbc wrap -compound res -all”,
and “pbc box”. See sections|\ :ref:`sec:vmd_topotools`\ |,
and|\ :ref::ref:`sec:vmd_advanced`\ |for details.| **c)**
|[fig:2bead_polymer] a)-b) Building a complex system from small pieces:
Construction of a polymer (b) out of smaller (2-atom) subunits (a) using
composition and rigid-body transformations. Bonds connecting different
monomer together (blue) must be declared explicitly, but angle and
dihedral interactions will be generated automatically. See
section|\ :ref:`sec:2bead`\ |for details. c) An irregular lattice of
short polymers. (See
section|\ :ref:`sec:multidimensional_arrays`\ |.) d) The same system
after 100000 time steps using Langevin dynamics. (The VMD console
commands used for visualization were: “topo readlammpsdata system.data
full”, “animate write psf system.psf”, “pbc wrap -compound res -all”,
and “pbc box”. See sections|\ :ref:`sec:vmd_topotools`\ |,
and|\ :ref::ref:`sec:vmd_advanced`\ |for details.| **d)**
|[fig:2bead_polymer] a)-b) Building a complex system from small pieces:
Construction of a polymer (b) out of smaller (2-atom) subunits (a) using
composition and rigid-body transformations. Bonds connecting different
monomer together (blue) must be declared explicitly, but angle and
dihedral interactions will be generated automatically. See
section|\ :ref:`sec:2bead`\ |for details. c) An irregular lattice of
short polymers. (See
section|\ :ref:`sec:multidimensional_arrays`\ |.) d) The same system
after 100000 time steps using Langevin dynamics. (The VMD console
commands used for visualization were: “topo readlammpsdata system.data
full”, “animate write psf system.psf”, “pbc wrap -compound res -all”,
and “pbc box”. See sections|\ :ref:`sec:vmd_topotools`\ |,
and|\ :ref::ref:`sec:vmd_advanced`\ |for details.|

.. raw:: latex

   \pagebreak

.. _sec:2bead:

Building a large molecule from smaller pieces
---------------------------------------------

Consider the following simple 2-atom dumbell-shaped molelule (“Monomer”)

::

   # -- file "monomer.lt" --

   import "forcefield.lt"   # contains force-field parameters

   Monomer inherits ForceField {

     write("Data Atoms") {
       # atomId molId   atomType   charge   x      y        z      
       $atom:ca $mol:... @atom:CA   0.0   0.000  1.0000   0.0000000
       $atom:r  $mol:... @atom:R    0.0   0.000  4.4000   0.0000000
     }
     write("Data Bonds") {
       # bond-id   bond-type        atom-id1  atom-id2
       $bond:cr    @bond:Sidechain  $atom:ca  $atom:r
     }
   }

Soon will use it to construct a polymer (“Polymer”) *Note: The ellipsis
notation used here “$mol:...”. warns moltemplate that the “Monomer”
molecule may be part of a larger molecule. (This is explained in more
detail in section*\ `9.6.2 <#sec:ellipsis_mol>`__\ *.) (Note: The
meaning of “inherits ForceField” will be explained below in
section*\ `6.2 <#sec:nbody_by_type_intro>`__\ *)*

In this example we will define two kinds of molecule objects: “Monomer”,
and “Polymer” (*defined later*).

.. _sec:twobead_polymer:

Building a simple polymer
~~~~~~~~~~~~~~~~~~~~~~~~~

We construct a short polymer by making 7 copies of “Monomer”, rotating
and moving each copy: [sec:twobead_polymer]

::

   # -- file "polymer.lt" --

   import "monomer.lt"  #(defines "Monomer" and "ForceField")

   Polymer inherits ForceField {

     # The next line is optional:
     create_var {$mol}  #(force all monomers to share the same molecule-ID)

     # Now create some monomers

     mon1 = new Monomer     #(no need to move the first monomer)
     mon2 = new Monomer.rot(180.0, 1,0,0).move(3.2,0,0)
     mon3 = new Monomer.rot(360.0, 1,0,0).move(6.4,0,0)
     mon4 = new Monomer.rot(540.0, 1,0,0).move(9.6,0,0)
     mon5 = new Monomer.rot(720.0, 1,0,0).move(12.8,0,0)
     mon6 = new Monomer.rot(900.0, 1,0,0).move(16.0,0,0)
     mon7 = new Monomer.rot(1080.0, 1,0,0).move(19.2,0,0)

     # Now, link the monomers together this way:
     write("Data Bonds") {
       $bond:backbone1  @bond:Backbone  $atom:mon1/ca  $atom:mon2/ca
       $bond:backbone2  @bond:Backbone  $atom:mon2/ca  $atom:mon3/ca
       $bond:backbone3  @bond:Backbone  $atom:mon3/ca  $atom:mon4/ca
       $bond:backbone4  @bond:Backbone  $atom:mon4/ca  $atom:mon5/ca
       $bond:backbone5  @bond:Backbone  $atom:mon5/ca  $atom:mon6/ca
       $bond:backbone6  @bond:Backbone  $atom:mon6/ca  $atom:mon7/ca
     }
   }

The position and orientation of each copy of “Monomer” is specified
after the “new” statement. Each “new” statement is typically followed by
a chain of move/rotate/scale functions separated by dots, evaluated
left-to-right (optionally followed by square brackets and then more
dots). For example, “mon2” is a copy of “Monomer” which is first rotated
180 degrees around the X axis (denoted by “1,0,0”), and **then** moved
in the (3.2,0,0) direction. (The last three arguments to the “rot()”
command denote the axis of rotation, which does not have to be
normalized.) (A list of available coordinate transformations is provided
in section :ref:`sec:xforms_table`.)

*(Note: Although we did not do this here, it is sometimes convenient to
represent polymers as 1-dimensional arrays. See
sections*\ `7 <#sec:arrays>`__\ *and*\ :ref:`sec:random_arrays`\ *for
examples.)*

To bond atoms in different molecules or molecular subunits together, we
used the write(“Data Bonds”) command to append additional bonds to the
system.

.. _sec:nbody_by_type_intro:

Bonded interactions *by type*
-----------------------------

In this example we did *not* provide a list of all 3-body and 4-body
angle forces between bonded atoms in the polymer. Moltemplate allows you
to manually list all of these interactions (using the “write_once("Data
Angles")” command from section :ref:`sec:spce_example`, *or* the
“write_once("Data Dihedrals")”, or “write_once("Data Impropers")”
commands). However there are usually many of them. For this reason, it
is often more convenient to provide moltemplate with instructions to
help it automatically figure out which atoms participate in 3-body and
4-body angle interactions, and what force field parameters to assign to
them. We will do that below using the following commands:
“write_once("Data Angles By Type")”, “write_once("Data Dihedrals By
Type")”, and “write_once("Data Impropers By Type")”

Furthermoree, since many different kinds molecules often share the same
rules for creating 3-body and 4-body angle interactions, it is
convenient to organize all of this information together into one place
(eg an object named “ForceField”). A “ForceField” object will typically
include many “write_once("Data Angles By Type")” commands, as well as
force field parameters and related atom type properties. We also
typically store that information in a separate file (eg “forcefield.lt”,
“oplsaa.lt”, “gaff2.lt”, “compass.lt”, etc...).

::

   # -- file "forcefield.lt" --

   ForceField {

     # There are 2 atom types: "CA" and "R"
     write_once("Data Masses") {
       @atom:CA    13.0
       @atom:R     50.0
     }

     # Force-field parameters ("coeffs") go in the "In Settings" section:

     write_once("In Settings") {
       # Pairwise (non-bonded) interactions:
       #           atomType1 atomType2   epsilon sigma
       pair_coeff   @atom:CA @atom:CA       0.10 2.0
       pair_coeff   @atom:R  @atom:R        0.50 3.6
       # (Interactions between different atoms are determined by mixing rules.)
     }

     # 2-body (bonded) interactions:
     #
     #   Ubond(r) = k*(r-r0)^2
     #
     write_once("In Settings") {
       #             bond-type        k     r0
       bond_coeff  @bond:Sidechain   15.0   3.4
       bond_coeff  @bond:Backbone    15.0   3.7
     }

     # Although the simple "Monomer" object we defined above has only
     # two atoms, later on, we will create molecules with many bonds.
     # By convention, in this file we keep track of all of the possible
     # interactions which could exist between these atoms:

     # Rules for determining 3-body (angle) interactions by atom & bond type:
     # angle-type     atomType1 atomType2 atomType3  bondType1 bondType2

     write_once("Data Angles By Type") {
       @angle:Backbone  @atom:CA  @atom:CA  @atom:CA   @bond:*   @bond:*
       @angle:Sidechain @atom:CA  @atom:CA  @atom:R    @bond:*   @bond:*
     }

     # Force-field parameters for 3-body (angle) interactions:
     #
     #   Uangle(theta) = k*(theta-theta0)^2
     #
     write_once("In Settings") {
       #             angle-type       k    theta0
       angle_coeff @angle:Backbone   30.00  114
       angle_coeff @angle:Sidechain  30.00  132
     }

     # 4-body interactions in this example are listed by atomType
     # Rules for determining 4-body (dihedral) interactions by atom & bond type:
     write_once("Data Dihedrals By Type") {
       # dihedralType atmType1 atmType2 atmType3 atmType4 bondType1 bType2 bType3
       @dihedral:CCCC @atom:CA @atom:CA @atom:CA @atom:CA  @bond:* @bond:* @bond:*
       @dihedral:RCCR @atom:R  @atom:CA @atom:CA @atom:R   @bond:* @bond:* @bond:*
     }

     # The forumula used is:
     #
     # Udihedral(phi) = K * (1 + cos(n*phi - d))
     #
     #     The d parameter is in degrees, K is in kcal/mol/rad^2.
     #
     # The corresponding command is 
     # dihedral_coeff dihedralType       K  n   d  w(ignored)

     write_once("In Settings") {
       dihedral_coeff @dihedral:CCCC   -0.5 1 -180 0.0
       dihedral_coeff @dihedral:RCCR   -1.5 1 -180 0.0
     }

     write_once("In Init") {
       # -- Styles used in "ForceField" --
       # -- (Changing these styles will change the formulas above) --
       units           real
       atom_style      full
       bond_style      harmonic
       angle_style     harmonic
       dihedral_style  charmm
       pair_style      lj/cut 11.0
     }
   }

Any molecule that wants to access this information can use the “inherits
ForceField” keyword. *(...as we did in the “monomer.lt” and “polymer.lt”
files in theexample above. Note: the “import forcefield” statement was
also necessary because the information is located in a separate file:
“forcefield.lt”.* *You can customize these “By Type” rules further by
altering the bond topology search rules and atom type symmetry. See
appendix*\ `16.2 <#sec:nbody_by_type_custom>`__\ *for details.)*
