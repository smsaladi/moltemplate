.. _sec:tree_man_page:

Advanced moltemplate.sh Usage
=============================

moltemplate.sh has several optional command line arguments. These are
explained in below:

::

   Usage:

   moltemplate.sh [-atomstyle style] \
                  [-pdb/-xyz/-raw coord_file] \
                  [-a assignments.txt] file.lt

   Optional arguments:

   -atomstyle style  By default, moltemplate.sh assumes you are using the "full"
                   atom style in LAMMPS.  You can change the atom style to "dipole"
                   using -atomstyle dipole.  If you are using a hybrid style, 
                   you must enclose the list of styles in quotes.  For example:
                   -atomstyle "hybrid full dipole"
                   For custom atom styles, you can also specify the
                   list of column names manually (enclosed in quotes):
                   -atomstyle "molid x y z atomid atomtype mux muy muz"
                   Be careful to enclose the entire list in quotes(").

   -raw raw_file   The raw_file file should contain the atomic coordinates in RAW format.
                   RAW files are simple 3-column ASCII files containin the coordinates
                   for the atoms in the system. (One line per atom, 3 numbers per line.
                   The atoms must appear in the same order in the data file.)
   -xyz xyz_file   An xyz_file argument should be supplied as an argument
                   following "-xyz".
                   This file should contain the atomic coordinates in xyz format.
                   (The atoms must appear in the same order in the data file.)

   -pdb pdb_file   The pdb_file file should contain the atomic coordinates in PDB format.

                   This file should contain one ATOM or HETATM record per atom.  Atoms

                   are sorted by chainID, resID, insertCode, atomID (in that order).
                   This order must match the order the atoms appear in the data file.

                   If the PDB file contains periodic boundary box information 
                   (IE., a "CRYST1" record), this information is also copied 
                   to the LAMMPS data file.  
                   (Support for triclinic cells is experimental as of 2019-9-03.
                   Other molecular structure formats may be supported later.
   -a "@atom:x 1"
   -a assignments.txt
                   The user can customize the numbers assigned to atom, bond,
                   angle, dihedral, and improper types or id numbers by using
                      -a "VARIABLE_NAME VALUE"
                   for each variable you want to modify.  If there are many
                   variables you want to modify, you can save them in a file
                   (one variable per line).  For an example of the file format
                   run moltemplate.sh once and search for a file named
                   "ttree_assignments.txt".  (This file is often located in
                   the "output_ttree/" directory.) Once assigned, the remaining
                   variables in the same category will be automatically assigned
                   to values which do not overlap with your chosen values.
   -b assignments.txt
                   "-b" is similar to "-a". However, in this case, no attempt 
                   is made to assign exclusive (unique) values to each variable.
   -nocheck
                  Normally moltemplate.sh checks for common errors and typos and
                  halts if it thinks it has found one.  This forces the variables
                  and categories as well as write(file) and write_once(file) 
                  commands to obey standard naming conventions.  The "-nocheck"
                  argument bypasses these checks and eliminates these restrictions.
   -checkff
                   This cause moltemplate.sh to check to make sure that there
                   are valid angle and dihedral interactions defined for every
                   3 or 4 consecutively bonded atoms in the system
                   (defined in "Angles/Dihedrals By Type").

.. _sec:manual_assignment:

Manual variables assignment (“-a” or “-b”)
------------------------------------------

It is possible to manually customize the values assigned to the atom
types (or to any other ttree-style variables). For example, consider the
the “spce.lt” file shown earlier. This file defines a single water
molecule with two atom types (hydrogen and oxygen). Typically the “O”
atom type is normally assigned to the integer “1”, and “H” would be
assigned to “2”. This is because “O” appears before “H” in that file. If
you wanted to swap the order, you could swap the order in which they
first appear.

Alternately you can specify the atom assignments directly using one or
more “-a” flags followed by a quoted assignment string:

::

   moltemplate.sh -a '@atom:SPCE/O 2' system.lt

This assigns the oxygen atom type to “2”. Note that quotes are necessary
around the ’@atom:SPCE/O 2’ string, which is a single argument. (Also
note that it is necessary to include SPCE/ before the O, because in that
example, this atom appeared (and was thus defined) inside the SPCE
molecule’s environment. Alternately, if it had been defined outside,
globally, then you could refer to it using “@atom:O”)

Variables need not be assigned to numbers. If for some reason, you want
to substitute “a string” everywhere this atom type appears, you would do
it this way:

::

   moltemplate.sh -a '@atom:SPCE/O "a string"' system.lt

Multiple assignments can be made by using multiple “-a” flags:

::

   moltemplate.sh -a '@atom:SPCE/O 2'  -a '@atom:SPCE/H 1' system.lt

However if you have a large number of assignments to make, it may be
more convenient to store them in a file. You can create a two-column
text file (for example “new_assignments.txt”) and run moltemplate this
way:

::

   moltemplate.sh -a new_assignments.txt system.lt

The contents of the “new_assignments.txt” file in this example would be:

::

   @atom:SPCE/O  2
   @atom:SPCE/H  1

The order of lines in this file does not matter.

Assigning $angle, $dihedral, $improper variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In general any kind of variable can be assigned this way (not only atom
types), including $mol, $bond, @bond, @angle, $angle, ... as well as
user-defined variable type. *Caveat: The only occasional exceptions are
the $angle, $dihedral, $improper variables.* (When “Angles By Type”
interactions are selected by the user, and mixed with regular “Angles”,
all of the $angle variables are automatically generated. The same is
true for “Dihedrals By Type” and “Impropers By Type”. See section
`[sec:nbody_by_type_utility] <#sec:nbody_by_type_utility>`__ for an
explanation of “By Type” interactions.)

Angles, dihedrals, and impropers interactions are automatically
generated, and in this case the user does not have the freedom to assign
these variables.

The “-b” flag
~~~~~~~~~~~~~

Note that when using the “-a” flag above, care will be taken to insure
that the assignment(s) are exclusive. None of the atom types (other than
@atom:SPCE/O) will be assigned “2”. (For this reason, using the “-a”
flag to change the atom type assignments can, in principle, alter the
numbers assigned other atom types, or variables.) This usually the
desired behavior. However suppose, for some reason, that you wanted to
force a variable assignment, so that other variables in the same
category are not effected. In that case, you can use the “-b” flag:

::

   moltemplate.sh -b '@atom:SPCE/O 2' system.lt

Keep in mind, that in this example, this could cause other atom-types
(for example “@atom:SPCE/H”) to be assigned to overlapping numbers.

.. _sec:ttree_assignments:

The “ttree_assignments.txt” file
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Generally, after running moltemplate.sh, a “ttree_assignments.txt” file
will be created (or updated if it is already present) to reflect any
changes you made. (This file is usually located in the “output_ttree/”
directory. It can also be located the current directory “./”.) You can
always check this to make sure that the atom types (or any other ttree
variables) were assigned correctly.

The “ttree_assignments.txt” file has the same format as the
“new_assignments.txt” file example above.

*Note:* In both files, an optional slash, “/”, may follow the “@” or “$”
characters, as in “@/atom:SPCE/O”. (This slash is optional and indicates
the environment in which the counter is defined. The “@atom” counter is
defined globally. The “$resid” counter example described in section
`14.2 <#sec:custom_categories>`__ is not.)

*Bug-warning: Using the “delete” command may cause some of the instance
variables (specifically the $atom, $mol, $bond, $angle, $dihedral, and
$improper variables) to be numbered incorrectly. However static
variables (beginning with @) should always be accurate. -Andrew
2019-9-03.*

lttree.py and ttree.py also accept “-a” and “-b” flags
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If for some reason, you are using “lttree.py” or “ttree.py” instead of
“moltemplate.sh”, then the “-a” and “-b” flags explained here also work
with these scripts. They are not specific to moltemplate.sh.

.. _sec:custom_categories:

Customizing the counting method using *category*
------------------------------------------------

Variables in “.lt” files are assigned to integers by default, starting
with 1, and incrementing by 1. This can be overridden using the
“category” command. For example, to create a new variable category named
“distance” which starts at :math:`0` and increments by :math:`0.5`, you
would include this command in your LT file:

::

   category $distance(0.0, 0.5)

(This command should *not* be used with traditional counter categories
like *$atom, $bond, $angle, $dihedral, $improper, $mol, @atom, @bond,
@angle, @dihedral,* and *@improper*.)

.. _sec:cpath_simple:

Creating local independent counters
-----------------------------------

By default variables in a given category are always assigned to unique
integers. This can be overridden using the “category” command. For
example, you might have a variable that keeps track of the monomer in
every polymer. The first monomer in a polymer is assigned “1”, the
second monomer, “2”, etc, *regardless* of the number of polymer in your
system.

To do this, we can create a new variable category named “monomerid”
which is defined within the scope of each instance of the “Polymer”
molecule:

::

   Monomer {
     write("Data Atoms") {
       $atom:ca @atom:CA $monomerid:.  0.0  0.0 0.0 0.0
       $atom:cb @atom:CB $monomerid:.  0.0  1.53 0.0 0.0
     }
   }

   Polymer {
     category $monomerid(1,1)
     monomers = Monomer[100]
   }

   polymers = Polymer[10]

In this example, there are 10 polymers containing 100 monomers each. The
“$monomerid” counters will be replaced with integers in the range
:math:`1\ldots 100`, (not :math:`1\ldots 1000`, as you might expect).
Because the “$monomerid” counter is local to the protein it is defined
within, “$monomerid” variables in other proteins do not share the same
counter, and can overlap.

.. _sec:order:

Counting order
--------------

Most variables are assigned automatically. By default static variables
(@) are assigned in the order they appear in the file (or files, if
multiple LT files are included). Subsequently, instance variables ($)
are assigned in the order they are created during instantiation. However
you can customize the order in which they are assigned.

Ordering
~~~~~~~~

LT files are parsed by moltemplate.sh/lttree.py in multiple stages. The
“write_once()” and “write()” commands are carried out in the static and
instance phases respectively, as explained below.

The *static* phase
~~~~~~~~~~~~~~~~~~

In the “static” phase, “write_once()” statements are carried out in the
order they are read from the user’s input file(s) (regardless of whether
or not they appear in nested classes). Any “include” commands will
effect this order. After processing the class definitions, and carrying
out the “write_once()” commands, lttree.py begins the instantiation
phase.

The *instantiation* phase
~~~~~~~~~~~~~~~~~~~~~~~~~

During this phase, lttree.py makes copies of (instantiates) classes
which were requested by the user using the “new” command. During this
stage, lttree.py also appends data to files using the “write” command.
(In this manual, the “write()” and “new” are called instance commands.)
The sequence of alternating “write()” and “new” commands in the order
that they appear in the user’s input file(s). “new” commands recursively
invoke any instance commands for each copy of the class they create.

Again, the counting of instance variables (prefixed by “$”) does not
interfere with static variable assignment. For example “@atom:x” and
“$atom:x” correspond to different variables and belong to different
variable categories (“@atom” and “$atom”) and they are assigned to
numerical values independently.
