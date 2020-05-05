.. _sec:spce_example_robust:

Portability: Using *LT files* for force-field storage
=====================================================

The “.LT” format is a flexible file format for storing force field
parameters in LAMMPS. If you want to share your “.LT” file with others,
it’s not safe to assume that all interactions use the same standard
formula.

Mixing molecule types
---------------------

LAMMPS has the ability to combine molecules using multiple different
force-field styles together using. In section
:ref:`sec:spce_example`, we provided an example of an SPCE water
molecule model. This example was simple to understand. However, as
written, it would be impossible to combine this definition of water with
other molecules which don’t share the same simple bond or angle styles.
For example, we used harmonic restoring forces to preserve the water
angle at :math:`109.47^\circ`, but other users may want to mix this SPCE
water with a small number of molecules which use a more complicated
angular potential formula, or tabular angle potentials. Using the
“hybrid” keyword, you can avoid this limitation. A more robust example
is included below.

::

   # file "spce.lt" 
   #
   #    H1     H2
   #      \   /
   #        O

   SPCE {

     write_once("In Init") {
       # -- Default styles (for solo "SPCE" water) --
       units        real
       atom_style   full
       pair_style   hybrid lj/charmm/coul/long 9.0 10.0 10.0
       bond_style   hybrid harmonic
       angle_style  hybrid harmonic
       kspace_style pppm 0.0001
       pair_modify  mix arithmetic
     }

     # AtomID  MolID("."=this)  AtomType  charge  coordX   coordY   coordZ
     write("Data Atoms") {
       $atom:O  $mol:. @atom:O -0.8476  0.0000000 0.00000 0.000000
       $atom:H1 $mol:. @atom:H  0.4238  0.8164904 0.00000  0.5773590
       $atom:H2 $mol:. @atom:H  0.4238  -0.8164904 0.00000 0.5773590
     }

     # atom-type  Mass
     write_once("Data Masses") {
       @atom:O    15.9994
       @atom:H     1.008
     }

     # -- Forces between atoms (non-bonded) --

     #           atomTypeI atomTypeJ    pair-style-name    parameter-list
     write("In Settings") {
       pair_coeff   @atom:O @atom:O  lj/charmm/coul/long   0.1553  3.166 
       pair_coeff   @atom:H @atom:H  lj/charmm/coul/long   0.0     2.058
     }

     # -- Forces between atoms (bonded) --

     #  bond-id   bond-type  atom-id1 atom-id2
     write("Data Bonds") {
       $bond:oh1  @bond:OH   $atom:O  $atom:H1
       $bond:oh2  @bond:OH   $atom:O  $atom:H2
     }

     #             bond-type   bond-style-name  parameter-list
     write("In Settings") {
       bond_coeff   @bond:OH      harmonic      200.0   1.0 
     }

     # angle-id   angle-type  atom-id1 atom-id2 atom-id3
     write("Data Angles") {
       $angle:hoh @angle:HOH  $atom:H1 $atom:O $atom:H2
     }

     #              angle-type  angle-style-name  parameter-list
     write("In Settings) {
       angle_coeff  @angle:HOH    harmonic        200.0   109.47
     }

     # miscellaneous
     write_once("In Settings") {
       group spce type  @atom:O  @atom:H
       fix fSHAKE spce shake 0.0001 10 100 b @bond:OH a @angle:HOH
       # (Remember to "unfix" fSHAKE during minimization.)
     }

   } # SPCE

There are two differences between this molecule definition and the
“spce_simple.lt” example from section :ref:`sec:spce_example`:

Hybrid force field styles
~~~~~~~~~~~~~~~~~~~~~~~~~

To experienced LAMMPS users, it may seem strange that in this example
that we have chosen “hybrid” styles followed by only one force-field
style (“harmonic”). However this will make your molecule easier to share
with others. When other people use your LT file, they can override these
styles as explained in section `9.2 <#sec:overriding_styles>`__.

.. _sec:overriding_styles:

Combining molecules with different force field styles
-----------------------------------------------------

*Later on*, if a user wants to combine the SPCE water molecule with
another molecule which uses a tabular pair_style (for example), they
would have to specify the complete hybrid pair_style in the “Init”
section of their LT file. For example:

::

   import "spce.lt"
   import "other_molecule.lt"

   write_once("In Init") {
     pair_style hybrid lj/charmm/coul/long 9 10 10 table spline 1000
   }

Note: By placing the “write_once("In Init"){ }” statement *after*
“import "spce.lt"”, this insures that the pair_style commands issued
here will override the pair_style commands issued earlier “spce.lt”.
This allows moltemplate users users to combine their molecules “spce.lt”
file shown here with other template files without modification (assuming
the atom styles match).

Warning: Force-field parameters belong in “In Settings”, not “Data”
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

LAMMPS allows users to store force-field parameters (“Coeffs”) in two
places: a DATA file, *or* an INPUT script. Similarly, moltemplate
technicaly allows you to store these parameters in in the “Data”
sections of your .LT file:

write_once("Data Pair Coeffs")

write_once("Data Bond Coeffs")

write_once("Data Angle Coeffs")

write_once("Data Dihedral Coeffs")

write_once("Data Improper Coeffs")

*However, for portability reasons, this is discouraged.* Instead,
declare your force field parameters as we do in this manual, using the
corresponding input script commands. (For example, “pair_coeff”,
“bond_coeff”, “angle_coeff”, “dihedral_coeff”, and “improper_coeff”. As
in the examples, all of these commands belong in the “write_once("In
Settings")” sections of your .LT files.)

.. _sec:nesting:

Nesting
-------

Molecule names such as “Solvent” (or even “Water”) are short and easy to
type, but are vague and are not portable. If you use common, generic
molecule names, you will not be able to combine your molecule templates
with templates written by others (without carefully checking for naming
conflicts). LT files were meant to be used for storing and exchanging
libraries of different molecule types.

Suppose, for example, that you want to run a simulation consisting of
different molecule types, each of which belong to different LT files.
Suppose two of the LT files both happen to contain definitions for
“Water”. Moltemplate does not detect these name clashes automatically
and instead attempts to merge the two versions of “Water” together,
(most likely creating a molecule with 6 atoms instead of 3). This is
presumably not what you want.

As the number of molecule types grows, the possibility of naming clashes
increases. As the behavior of the same molecule can be approximated
using many different force fields, one has to be careful to avoid
clashing molecule names.

To alleviate the problem, you can “nest” your molecules inside the
definition of other molecules or namespace objects. This reduces the
scope in which your molecule is defined. See section
:ref:`sec:butane` for an example.

.. _sec:force_field_example_trappe:

A simple force-field example
----------------------------

Force-field parameters can be shared by groups of related molecules. In
the example below, we create an object named “TraPPE”. Later we use it
to define a new molecule named “Cyclopentane”.

The following example defines a coarse-grained (united-atom) version of
a “cyclopentane” molecule. (Hydrogen atoms have been omitted.) In this
example, only the atom types (and positions) and the bonds connecting
them need to be specified. The interactions between them are determined
automatically by the settings in the force-field file “trappe1998.lt”.

::

   import "trappe1998.lt"

   cyclopentane {

     # AtomID  MolID('.'=this) AtomType  charge coordX  coordY   coordZ
     write("Data Atoms") {
       $atom:c1 $mol:. @atom:TraPPE/CH2 0.0 0.0000 0.000000000 1.0000000
       $atom:c2 $mol:. @atom:TraPPE/CH2 0.0 0.0000 0.951056516 0.3090170
       $atom:c3 $mol:. @atom:TraPPE/CH2 0.0 0.0000 0.587785252 -0.809017
       $atom:c4 $mol:. @atom:TraPPE/CH2 0.0 0.0000 -0.587785252 -0.809017
       $atom:c5 $mol:. @atom:TraPPE/CH2 0.0 0.0000 -0.951056516 0.3090170
     }

     write("Data Bonds") {
       $bond:bond1 @bond:TraPPE/CC $atom:c1 $atom:c2
       $bond:bond2 @bond:TraPPE/CC $atom:c2 $atom:c3
       $bond:bond3 @bond:TraPPE/CC $atom:c3 $atom:c4
       $bond:bond4 @bond:TraPPE/CC $atom:c4 $atom:c5
       $bond:bond5 @bond:TraPPE/CC $atom:c5 $atom:c1
     }
   }

(The “TraPPE/” is explained below.) We can create copies of this
molecule in the same way we did with SPCE:

::

   # A cubic lattice of 125 cyclopentane molecules (12-angstrom spacing)
   mols = new Cyclopentane [5].move(0,0,12) [5].move(0,12,0) [5].move(12,0,0)

Unlike the SPCE example, we don’t have to specify all of the
interactions between these atoms because the atom and bond types (CH2,
CC). match the type-names defined in the “trappe1998.lt” file. This file
contains a collection of atom types and force-field parameters for
coarse-grained hydrocarbon chains. (See :raw-latex:`\cite{TraPPE}` for
details.) This way, the “CH2” atoms in cyclopentane will interact with,
and behave identically to any “CH2” atom from any other molecule which
uses the TraPPE force field. (The same is true for other atom types, and
interaction-types which are specific to “TraPPE”, such as
“@atom:TraPPE/CH3”, “@bond:TraPPE/CC”, etc... Another molecule which
uses the TraPPE force field is discussed later in section
:ref:`sec:butane`.) The important parts of the “trappe1998.lt” file
are shown below:

.. _sec:trappe:

Namespace example
~~~~~~~~~~~~~~~~~

::

   # -- file "trappe1998.lt" --

   TraPPE {
     write_once("Data Masses") {
       @atom:CH2 14.1707
       @atom:CH3 15.2507
     }
     write_once("In Settings") {
       bond_coeff     @bond:CC      harmonic   120.0   1.54
       angle_coeff    @angle:CCC    harmonic   62.0022 114
       dihedral_coeff @dihedral:CCCC opls 1.411036 -0.271016 3.145034 0.0
       pair_coeff @atom:CH2 @atom:CH2 lj/charmm/coul/charmm 0.091411522 3.95
       pair_coeff @atom:CH3 @atom:CH3 lj/charmm/coul/charmm 0.194746286 3.75
       # (Interactions between different atom types use mixing rules.)
       # (Hybrid styles were used for portability.)
     }
     write_once("Data Angles By Type") {
       @angle:CCC @atom:C* @atom:C* @atom:C* @bond:CC @bond:CC
     }
     write_once("Data Dihedrals By Type") {
      @dihedral:CCCC @atom:C* @atom:C* @atom:C* @atom:C* @bond:CC @bond:CC @bond:CC
     }
   }

In addition to the atom-type names and masses, this file stores the
force-field parameters (coeffs) for the interactions between them.

Bonded interactions *by type*
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Again, the “Data Angles By Type” and “Data Dihedrals By Type” sections
tell moltemplate.sh that bonded 3-body and 4-body interactions exist
between any 3 or 4 consecutively bonded carbon atoms (of type CH2, CH3,
or CH4) assuming they are bonded using “CC” (saturated) bonds. The “\*”
character is a wild-card. “C\*” matches “CH2”, “CH3”, and “CH4”.
(Bond-types can be omitted or replaced with wild-cards “@bond:\*”.)

Namespaces and nesting:
~~~~~~~~~~~~~~~~~~~~~~~

Names like “CH2” and “CC” are extremely common. To avoid confusing them
with similarly named atoms and bonds in other molecules, we enclose them
(“nest” them) within a *namespace* (“TraPPE”, in this example). Unlike
“SPCE” and “Cyclopentane”, “TraPPE” is not a molecule. It is just a
container of atom types, bond-types and force-field parameters shared by
other molecules. We do this to distinguish them from other atoms and
bonds which have the same name, but mean something else. Elsewhere we
can refer to these atom/bond types as “@atom:TraPPE/CH2” and
“@bond:TraPPE/CC”. (You can also avoid repeating the cumbersome
“TraPPE/” prefix for molecules defined within the TraPPE namespace. For
example, see section :ref:`sec:butane`.)

.. _sec:butane:

Nested molecules
----------------

Earlier in section :ref:`sec:trappe`, we created an object named
“TraPPE” and used it to create a molecule named “Cyclopentane”. Here we
use it to demonstrate nesting. Suppose we define a new molecule “Butane”
consisting of 4 coarse-grained (united-atom) carbon-like beads, whose
types are named “CH2” and “CH3”.

::

   # -- file "trappe_butane.lt" --

   import "trappe1998.lt"

   Butane {
     write("Data Atoms"){
       $atom:c1 $mol:. @atom:TraPPE/CH3 0.0  0.419372  0.000 -1.937329
       $atom:c2 $mol:. @atom:TraPPE/CH2 0.0  -0.419372  0.000 -0.645776
       $atom:c3 $mol:. @atom:TraPPE/CH2 0.0  0.419372  0.000  0.645776
       $atom:c4 $mol:. @atom:TraPPE/CH3 0.0  -0.419372  0.0000 1.937329
     }
     write("Data Bonds"){
       $bond:b1 @bond:TraPPE/CC $atom:c1 $atom:c2
       $bond:b2 @bond:TraPPE/CC $atom:c2 $atom:c3
       $bond:b3 @bond:TraPPE/CC $atom:c3 $atom:c4
     }
   }

Alternately, as mentioned above, it may be simpler to nest our “Butane”
within “TraPPE”, so that so that it does not get confused with other
(perhaps all-atom) representations of butane. In that case, we would
use:

::

   # -- file "trappe_butane.lt" --

   import "trappe1998.lt"

   TraPPE {
     Butane {
       write("Data Atoms"){
         $atom:c1 $mol:. @atom:../CH3 0.0  0.419372  0.000 -1.937329
         $atom:c2 $mol:. @atom:../CH2 0.0  -0.419372  0.000 -0.645776
         $atom:c3 $mol:. @atom:../CH2 0.0  0.419372  0.000  0.645776
         $atom:c4 $mol:. @atom:../CH3 0.0  -0.419372  0.0000 1.937329
       }
       write("Data Bonds"){
         $bond:b1 @bond:../CC $atom:c1 $atom:c2
         $bond:b2 @bond:../CC $atom:c2 $atom:c3
         $bond:b3 @bond:../CC $atom:c3 $atom:c4
       }
     }
   }

Note: Wrapping Butane within “TraPPE{ }” clause merely appends
additional content to be added to the “TraPPE” object defined in the
“trappe1998.lt” file (which was included earlier). It does not overwrite
it. Again “../” tells moltemplate use the “CH2” atom defined in the
context of the TraPPE environment (IE. one level up). This insures that
moltemplate does not create a new “CH2” atom type which is local to the
Butane molecule. (Again, by default all atom types and other variables
are local. See section `5.2.5 <#sec:variable_scope>`__.)

To use this butane molecule in a simulation, you would import the file
containing the butane definition, and use a “new” command to create one
or more butane molecules.

::

   import "trappe_butane.lt"
   new butane = TraPPE/Butane

(You don’t need to import “trappe1998.lt” in this example because it was
imported within “trappe_butane.lt”.) The “TraPPE/” prefix before
“Butane” lets moltemplate/ttree know that butane was defined *locally*
within TraPPE.

*Note: An alternative procedure using*\ **inheritance**\ *exists which
may be a cleaner way to handle these kinds of relationships. See
sections*\ :ref:`sec:inheritance`\ *and*\ :ref:`sec:multiple_inheritance`\ *.*

.. _sec:paths:

Path syntax: “../”, “.../”, and “$mol:.”
----------------------------------------

Generally, multiple slashes (“/”) as well as (“../”) can be used build a
path that indicates the (relative) location of any other molecule in the
object hierarchy. (The “.”, “/” and “..” symbols are used here in the
same way they are used to specify a path in a unix-like file-system. For
example, the “.” in “$mol:.” refers to the current molecule (instance),
in the same way that “./” refers to the current directory. (Note: is
shorthand for )

A slash by itself, “/”, refers to the *global environment*. This is the
outermost environment in which all molecules are defined/created.

.. _sec:ellipsis_type:

*(Advanced)* Ellipsis notation “.../”
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If you are using multiple levels of nesting, and if you don’t know (or
if you don’t want to specify) where a particular molecule type or atom
type (such as “CH2”) was defined, you can refer to it using “.../CH2”
instead of “../CH2”. The “...” ellipsis syntax searches up the tree of
nested molecules to find the target (the text following the “/” slash).

.. _sec:ellipsis_mol:

*(Advanced)* $mol:... notation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Recall that LAMMPS allows users the option to assign *molecule-IDs* to
each atom. (In the water example (section :ref:`sec:spce_example`),
atoms in each water molecule is assigned to a molecule-ID, denoted
“$mol:.”. In that example, the “.” was the name of that molecule’s ID.)

If you want to build large molecules using smaller pieces as
building-blocks moltemplate has a way to allow all the the atoms to
share the same molecule-ID. To refer to the ID of the molecule to which
you belong, use “$mol:...”. (If none of the molecule-objects which
instantiate the current molecule-object define a variable in the $mol
category, then a new local $mol variable will be created automatically.)
This means that the second column of each line of the “Data Atoms”
section should contain “$mol:...” (assuming “atom_style full” or
“molecular” is used).

The “...” syntax is explained more formally in appendix
`17 <#sec:adv_variable_syntax>`__.)

.. _sec:using_namespaces:

*using namespace* syntax
------------------------

Because the *Butane* molecule was defined within the *TraPPE*
environment, you normally have to indicate this when you refer to it
later. For example, to create a copy of a *Butane* molecule, you would
normally use:

::

   import "trappe_butane.lt"

   butane = new TraPPE/Butane

However for convenience, you can use the declaration so that, in the
future, you can quickly refer to any of the molecule types defined
within *TraPPE* directly, without having to specify their path.

::

   import "trappe_butane.lt"

   using namespace TraPPE

   butane = new Butane

This only works for molecule types, not atom types
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Unfortunately, you still *must* always **refer to** atom types, bond
types, and any other **primitive types explicitly** (by their full
path). For example, the second line in the *“Data Atoms”* in the example
below does not refer to the *CH2* atom type defined in *TraPPE*.
(Instead it creates a *new* atom type, which is probably not what you
want.)

::

   import "trappe_butane.lt"
   using namespace TraPPE
   butane = new Butane
   write("Data Atoms") {
     $atom:c1 $mol @atom:TraPPE/CH2 0.0   0.41937 0.00 1.9373  # <-- yes
     $atom:c2 $mol @atom:CH2        0.0  -0.41937 0.00 -0.6457 # new atom type?
   }

If, for example, you want to leave out the “TraPPE/” prefix when
accessing the atom, bond, and angle types defined in TraPPE, then
instead you can define a new molecule which *inherits* from TraPPE. (See
section :ref:`sec:inheritance`.)

.. _sec:inheritance:

Inheritance
-----------

We could have defined *Butane* this way:

::

   import "trappe1998.lt"

   Butane inherits TraPPE {
     write("Data Atoms"){
       $atom:c1 $mol:. @atom:CH3 0.0  0.419372  0.000 -1.937329
       $atom:c2 $mol:. @atom:CH2 0.0  -0.419372  0.000 -0.645776
       $atom:c3 $mol:. @atom:CH2 0.0  0.419372  0.000  0.645776
       $atom:c4 $mol:. @atom:CH3 0.0  -0.419372  0.0000 1.937329
     }
     write("Data Bonds"){
       $bond:b1 @bond:CC $atom:c1 $atom:c2
       $bond:b2 @bond:CC $atom:c2 $atom:c3
       $bond:b3 @bond:CC $atom:c3 $atom:c4
     }
   }

A molecule which *inherits* from another molecule (or namespace) *is* a
particular type of that molecule (or namespace). Defining *Butane* this
way allows it to access all of molecule types, atom types, and bond
types, etc... defined within *TraPPE* as if they were defined locally.
(I did not have to refer to the CH3 atom types as “@atom:TraPPE/CH3”,
for example.)

.. _sec:multiple_inheritance:

Multiple inheritance:
~~~~~~~~~~~~~~~~~~~~~

A molecule can inherit from multiple parents. This is one way you can
allow the *Butane* molecule to borrow atom, bond, angle, dihedral, and
improper types from *multiple* different force-field parents:

::

   import "trappe1998.lt"
   import "oplsaa.lt"

   Butane inherits TraPPE OPLSAA {
     ...
   }

*Details:Moltemplate attempts to resolve duplicate atom types or
molecule types if they are found in both parents, giving priority to the
first parent in the list of parents following the “inherits” keyword.
(“TraPPE” in this example.)*

.. _sec:inheritance_vs_nesting:

Inheritance *vs.* Nesting
~~~~~~~~~~~~~~~~~~~~~~~~~

If two molecules are related to each other this way: , then consider
using inheritance instead of nesting (or object composition). In this
example (with *Butane* and *TraPPE*) either nesting or inheritance would
work.

Again, one very minor advantage to nesting *Butane* inside *TraPPE*, is
that it prevents the name *Butane* from being confused with or
conflicting with any other versions of the *Butane* molecule defined
elsewhere. (Usually this is not a consideration.)

.. _sec:inheritance_vs_object_composition:

Inheritance *vs.* Object Composition
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On the other hand, if two molecules are related to each other this way:
, then you might consider using object composition instead of
inheritance. For example:

::

   import "B.lt"  # <-- defines the molecule type "B"

   import "C.lt"  # <-- defines the molecule type "C"

   A {
     b = new B
     c = new C
   }
