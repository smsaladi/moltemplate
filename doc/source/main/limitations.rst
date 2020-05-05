Known bugs and limitations
==========================

Please report any bugs you find by email to |image|, or to the
lammps-users mailing list.

**1)** LAMMPS-style molecule-templates are *not* supported. The DATA
files created by moltemplate are not in the correct format to be read by
the LAMMPS *molecule* command. (This is because this command was added
after moltemplate was written.) However the formats are similar, and the
relevant information can be extracted using a text-editor and converted
to the other format. (Using a text-editor and awk, or a spreadsheet
program. For more information on these file formats,
http://lammps.sandia.gov/doc/read_data.html
http://lammps.sandia.gov/doc/molecule.html.) Again, feel free to contact
|image| to request support for LAMMPS-style molecule templates.

**2)** **Moltemplate consumes a large amount of memory (RAM)**

Memory use grows proportional to system size. As of 2019-9-03, setting
up a system of 1000000 atoms using moltemplate currently requires
between 2.7 and 12 GB of *available* memory. (Systems with many bonds
and angles consume more memory, as well as systems with a high molecule
count.) Unfortunately this code was not carefully written to minimize
memory usage. (In addition, python programs can require more than 10
times as much memory as similar programs written in C/C++.)

This problem might be alleviated by using other python interpreters with
a lower memory footprint. Alternately, it may be necessary to split a
large system into pieces, run moltemplate on each piece, and combine the
resulting data files into one large data file later.

Also, computers with a moderate amount of RAM can be rented very
cheaply. (For example, see https://cloud.google.com/compute/.)

*When setting up large simulations with moltemplate, consider using the
command* to prevent system crashes. (If you are on a shared computer,
ask an administrator to do this.) If these options are not available,
you can always run a resource monitor (like “top”) before starting
moltemplate and kill the process if it’s memory usage exceeds 80%.

**3)** Limited support for non-point-like atoms:

As of 2019-9-03, only the “full”, “angle”, “atomic”, “charge”, “sphere”,
“dipole”, “ellipsoid”, and “molecular” styles have been tested. Other
non-point-like atoms like “tri”, “line” *should* also work with
moltemplate. However these objects are *not rotated correctly* by the
“.rot()” command (or scaled correctly by the “.scale()” command). More
exotic exotic atom styles, such as “wavepacket”, “electron”, “sphere”
and “peri” have not been tested. In addition, atom_style **body** and
atom_style **template** are *not* supported. Feel free to contact
|image| to request support for exotic atom styles.

**4)** When placed at the end of a line, LAMMPS interprets **the “&”
character** as a request to merge two lines together. *It is usually
safe to use this character inside moltemplate write() or write_once()
commands.* However in some rare cases, joining two lines together using
the “&” character can confuse moltemplate. For example, in a lammps
input script command, (like “pair_coeff” or “dihedral_coeff”), **the “&”
character should not appear before the last “@” or “$” variable is
referenced.** Also avoid using the “&” character anywhere in the “Data
Atoms”, “Data Bonds”, “Data Angles”, “Data Dihedrals”, “Data Impropers”,
“Data Angles By Type”, “Data Dihedrals By Type”, and “Data Impropers By
Type” sections.

**5)** Triclinic boundary conditions have not been tested:

As of 2019-9-03, support for PDB files with triclinic cells is
experimental. Please let me know if it is not working.

**6)** Inconsistent support for wildcard characters (“\*” and “?”)
[sec:wildcard_bug] The wildcard character “\*” is interpreted
differently in different parts of an LT file. Wildcard characters work
reliably and are used for *string* pattern matching when inside
“bond_coeff”, “angle_coeff”, “dihedral_coeff”, “improper_coeff”, and
most “pair_coeff” commands, as well as any of the *“By Type”* sections
in an LT file (such as *“Data Angles By Type”*, *“Data Dihedrals By
Type”*, and *“Data Impropers By Type”*). However these wildcard
characters *do not* within pair_coeff commands that require *more* than
2 atom types as arguments. (such as “pair_style hbond/dreiding/lj”.
However manybody pair_styles which use “pair_coeff \* \*” notation work
fine.)
