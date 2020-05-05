.. |[fig:mol_complex] a) The “MolecularComplex” molecule. This is a contrived example consisting of two “Polymers”. See section| image:: /_static/mol_complex_LR.jpg
   :height: 3cm

.. |[fig:mol_complex] a) The “MolecularComplex” molecule. This is a contrived example consisting of two “Polymers”. See section| image:: /_static/mol_complex+mol_complex0_transparent_LR.jpg
   :height: 3cm

.. _sec:custom_xform:

Customizing molecule position and topology
==========================================

By default, each copy of a molecule created using the *new* command is
identical. This need not be the case.

As discussed in section `7.2 <#sec:xform_after_instance>`__, individual
molecules which were recently created can be moved, rotated, and scaled.
You can also overwrite or delete individual atoms, bonds, and other
interactions within a molecule, or their subunits. (See sections
`8.3.2 <#sec:delete_atoms_bonds>`__, `8.1.1 <#sec:custom_atom>`__,
`8.1.2 <#sec:custom_bond>`__, and `8.2 <#sec:adding_atoms_bonds>`__.)
You make any of these modifications to *some* copies of the molecule
without effecting other copies. Furthermore, if those molecules are
compound objects (if they contain individual molecular subunits within
them), then you can rearrange the positions of their subunits as well.
And all of this can be done from anywhere else in the LT file.

For example, suppose we used the “Polymer” molecule we defined above to
create a larger, more complicated “MolecularComplex” molecule.

::

   MolecularComplex inherits ForceField {
     polymers[0] = new Polymer
     polymers[1] = new Polymer.rot(180,1,0,0).move(0, 12.4, 0)
   }
   mol_complex = new MolecularComplex

The *MolecularComplex* molecule is shown in figure
`[fig:mol_complex] <#fig:mol_complex>`__\ a). *Optional: If you want all
the atoms in a “MolecularComplex” to share the same molecule-ID, then
define “MolecularComplex” this way:*

::

   MolecularComplex inherits ForceField {
     create_var { $mol }
     polymers[0] = new Polymer
     polymers[1] = new Polymer.rot(180,1,0,0).move(0, 12.4, 0)
   }

*For this to work, you must also delete the line from the definition of
the Polymer molecule. See section*\ :ref:`sec:2bead`\ *.*

We can subsquently customize the position of the 3rd monomer
(“monomers[2]”) of the second polymer (“polymers[1]”), this way:

::

   mol_complex/polymers[1]/monomers[2].move(0,0.2,0.6)

This does not effect the position of *monomers[2]* in *polymers[0]*. (or
in any other *“Polymer”* or *“MolecularComplex”* molecule.) If you want
to do the same thing for both polymers, you could use a wildcard
character “\*”

::

   mol_complex/polymers[*]/monomers[2].move(0,0.2,0.6)

If you want to move both polymers, you can use:

::

   mol_complex/polymers[*].move(0,0.2,0.6)

you could use a wildcard character “\*” (You can also use ranged (slice)
notation, such as “polymers[0-1]”, as an alternative to “polymers[*]”.
See section `7.5 <#sec:array_wildcards_intro>`__.

To make changes that apply to every subsequently created *“Polymer”* or
*“MolecularComplex”* molecule, see section
`8.4 <#sec:molecule_customization>`__.)

Customizing individual atoms or bonds
-------------------------------------

.. _sec:custom_atom:

Customizing individual atom locations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The “move” or “rot” commands can not be used to control the positions of
*individual atoms*. Instead simply overwrite their coordinates this way:

::

   write("Data Atoms") {
     $atom:mol_complex/polymers[0]/monomers[2]/ca $mol:mol_complex @atom:R 0 6.4 8.2 0.6
   }

This is an case where we must use the *full* variable name of the atom
(“$atom:mol_complex/polymers[0]/monomers[2]/ca”) to indicate *which*
“ca” atom we want to overwrite (In this case, we want to modify the “ca”
atom belonging to “monomers[2]” in “polymers[0]” in “mol_complex”).

.. _sec:custom_bond:

Customizing individual bonds, angles, dihedrals,...
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Similarly, you can customize existing bonds, angles, etc... by
overwriting the line containing a reference to that particular bond (or
angle, ...). For example, you can increase the rest length of the spring
representing the 3rd backbone bond in the first polymer, by changing it
from type “@bond:Backbone” to “@bond:LongBond”

::

   write("Data Bonds") {
     $bond:mol_complex/polymers[0]/backbone3 @bond:ForceField/LongBond &
         $atom:mol_complex/polymers[0]/monomers[2]/ca &
         $atom:mol_complex/polymers[0]/monomers[3]/ca 
   }

Note: The optional “&” character is used in LAMMPS to split a long
command into multiple lines. This command is quite long since we are
outside the definition of the polymer molecule, and we must provide the
full-path for every variable (for example “mol_complex/polymers[0]”, and
“ForceField”).

Alternatively, if you prefer, you can *delete* the bond and define a new
bond connecting the same pair of atoms (see section
:ref:`sec:delete`).

Of course, at some point you must also create a new “bond_coeff” command
defining the properties of this new type of bond (for example to
increase the length of the new spring). You can either edit the
“forcefield.lt” file, *or* simply add the following lines of text
somewhere in one of your .LT files (for example “system.lt”)

::

   # Note: This will augment the "ForceField" object, not overwrite it:
   ForceField { 
     write_once("In Settings") {
       #             new bond-type   k    r0
       bond_coeff  @bond:LongBond   10.0  4.8
     }
   }

.. _sec:adding_atoms_bonds:

Adding bonds and angles to individual molecules
-----------------------------------------------

Adding additional bonds within a molecule can be accomplished by writing
additional lines of text to the “Data Bonds” section. (This is what we
did when we added bonds between monomers to create a polymer in section
:ref:`sec:twobead_polymer`.) Again, bonds and atom names
must be referred to by their *full* names. Bonds and bonded interactions
can be deleted using the “delete” command. (See section
:ref:`sec:delete`.)

.. _sec:delete:

The **delete** command
----------------------

Deleting molecules or molecular subunits
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Molecules can be further customized by deleting individual atoms, bonds,
bonded-interactions, and entire subunits. We can **delete** the 3rd
monomer of the second polymer, use the “delete” command:

::

   delete mol_complex/polymers[1]/monomers[2]

.. _sec:delete_atoms_bonds:

Deleting atoms, bonds, angles, dihedrals, and impropers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Individual atoms or bonds can be deleted in a similar way:

::

   delete mol_complex/polymers[1]/monomers[3]/ca   #<-- deletes the "ca" atom
   delete mol_complex/polymers[1]/monomers[4]/cr   #<-- deletes the "cr" bond

Whenever an atom or a molecule is deleted, the bonds, angles, dihedrals,
and improper interactions involving those atoms are deleted as well.
*Note: You must omit the “$” character when deleting atoms, bonds, or
angles, as we did in the two lines above).*

When a bond is deleted, any angular, dihedral, or improper interactions
which were *automatically* generated by moltemplate are removed as well.
(However other bonded interactions explicitly listed by the user in
their “Data Angles”, “Data Dihedrals”, or “Data Impropers” sections are
not removed. These need to be deleted manually.)

Multiple molecules can moved or deleted in a single command. For
example, the following command deletes the third, fourth, and fifth
monomers from both polymers[0] and polymers[1]:

::

   delete mol_complex/polymers[*]/monomers[2-4]

See section `7.5 <#sec:array_wildcards_intro>`__ for an explanation of
ranged (“[2-4]”) array notation, and wildcard characters (“\*”).

*Minor bug notice: Deleting atoms or molecules may cause inaccuracies in
the $atoms, $bonds, $angles, $dihedrals, and $impropers sections of the
“ttree_assignments.txt” file. (If this is a problem, please email me.
-Andrew 2019-9-03.) Fortunately, this should not harm the resulting
LAMMPS data files or input scripts generated by moltemplate. They should
still work with LAMMPS.*

*WARNING: The*\ **delete**\ *feature is experimental. There have been a
few bugs in the*\ **delete**\ *command, but by 2019-9-03 these should be
fixed. Please report any problems you find. As always, be sure to
visualize your structures to make sure they look reasonable. (...by
running moltemplate.sh using the “-vmd” command line option, for
example. See
sections*\ :ref:`sec:vmd_topotools`\ *,*\ :ref:`sec:vmd_advanced`\ *for
details.)*

.. _sec:molecule_customization:

Customizing molecule *types*
----------------------------

You can create modified versions of existing molecule *types*, without
having to redefine the entire molecule. For example:

::

   MolecularComplex0 = MolecularComplex.move(-9.6,-6.2, 0).scale(0.3125)

or equivalently:

::

   MolecularComplex0 = MolecularComplex
   MolecularComplex0.move(-9.6,-6.2, 0).scale(0.3125)

This creates a new type of molecule named “MolecularComplex0” whose
coordinates have been centered and rescaled. (Note that the “scale()”
command only effects the atomic coordinates. (You will have to override
earlier force field settings, such as atomic radii and bond-lengths in
order for this to work properly.) If we want to make additional
customizations (such as adding atoms, bonds, or molecular subunits), we
could use this syntax:

::

   MolecularComplex0 = MolecularComplex

   # Add some new atoms connecting the two polymers in the mol_complex

   MolecularComplex0 inherits ForceField {
     write("Data Atoms") {
       $atom:t1 $mol:. @atom:CA   0.0   23.0   0.0     0.0
       $atom:t2 $mol:. @atom:CA   0.0   24.7   4.0     0.0
       $atom:t3 $mol:. @atom:CA   0.0   24.7   8.4     0.0
       $atom:t4 $mol:. @atom:CA   0.0   23.0   12.4    0.0
     }
     write("Data Bonds") {
       $bond:b1 @bond:Backbone $atom:polymers[0]/res7/CA $atom:t1
       $bond:b2 @bond:Backbone $atom:t1 $atom:t2
       $bond:b3 @bond:Backbone $atom:t2 $atom:t3
       $bond:b4 @bond:Backbone $atom:t3 $atom:t4
       $bond:b5 @bond:Backbone $atom:t4 $atom:polymers[1]/res7/ca
     }
   }

   # Center and rescale the atoms in all "MolecularComplex0"
   MolecularComplex0.move(-9.6,-6.2, 0).scale(0.3125)

The result of these modifications is shown in figure
`[fig:mol_complex] <#fig:mol_complex>`__\ b).

.. raw:: latex

   \centering

**a)** |[fig:mol_complex] a) The “MolecularComplex” molecule. This is a
contrived example consisting of two “Polymers”. See
section|\ :ref:`sec:twobead_polymer`\ |b) A customized
version of the “MolecularComplex” molecule. (The original
“MolecularComplex” is shown faded in the background for comparison.)|
**b)** |[fig:mol_complex] a) The “MolecularComplex” molecule. This is a
contrived example consisting of two “Polymers”. See
section|\ :ref:`sec:twobead_polymer`\ |b) A customized
version of the “MolecularComplex” molecule. (The original
“MolecularComplex” is shown faded in the background for comparison.)|

*Note: These coordinate transformations will be
applied*\ **after**\ *the molecule is constructed. (If you add atoms to
the molecule, these will be added before the coordinate transformations
are applied, even if you issue the command later.) Consequently, to make
things clear, I recommend placing the coordinate transforms applied to
an entire molecule type*\ **after**\ *all of its internal details
(bonds, atoms, subunits) have been declared, as we did here.*

.. _sec:inheritance_intro:

*(Advanced)* Inheritance
~~~~~~~~~~~~~~~~~~~~~~~~

The *MolecularComplex0* molecule is a type of *MolecularComplex*
molecule. For those who are familiar with programming, relationships
like this are analogous to the relationship between parent and child
objects in an object-oriented programming language. More general kinds
of inheritance are supported by moltemplate and are discussed in section
:ref:`sec:inheritance`.

*(Advanced)* Multiple Inheritance
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If we wanted, we could have created a new molecule type (like
*“MolecularComplex0”*) which includes atom types and features from
*multiple* different types of molecules. Section
:ref:`sec:inheritance` mentions one way to do this and section
`9.8.3 <#sec:inheritance_vs_object_composition>`__ discusses alternate
approaches.