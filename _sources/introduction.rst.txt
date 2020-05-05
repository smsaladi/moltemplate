.. _sec:introduction:

Introduction
============

Moltemplate is a general molecule builder and force-field database
system for LAMMPS. A simple file format has been created to store
molecule definitions and force-fields (the LAMMPS-template format,
“LT”). LT files are templates containing *all* of the text relevant to a
particular molecule (including coordinates, bond-topology, angles,
force-field parameters, constraints, groups and fixes). Moltemplate can
then duplicate the molecule, customize it, and use it as a
building-block for constructing larger, more complex molecules. (These
molecules can be used to build even larger molecules.) Once built,
individual molecules and subunits can be customized (atoms and bonds,
and subunits can be inserted, moved, deleted and/or replaced).

Moltemplate is extremely flexible. It supports all LAMMPS force-field
styles and nearly all atom-styles (now and in the future).

Moltemplate requires the Bourne-shell, and a recent version of python
(2.7 or 3.0 or higher), and can run on OS X, linux, or windows (if a
suitable shell environment has been installed). **A substantial amount
of memory is needed** to run moltemplate. For example, building a system
of 1000000 atoms typically requires between 3 and 12 GB of *available*
memory. (This depends on the number of bonds, molecules, and angular
interactions. See section :ref:`sec:limitations` for details.)

Converting *LT files* to LAMMPS input/data files
------------------------------------------------

The moltemplate.sh program converts LT-files (which contain molecule
definitions) into complete LAMMPS input-scripts and data-files:

::

   moltemplate.sh -atomstyle "full" system.lt

or

::

   moltemplate.sh -xyz coords.xyz -atomstyle "full" -vmd system.lt

In the first example, the coordinates of the atoms in the system are
built from commands inside the "system.lt" file. In the second example
coordinates for the atoms are read from an XYZ-file, and then invokes
VMD to visualize the system just created. (PDB-files and other
coordinate formats are also supported. Note: The "full" atom style was
used in this example, but other LAMMPS atom styles are supported,
including hybrid styles.)

Either of these commands will construct a LAMMPS data file and a LAMMPS
input script (and possibly one or more auxiliary input files), which can
be directly run in LAMMPS with minimal editing.

Converting LAMMPS input/data files to *LT files*
------------------------------------------------

Existing LAMMPS input/data files can be converted into ".LT" files using
the "ltemplify.py" utility.
( *Some additional manual editing may be required. See appendix*
:ref:`sec:ltemplify` ).

Warning: All-atom force fields
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Although moltemplate was designed for building coarse-grained molecules,
popular all-atom force-fields such as AMBER GAFF and OPLS-AA have been
converted into LT format, and more are planned. Moltemplate users can
build molecules, using these force-field rules to generate angles,
dihedrals, and impropers interactions for their molecules automatically
(and lookup their corresponding force field parameters). Unfortunately,
as of 2019-9-03, moltemplate does *not* assign partial charges, *or*
infer the atom-types which are needed to lookup this information.
*(Force-field-specific atom types are typically inferred from PDB files,
CIF files, SMILES strings, or read directly from PSF files or other
files.)* In practice, the lack of automatic atom-type determination can
be a huge limitation. Currently, users must carefully choose the name of
the atom type associated with every atom in their molecules. For small
molecules, this can sometimes be done by reading the force-field file
carefully and choosing from among the types in the list whose
description in the comments seems to match the context where the atom
appears in their molecule. However this is risky because the accuracy of
the simulation depends on selecting the correct atom type from this
list. For large, complex molecules, manually selecting atom types this
way is not feasible. In that case, users can try using an external tool
(like AmberTools or PSFGEN) to generate atom type names and partial
charges, and (manually) copy that information into the moltemplate file
(LT file). (Unfortunately, the atom type names in different versions of
the same force field do not always match, so this also must be done
carefully. For example, as of 2019-9-03, the OPLSAA atom types created
by PSFGEN *do not match* the OPLSAA atom types used by moltemplate.
Efforts are underway to make these various tools work better together.)

Fortunately, external web-servers such as ATB or LibParGen can be used
to generate files moltemplate or LAMMPS or format which internally
contain all of the force-field information (so that using an external
force field is unnecessary).

Additional tools
----------------

The VMD topotools plugin :cite:`topotools` is useful for
converting PDB files into LAMMPS format. These files can then be
converted to “LT” format using the “ltemplify.py” utility. VMD
:cite:`VMD` and topotools are also useful for visualizing
the data files created by moltemplate.sh (See section
:ref:`sec:vmd_topotools`.)

The PACKMOL :cite:`packmol` program is useful for generating
coordinates of dense heterogeneous mixtures of molecules, which can be
read by moltemplate. (The VMD “solvate” plugin may also be helpful.)

Examples
--------

This manual explains in detail how to use moltemplate.sh to build LAMMPS
files from scratch. You will also need to learn how to *run* LAMMPS and
visualize your results. Section :ref:`sec:tutorial`contains a brief
tutorial which explains how to build a box of water using moltemplate
and visualize initial conformation, run LAMMPS, and then visualize the
trajectory. Several complete working examples (with images and readme
files) which can be downloaded and modified are available online at:
http://moltemplate.org/visual_examples.html A more comprehensive list of
examples is included in the “examples/” subdirectory distributed with
moltemplate. These examples are a good starting point for learning
LAMMPS and moltemplate.

License
-------

Moltemplate is open source and publicly available at
http://moltemplate.org. With the exception of one file (*ttree_lex.py*)
moltemplate is available under the terms of the *MIT license*. The
remaining file, (*ttree_lex.py*) is a modified version of (*shlex.py*)
which is available under the *PSF license*
(https://docs.python.org/3/license.html).

.. bibliography:: /refs.bib
   :filter: docname in docnames