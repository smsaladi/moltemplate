Arrays, slices, and coordinate transformations
==============================================

Moltemplate supports 1-dimensional, and multi-dimensional arrays. These
can be used to create straight (or helical) polymers sheets, tubes,
tori. They are also to fill solid 3-dimensional volumes with molecules
or atoms. (See sections `4.2 <#sec:coords_intro>`__ and
`7.6 <#sec:multidimensional_arrays>`__.)

Here we show an easier way to create the short polymer shown in section
`[sec:2beadPolymer] <#sec:2beadPolymer>`__. You can make 7 copies of the
*Monomer* molecule this way:

::

     monomers = new Monomer[7]

This creates 7 new *Monomer* molecules (named , , , , ... ). You can
connect them with bonds in the same way we did earlier:

::

     write("Data Bonds") {
       $bond:backbone1  @bond:Backbone  $atom:monomers[0]/ca  $atom:monomers[1]/ca
       $bond:backbone2  @bond:Backbone  $atom:monomers[1]/ca  $atom:monomers[2]/ca
       $bond:backbone3  @bond:Backbone  $atom:monomers[2]/ca  $atom:monomers[3]/ca
            :                :               :                     :
     }

Unfortunately, by default, the coordinates of each molecule are
identical. To prevent the atom coordinates from overlapping, you have
several choices:

.. _sec:arrays+xform:

Transformations following brackets [] in a new statement
--------------------------------------------------------

After every square-bracket [] in a new command, you can specify a list
of transformations to apply. For example, we could have generated atomic
coordinates for the the short polymer in section
`[sec:2beadPolymer] <#sec:2beadPolymer>`__ using this command:

::

     monomers = new Monomer [7].rot(180, 1,0,0).move(3.2,0,0)

This will create 7 molecules. The coordinates of the first molecule
*monomers[0]* are will be unmodified. However each successive molecule
will have its coordinates cumulatively modified by the commands
“rot(180, 1,0,0)” followed by “move(3.2,0,0)”.

.. _sec:xform+arrays+xform:

optional: initial customizations (preceding [] brackets)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can also make adjustments to the initial coordinates of the molecule
before it is copied, and before any of the array transformations are
applied. For example:

::

     monomers = new Monomer.scale(1.5) [7].rot(180, 1,0,0).move(3.2,0,0)

In this example, the “scale(1.5)” transformation is applied once to
enlarge every *Monomer* object initially. This will happen before any of
the rotation and move commands are applied to build the polymer (so the
3.2 Angstrom spacings between each monomer will not be effected).

.. _sec:xform_after_instance:

Transformations following instantiation
---------------------------------------

Alternately you apply transformations to a molecule after they have been
created (even if they are part of an array).

::

     monomers = new Monomer [7]

     # Again, the first line creates the molecules named 
     # "monomers[0]", "monomers[1]", "monomers[2]", ... "monomers[6]".
     # The following lines move them into position.
     monomers[1].rot(180.0, 1,0,0).move(3.2,0,0)
     monomers[2].rot(360.0, 1,0,0).move(6.4,0,0)
     monomers[3].rot(540.0, 1,0,0).move(9.6,0,0)
     monomers[4].rot(720.0, 1,0,0).move(12.8,0,0)
     monomers[5].rot(900.0, 1,0,0).move(16.0,0,0)
     monomers[6].rot(1080.0, 1,0,0).move(19.2,0,0)

.. _sec:xform_order:

Transformation order (general case)
-----------------------------------

A typical array of molecules might be instantiated this way:

::

   mols = new Molecule.XFORMS1() [N].XFORMS2()
   mols[*].XFORMS3()

The list of transformations denoted by “XFORMS1” in this example are
applied to the molecule first. Then the transformations in “XFORMS2” are
then applied to each copy of the molecule multiple times. (For the
molecule with index “:math:`i`”, named “Molecule[:math:`i`]”, XFORMS2
will be applied :math:`i` times.) Finally after all the molecules have
been created, the list of transformations in XFORMS3 will be applied.
For example, to create a ring of 10 polymers of radius 30.0, centered at
position (0,25,0), use this notation:

::

   polymer_ring = new Polymer.move(0,30,0) [10].rot(36,1,0,0)
     # After creating it, we can move the entire ring 
     # (These commands are applied last.)
   polymer_ring[*].move(0,25,0)

.. _sec:random_arrays:

Random arrays
-------------

.. raw:: latex

   \centering

**a)** |[fig:random_heteropolymer] A random heteropolymer (c), composed
of of Monomer and Monomer3 monomer subunits (a and b) with (target)
probabilities 0.6 and 0.4. (However, due to random fluctuations, the
actual ratio in this case was 68% and 32%. To avoid this problem, see
section|\ `7.4.1 <#sec:random_exact>`__\ |.)| **b)**
|[fig:random_heteropolymer] A random heteropolymer (c), composed of of
Monomer and Monomer3 monomer subunits (a and b) with (target)
probabilities 0.6 and 0.4. (However, due to random fluctuations, the
actual ratio in this case was 68% and 32%. To avoid this problem, see
section|\ `7.4.1 <#sec:random_exact>`__\ |.)| **c)**
|[fig:random_heteropolymer] A random heteropolymer (c), composed of of
Monomer and Monomer3 monomer subunits (a and b) with (target)
probabilities 0.6 and 0.4. (However, due to random fluctuations, the
actual ratio in this case was 68% and 32%. To avoid this problem, see
section|\ `7.4.1 <#sec:random_exact>`__\ |.)|

Arrays of random molecules can be generated using the syntax. For
example, below we define a random polymer composed of 50 *Monomer* and
*Monomer3* monomer subunits. (See figure
`[fig:random_heteropolymer] <#fig:random_heteropolymer>`__.)

::

   RandPoly50 inherits ForceField {
     # Make a chain of randomly chosen monomers:

     monomers = new random([Monomer, Monomer3], [0.6, 0.4], 123456)
                    [50].rot(180,1,0,0).move(2.95, 0, 0)

     # Now, link the monomers together this way:
     write("Data Bonds") {
       $bond:bb1 @bond:Backbone $atom:monomers[0]/ca $atom:monomers[1]/ca
       $bond:bb2 @bond:Backbone $atom:monomers[1]/ca $atom:monomers[2]/ca
       $bond:bb3 @bond:Backbone $atom:monomers[2]/ca $atom:monomers[3]/ca
       $bond:bb4 @bond:Backbone $atom:monomers[3]/ca $atom:monomers[4]/ca

:math:`\quad \quad \quad \vdots`

::

       $bond:bb50 @bond:Backbone $atom:monomers[48]/ca $atom:monomers[49]/ca
     }
     #(Note: Both the "Monomer" and "Monomer3" subunits contain atoms
     #       named "$atom:ca".
   } #RandPoly50

It is also possible to fill a 2 or 3-dimensional volume with molecules
randomly. This is discussed in section `7.8 <#sec:random_advanced>`__.

The function takes 2 or 3 arguments: a list of molecule types ( and in
this example), and a list of probabilities (*0.6* and *0.4*) both
enclosed in square-brackets [].

.. _sec:random_exact:

Random arrays with exact molecule type counts
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Recall that we requested that 60% of the molecules be of type “Monomer”
and 40% type “Monomer3” (corresponding to 30 and 20, respectively).
However, the resulting polymer (shown in figure
`[fig:random_heteropolymer] <#fig:random_heteropolymer>`__) contains 34
“Monomer” and 16 “Monomer3” monomers (68% and 34%, respectively). This
is because each time a monomer is created, a random number is generated
to decide which type of monomer will be created. There is no guarantee
that the total final fraction of monomers will match the target
probabilities exactly (60% and 40%, respectively). To specify the number
of molecule types precisely, you can replace the list of probabilities
“[0.6,0.4]” with a list of integers “[30,20]”.

::

     monomers = new random([Monomer, Monomer3], [30, 20], 123456)
                    [50].rot(180,1,0,0).move(2.95, 0, 0)

This will create exactly 30 “Monomer” and 20 “Monomer3” monomers. (You
can do this with multidimensional arrays as well. See section
`7.9.1 <#sec:random_multidim_exact>`__.)

Details regarding the *new random* command:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*Note:* You can tell moltemplate to customize the bond-types and angles,
depending on the (types of) monomers are connected by each bond. The
“random_heteropolymer” example downloadable at
`www.moltemplate.org <www.moltemplate.org>`__ demonstrates how to do
this.

*Note:* Although this example, there are only two monomer types
(“Monomer” and “Monomer3”), there is no limit to the number of molecule
types which appear in these lists (eg “[Monomer, Monomer3,
4bead],[0.2,0.3,0.2]”)

*Note:* An optional random-seed argument can also be included. (For
example the shown above. If you omit this number, then you will get
different results each time you run moltemplate.)

*Note:* These lists can also contain vacancies/blanks. See section
`7.9 <#sec:random_vacancies>`__.)

*Note:* Once a molecule containing random monomers is defined, ( in this
example), each copy of that molecule (created using the *new* command)
is identical.

Optional: Customizing molecule positions in a *random()* array
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can customize the position of each type of molecule in the array,
before the array is constructed. To do this, you can add additional
movement commands after each molecule’s type name in the list (eg
“Monomer” and “Monomer3”):

::

     monomers = new random([Monomer.move(0,0.01,0),
                            Monomer3.move(0,-0.01,0)], 
                           [30,20],
                           123456)
                    [50].rot(180,1,0,0).move(2.95, 0, 0) 

The and suffixes moves these monomers closer or further away from the
polymer axis (the x axis in this example). This is not restricted to
commands. (You can also use , and commands as well.) These moves will be
applied (in order from left to right), *before* any of the and commands
appearing later (following “[50]”) are carried out.

.. _sec:array_wildcards_intro:

[*] and [i-j] slice notation
----------------------------

You can move the entire array of molecules using “[*]” notation:

::

     monomers[*].move(0,0,40)

(Note that “monomers.move(0,0,40)” does not work. You must include the
“[*]”.) You can also use range limits to move only some of the monomers:

::

     monomers[2-4].move(0,0,40)

This will move only the third, fourth, and fifth monomers. If you are
more familiar with python’s slice notation, you can accomplish the same
thing using:

::

     monomers[2:5].move(0,0,40)

(In this case, the second integer (eg “5”) is interpreted as a strict
upper bound.)

(If these customizations are not enough for your needs, you can also
always load atom coordinates from an external PDB or XYZ file. Such
files can be generated by PACKMOL, or a variety of advanced graphical
molecular modeling programs. For complex systems, this may be the best
choice.)

Building arrays one interval at a time (using slice notation)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

For a more complicated example, you can build polymers using slice
notation. The example below demonstrates how to build a polymer,
specifying which part is random, and and which part is not:

::

     monomers[0]    = new Monomer3
     monomers[1-48] = new random([Monomer, Monomer3], [30, 18], 123456)
                          [48].rot(180,1,0,0).move(2.95, 0, 0)
     monomers[49]   = new Monomer3
     # It's a good idea to move these monomers to keep them from overlapping
     monomers[0].rot(180,1,0,0)
     monomers[1-48].move(2.95,0,0)
     monomers[49].move(144.55,0,0)    #(note: 144.55=49*2.95)

In this example, we insure that monomers[0] and monomers[49] are both of
type “Monomer3” (while keeping the total number of “Monomer” and
“Monomer3” monomers at 30 and 20, respectively).

*(Note: You can replace “monomers[1-48]” with “monomers[1:49]”, or
“monomers[1*48]”, if you prefer that syntax style. You can build
multidimensional arrays using slice notation as well, for example
“molecules[3][10-19][4-6] = new Molecule[10][3]”)*

.. _sec:multidimensional_arrays:

Multidimensional arrays
-----------------------

The same techniques work with multidimensional arrays. Coordinate
transformations can be applied to each layer in a multi-dimensional
array. For example, to create a cubic lattice of 3x3x3 polymers: you
would use this syntax:

::

   molecules = new Polymer [3].move(30.0, 0, 0)
                           [3].move(0, 30.0, 0)
                           [3].move(0, 0, 30.0)

(Similar commands can be used with rotations to generate objects with
cylindrical, helical, conical, or toroidal symmetry.)

Customizing individual rows, columns, or layers
-----------------------------------------------

Similarly, you can customize the position of individual polymers, or
layers or columns using the methods above:

::

   molecules[1][*][*].move(0,20,0)
   molecules[*][1][*].move(0,0,20)
   molecules[*][*][1].move(20,0,0)

See figure `[fig:2bead_polymer] <#fig:2bead_polymer>`__\ c) *(You can
also use slice notation, eg “molecules[1][0-2][0-1].move(20,0,0)”)*

You can delete part of an array and replace it with something else (eg
*“Lipid”*) using slice notation:

::

   delete molecules[0-1][1][1-2] # (shorthand for delete molecules[0][1][1]
                                 #                delete molecules[0][1][2]
                                 #                delete molecules[1][1][1]
                                 #                delete molecules[1][1][2])

   # Now replace the array elements we deleted:
   molecules[0-1][1][1-2] = new Lipid [2].move(30,  0.0, 0.0)
                                      [2].move(0.0, 0.0, 30.0)

   # ...and move them back to the location of the vacancies we created
   molecules[0-1][1][1-2].move(0, 30.0, 30.0)

*The word “Lipid” in this example is not important. It is the name of
some other molecule type.*

.. _sec:random_advanced:

Creating random mixtures using multidimensional arrays
------------------------------------------------------

You can use to fill space with a random mixture of molecules. The
following 2-dimensional example creates a lipid bilayer (shown in figure
`[fig:random_bilayer] <#fig:random_bilayer>`__) composed of an equal
mixture of DPPC and DLPC lipids. (...Whose definition we omit here. See
the online examples for details.)

::

   import "lipids"                                     # define DPPC & DLPC
   lipids = new random([DPPC,DLPC], [0.5,0.5], 123)    # "123"=random_seed
                       [19].move(7.5,    0,     0)     # lattice spacing 7.5
                       [22].move(3.75, 6.49519, 0)     # hexagonal lattice
                        [2].rot(180, 1, 0, 0)          # 2 monolayers

.. raw:: latex

   \centering

**a)** |[fig:random_bilayer] A lipid bilayer membrane composed of a
random equal mixture of two different lipid types in a 1:1 ratio. (See
section|\ `7.8 <#sec:random_advanced>`__\ |.) In b) one of the molecule
types was left blank leaving vacancies behind. (See
section|\ `7.9 <#sec:random_vacancies>`__\ |.)| **b)**
|[fig:random_bilayer] A lipid bilayer membrane composed of a random
equal mixture of two different lipid types in a 1:1 ratio. (See
section|\ `7.8 <#sec:random_advanced>`__\ |.) In b) one of the molecule
types was left blank leaving vacancies behind. (See
section|\ `7.9 <#sec:random_vacancies>`__\ |.)|

.. _sec:random_vacancies:

Inserting random vacancies
--------------------------

The list of molecule types passed to the function may contain blanks. In
the next example, 30% of the lipids are missing:

::

   lipids = new random([DPPC, ,DLPC], [0.35,0.3,0.35], 123) # 2nd element is blank
                       [19].move(7.5,    0,     0)
                       [22].move(3.75, 6.49519, 0)
                        [2].rot(180, 1, 0, 0)     

The results are shown in figure
`[fig:random_bilayer] <#fig:random_bilayer>`__\ b). *(Note: When this
happens, the array will contain missing elements. Any attempt to access
the atoms inside these missing molecules will generate an error message,
however moving or deleting array entries using [*] or [i-j] notation
should be safe.)*

.. _sec:random_multidim_exact:

Random multidimensional arrays with exact type counts
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Due to random fluctuations the number of DPPC and DLPC lipids created
may not equal exactly 0.35 :math:`\times` of the number of entries in
the array,

Alternately, you can specify the exact number of DPPC and DLPC molecules
you desire (as opposed to a list of probabilities). To do this, replace
the list of probabilities with integers:

::

   lipids = new random([DPPC, ,DLPC], [293,250,293], 123)
                       [19].move(7.5,    0,     0)
                       [22].move(3.75, 6.49519, 0)
                        [2].rot(180, 1, 0, 0)     

This will generate exactly 293 DPPC and DLPC molecules (and 250 *blank*
entries, since the second molecule type was unspecified). The sum (ie
293+250+293) must equal the number of entries in the array you are
creating (ie 19x22x2).

.. _sec:delete_holes:

Cutting rectangular holes using **delete**
------------------------------------------

The delete command can be used to cut large holes in 1, 2, and
3-dimensional objects. For example, consider a simple 3-dimensional
12x12x12 cube of molecules. (For simplicity, each “molecule” in this
example contains only one atom. These atoms appear as blue spheres in
figure `[fig:delete_holes] <#fig:delete_holes>`__.)

::

   molecules = new OneAtomMolecule [12].move(3.0,0,0)
                                   [12].move(0,3.0,0)
                                   [12].move(0,0,3.0)

Then, we cut out some rectangular vacancies:

::

   delete molecules[*][*][2]      
   delete molecules[*][*][8]      
   delete molecules[6-7][0-8][5-6]

The result of these operations is shown in figure
`[fig:delete_holes] <#fig:delete_holes>`__. *(Note: You may move or
delete previously deleted array elements more than once, and/or deleting
overlapping rectangular regions without error.)*

.. raw:: latex

   \centering

.. figure:: _static/delete_holes1.jpg
   :alt:  [fig:delete_holes] Rectangular holes can be carved out of an array of molecules (represented here by blue spheres) using the “delete” command. Three delete commands were used to remove the two planar regions and the rectangular hole in the center.
   :width: 4.0cm

    [fig:delete_holes] Rectangular holes can be carved out of an array
   of molecules (represented here by blue spheres) using the “delete”
   command. Three delete commands were used to remove the two planar
   regions and the rectangular hole in the center. 
