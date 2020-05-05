Bonded interactions “By Type”
=============================

Interactions between atoms in LAMMPS which are not bonded together (ie
“non-bonded” or “pair” interactions) are specified *by atom type*.
*Bonded interactions* in LAMMPS, (including 3-body angle, and 4-body
dihedral and improper interactions), are specified by unique *atom ID
number*. (There are typically a large number of angles and bonds in a
typical molecule, and this information occupies the majority of in a
typical LAMMPS data file.)

This has changed in moltemplate.sh. moltemplate.sh contains a utility
which can generate angles, dihedrals, and impropers automatically by
atom and bond *type*. (This utility is described in section
`[sec:nbody_by_type_utility] <#sec:nbody_by_type_utility>`__.)
moltemplate.sh will inspect the network of bonds present in your system,
detect all 3-body, and 4-body interactions, and determine their type.
(Higher n-body interactions can also be defined by the user.) Specifying
interactions this way can eliminate significant redundancy since many
atoms share the same type.

To make use of this feature, you would create a new section named , , or
whose syntax mimics the , , and sections of a LAMMPS data file. The
syntax is best explained by example:

::

   write("Data Angles By Type") {
     @angle:XCXgeneral       *      *C*      *
     @angle:CCCgeneral    @atom:C @atom:C @atom:C    *         *
     @angle:CCCsaturated  @atom:C @atom:C @atom:C @bond:SAT @bond:SAT
   }

The first line will generate a 3-body angle interaction (of type )
between any 3 consecutively bonded atoms as long as the second atom’s
type-name contains the letter “C”. (Atom and bond type-names can contain
wildcard characters \*)

The second line will generate a 3-body interaction of type between any 3
atoms of type , regardless of the type of bonds connecting them. (The
last two columns, which are both wildcard characters, \*, tell
moltemplate.sh to ignore the two bond types. Since this is the default
behavior these two columns are optional and can be omitted.)

The third line will generate a 3-body interaction of type between any 3
atoms of type , if they are connected by bonds of type .

Note: The 2nd and 3rd lines in this example will generate new
interactions which may override any angle interactions assigned earlier.

Regular expressions
-------------------

Regular-expressions can also be used to match potential atom and bond
types. (To use regular expressions, surround the atom and bond types on
either side by slashes. For example: , should match through .) *Note:
This feature has not been tested as of 2019-9-03.*

In a similar way, one can define “Dihedrals By Type” and “Impropers By
Type”.
