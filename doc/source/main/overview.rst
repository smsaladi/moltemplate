Overview
========

.. _sec:write:

Basics: The *write()* and *write_once()* commands
-------------------------------------------------

Each LT file typically contains one or more “write” or “write_once”
commands. These commands have the following syntax

::

   write_once(filename) {text_block}

This creates a new file with the desired file name and fills it with the
text enclosed in curly brackets {}. Text blocks usually span multiple
lines and contain counter variables (beginning with “@” or “$”). which
are replaced with numbers. However the “write()” command will repeatedly
append the same block of text to the file every time the molecule (in
which the write command appears) is generated or copied (using the “new”
command, after incrementing the appropriate counters, as explained in
`5.2.2 <#sec:instance_variables>`__).

.. _sec:variables:

Basics: counter variables
-------------------------

Words following a “@” or a “$” character are *counter variables*. (These
are not to be confused with *LAMMPS variables*
http://lammps.sandia.gov/doc/variable.html). By default, *all counter
variables are substituted with a numeric counter* before they are
written to a file. These counters begin at 1 (by default), and are
incremented as the system size and complexity grows (see below).

These words typically contain a colon (:) followed by more text. The
text preceding this colon is the *category name*. (For example:
“$atom:”, “$bond:”, “$angle:”, “@atom:”, “@bond:”, “@angle:”) Variables
belonging to different categories are counted independently.

Users can override these assignment rules and create custom categories.
(See appendices `14.1 <#sec:manual_assignment>`__ and
`14.2 <#sec:custom_categories>`__ for details.)

.. _sec:static_variables:

Static counters begin with “@”
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

“@” variables generally correspond to *types*: such as atom types, bond
types, angle types, dihedral types, improper types. These are simple
variables and they assigned to unique integers in the order they are
read from your LT files. Each uniquely named variable in each category
is assigned to a different integer. For example, “@bond:” type variables
are numbered from “1” to the number of *bond types*. (Pairs of bonded
atoms are assigned a *bond type*. Later, LAMMPS will use this integer to
lookup the bond-length and Hooke’s-law elastic constant describing the
force between these two atoms.)

.. _sec:instance_variables:

Instance counters begin with “$”
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

On the other hand, “$” variables correspond to unique ID numbers:
atom-IDs, bond-IDs, angle-IDs, dihedral-IDs, improper-IDs, and
molecule-IDs. These variables are created whenever a copy of a molecule
is created (using the “new” command). If you create 1000 copies of a
water molecule using a command like

::

   wat = new SPCE[10][10][10]

then moltemplate creates 3000 “$atom” variables with names like

::

   $atom:wat[0][0][0]/o
   $atom:wat[0][0][0]/h1
   $atom:wat[0][0][0]/h2
   $atom:wat[0][0][1]/o
   $atom:wat[0][0][1]/h1
   $atom:wat[0][0][1]/h2

:math:`\quad \vdots`

::

   $atom:wat[9][9][9]/o
   $atom:wat[9][9][9]/h1
   $atom:wat[9][9][9]/h2

.. _sec:full_names:

Variable names: short-names *vs.* full-names
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In the example above, the $ variables have full-names like
“$atom:wat[8][3][7]/h1”, not “$atom:h1”. However inside the definition
of the water molecule, you don’t specify the full name. You can refer to
this atom as “$atom:h1”. Likewise, the full-name for the @atom variables
is actually “@atom:SPCE/H”, not “@atom:H”. However inside the definition
of the water molecule, you typically use the shorthand notation
“@atom:H”.

Numeric substitution
~~~~~~~~~~~~~~~~~~~~

Before being written to a file, every variable (either $ or @) with a
unique *full-name* will be assigned to a unique integer, starting at 1
by default.

The various $atom variables in the water example will be substituted
with integers from 1 to 3000 (assuming no other molecules are present).
But the “@atom:O” and “@atom:H” variables (which are shorthand for
“@atom:SPCE/O” and “@atom:SPCE/H”) will be assigned to to “1” and “2”
(again, assuming no other molecule types are present).

So, in summary, @ variables increase with the *complexity* of your
system (IE the number of molecule types or force-field parameters), but
$ variables increase with the *size* of your system.

.. _sec:variable_scope:

Variable scope
~~~~~~~~~~~~~~

This effectively means that all variables are specific to local
molecules they were defined in. In other words, an atom type named
“@atom:H” inside the “SPCE” molecule, will be assigned to a different
number than an atom named “@atom:H” in an “Arginine” molecule. This is
because the two variables will have different *full* names
(“@atom:SPCE/H”, and “@atom:Arginine/H”).

Sharing atom types or other variables between molecules
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

There are several ways to share atom types between two molecules. The
*recommended way* is to define them in a separate file and refer to them
when needed. This approach is demonstrated in section
`6.1 <#sec:2bead>`__.

*(Alternately, you can define them outside the current molecule
definition, and use file-system-path-like syntax (“../”, or “../../” or
“/”) to access atoms (or molecules) outside of the current molecule. For
example, two different molecule types can share the same type of
hydrogen atom by referring to it using this syntax: “@atom:../H”. For
details, see section*\ `9.6 <#sec:paths>`__\ *. and
appendix*\ `17 <#sec:adv_variable_syntax>`__\ *.)*

.. _sec:output_ttree:

Troubleshooting using the *output_ttree* directory
--------------------------------------------------

Users can see what numbers were assigned to each variable by inspecting
the contents of the “output_ttree” subdirectory created by moltemplate.
Unfortunately, it not unusual for LAMMPS to crash the first time you
attempt to run it on a DATA file created by moltemplate. This often
occurs if you failed to spell atom types and other variables
consistently. The LAMMPS error message (located at the end of the
“log.lammps” file created by LAMMPS) will help you determine what type
of mistake you made. (For example, what type of variable was misspelled
or placed in the wrong place?)

To help you, the “output_ttree” directory contains a file named
“ttree_assignments.txt”. This is a simple 2-column text file containing
a list of *all* of the variables you have created in one column, and the
numbers they were assigned to in the second column. (There is also a
comment on each line beginning with a “#” character which indicates the
file and line number where this variable is first used.)

The “output_ttree” directory also contains all of the files that you
created. The versions with a “.template” extension contain text
interspersed with *full* variable names (before numeric substitution).
(A spelling mistake, like using “$atom:h” when you meant to say
“$atom:h1” or “@atom:H” will show up in these files if you inspect them
carefully.) This can help you identify where the mistake occurred in
your LT files.

Once a molecular system is debugged and working, users can ignore or
discard the contents of this directory.

.. _sec:DataIn:

“Data” and “In”
---------------

Again, LAMMPS requires an *input script* and a *data file* to run.
*Moltemplate’s job is to generate these files.* *Input scripts*
typically contain force-field styles, parameters and run settings *Data
files* typically contain atom coordinates and bonded topology data.

If you are familiar with LAMMPS, you may have noticed the file names
above (in the example from section `4.1 <#sec:spce_example>`__) sound
suspiciously like sections from LAMMPS data files or input scripts, such
as “Data Boundary”, “Data Atoms”, “Data Bonds”, “Data Masses”, “Data
Angles”, “Data Dihedrals”, “Data Impropers”, “In Init”, “In Settings”).
All files whose names begin with “In” or “Data” are special. For the
user’s convenience, the moltemplate.sh script copies the contents of
these files into the corresponding section (“Atoms”, “Bonds”, “Angles”,
etc.) of the DATA file or INPUT scripts generated by moltemplate
(“system.data”, “system.in.settings”, etc). (Then the original files are
moved to the “output_ttree/” directory, in an effort to clean things up
and hide them from view.) Users can create their own custom sections to
a LAMMPS data file. (See section `5.6 <#sec:custom_data>`__.

More generally, the “write()” and “write_once()” commands can be used to
create any other files you may need to run your simulations, which refer
to the same *@atom* and *@bond* types. (See section
`5.5 <#sec:aux_files>`__ for an example.)

.. _sec:aux_files:

*(Advanced)* Using moltemplate to generate auxiliary files
----------------------------------------------------------

The following excerpt from an LT file creates a file named
“system.in.sw”. (It contains parameters for the “sw” pair style. This
exotic many-body pair style requires a large number of parameters, which
are read from a separate file.) This “system.in.sw” file file will be
read later when you run the simulation. (The pair_coeff command below
tells LAMPS to read that file.)

::

   write_once("system.in.sw") {
     mW mW mW 6.189 2.3925 1.8 23.15 1.2 -0.33333 7.04956 0.602224 4 0 0
   }
   write_once("In Settings") {
     pair_coeff * * sw system.in.sw mW NULL NULL NULL
   }

As new force-field styles and/or fixes are added to LAMMPS, the files
they depend on can be embedded in an LT file in this way.

.. _sec:custom_data:

*(Advanced)* Making custom DATA sections
----------------------------------------

Suppose that in the future, the format of the LAMMPS DATA file changes
so that it now becomes necessary to supply a new section named “Foo Fee
Fum”, for example. You could do that using this command:

::

   write_once("Data Foo Fee Fum") {
     File contents goes here. (These files can contain
     atom counters and/or other counter variables).
   }

This way moltemplate copy this text into the “Foo Fee Fum” section at
the end of the DATA file it is constructing. This allows users to adapt
to future changes in the LAMMPS data file format.

.. _sec:vardetails:

Does “@atom:H” conflict with “$atom:H”?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

No. It is okay for static(@) and instance($) variables to share the same
names. (Moltemplate considers them distinct variables and they will be
assigned independently.)

Addional Details
~~~~~~~~~~~~~~~~

Variable and molecule names can include unicode characters. They can
also include some whitespace characters and other special characters by
using backslashes and curly-brackets, for example: “@{atom: CA }” and
“@atom:\ CA\ ”. Curly-brackets are useful to clarify when a variable
name begins and ends, such as in this example: “@{atom:C}@{atom:H}”.
This prevents the “\*” character from being appended to the end of the
“C” variable name. (Note that using the “\*” character in any of the
*coeff* commands within moltemplate is discouraged. See section
`[sec:wildcard_bug] <#sec:wildcard_bug>`__.)

*(Unicode is supported.)*