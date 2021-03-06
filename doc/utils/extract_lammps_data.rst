extract_lammps_data.py
======================

Description
-----------

**extract_lammps_data.py** is a simple script which extracts sections of
text from a LAMMPS data file. (Of course, you can accomplish the same
thing with a text editor, but this script turns it into a one-line
command.) Although it was written in python, **extract_lammps_data.py**
is a stand-alone executable intended to be run from the terminal
(shell).

Typical usage
-------------

::

   extract_lammps_data.py SECTION_NAME < FILE.DATA > EXCERPT.TXT

This extracts a section from a LAMMPS data file and saves it in
EXCERPT.TXT.

More general usage
------------------

::

   extract_lammps_data.py [-n] SECTION_LIST < FILE.DATA > EXCERPT.TXT

The SECTION_LIST is a list of sections you want in your output file (eg
“EXCERPT.TXT”). Each section must be a quoted-string, and spaces (not
commas) should separate each section name.

The optional “-n” flag negates the selection. (Selecting instead,
everything except the sections you listed.)

Examples
--------

::

   extract_lammps_data.py "Atoms" < FILE.DATA > Atoms.txt
   extract_lammps_data.py "Header" < FILE.DATA > Header.txt
   extract_lammps_data.py -n "Header" < FILE.DATA > everything_except_Header.txt

   extract_lammps_data.py "Bonds" "Angles" "Dihedrals" "Impropers" \
                          < FILE.DATA > topology.txt

   extract_lammps_data.py "Pair Coeffs" "Bond Coeffs" "Angle Coeffs" \
                          "Dihedral Coeffs" "Improper Coeffs" \
                          < FILE.DATA > force_field.txt

The following section names are allowed:

::

   "Header"                   # The header section at the beginning of the file.
   "Atoms"
   "Masses"
   "Bonds"
   "Bond Coeffs"
   "Angles"
   "Angle Coeffs"
   "Dihedrals"
   "Dihedral Coeffs"
   "Impropers"
   "Improper Coeffs"
   "BondBond Coeffs"          # class2 angles
   "BondAngle Coeffs"         # class2 angles
   "MiddleBondTorsion Coeffs" # class2 dihedrals
   "EndBondTorsion Coeffs"    # class2 dihedrals
   "AngleTorsion Coeffs"      # class2 dihedrals
   "AngleAngleTorsion Coeffs" # class2 dihedrals
   "BondBond13 Coeffs"        # class2 dihedrals
   "AngleAngle Coeffs"        # class2 impropers
   "Angles By Type"           # moltemplate-specific. (not standard LAMMPS)
   "Dihedrals By Type"        # moltemplate-specific. (not standard LAMMPS)
   "Angles By Type'           # moltemplate-specific. (not standard LAMMPS)
