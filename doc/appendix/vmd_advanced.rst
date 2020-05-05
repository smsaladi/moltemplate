.. _sec:vmd_advanced:

Visualization in VMD
====================

This appendix is only intended to give you a quick, minimal list of
features you need to know to display your molecules using VMD. These
instructions were written for VMD 1.9 and topotools 1.2. For advanced
VMD features, analysis, and rendering options, consult the official VMD
documentation at http://www.ks.uiuc.edu/Research/vmd/current/docs.html

.. _sec:vmd_representation:

Customizing the appearance in VMD
---------------------------------

By default, VMD is likely to display your molecules with points and
lines, which can be ugly and difficult to see. To alter the appearance
of your molecules, select the
**Graphics**\ :math:`\rightarrow`\ **Representations...** menu, and then
select an option from the pull-down menu. Atoms are colored by atom-type
by default. You can customize the color of each atom type by
:math:`\rightarrow` As of 2019-9-03, VMD arbitrarily allows you to
assign colors to *only* the first 9 atom types. However you can get
around this limitation using multiple *representations* customize the
appearance of the remaining atom types (as explained below).

You may wish to use different representations for different molecules or
atom types. To do this, select the
**Graphics**\ :math:`\rightarrow`\ **Representations...** menu and click
on then tab. Then click on the button to create multiple
“*representations*” of your system. For each *representation*, you can
select different sets atoms, and use different draw-styles, for those
atoms. For example, you can customize the color of these atoms manually
by choosing **ColorID** from the pull-down menu. Then, to the right of
this menu, you can select the color (which is represented by a number).
This will effect all of atoms in the current *representation*. You can
also select a different and alter the atom and bond radii.

You can select from the the list of *representations* you have already
created by clicking on the list under the button. (Double-clicking
temporarily hides a *representation* from view.)

Again, each *representation* is usually assigned to a different subset
of atoms from the system. To specify the atoms in each *representation*,
click on the tab. By default “all” atoms are selected, however you can
select atoms according to atom **type**, **index**, **molid**,
**charge**, **mass**, **x**, **y**, **z**. This will limit the current
display settings to a subset of the atoms/bonds present in your system.
When selecting atoms, you can use complex boolean expressions
(containing one or more *and* and *or* operators and parenthesis). For
more information and some examples, see
http://www.ks.uiuc.edu/Research/vmd/vmd-1.9/ug/node19.html and
http://www.ks.uiuc.edu/Research/vmd/vmd-1.9/ug/node87.html#ug:topic:selections.

**Note:** In VMD/topotools, the **type**, **index**, and **molid**
properties of each atom correspond to the *@atom*, *$atom*, and *$mol*
variables for each atom in moltemplate. Unfortunately, VMD does not
understand moltemplate variable naming syntax (discussed in section
:ref:`sec:variables`). Instead, in VMD, variables must be specified
by their numeric equivalents. You can determine these numbers by reading
the *output_ttree/ttree_assignments.txt* file. (See section
`[sec:ttree_assignments] <#sec:ttree_assignments>`__ for details.) That
file contains a table containing a list of the numbers assigned to each
*@atom* (type), *$atom* (id), and *$mol* (molecule-id) variable.

.. _sec:vmd_pbc:

Visualizing periodic boundaries
-------------------------------

To view the periodic box boundaries, select the
**Extensions**\ :math:`\rightarrow` menu, and in the *Tk Console*
window, enter:

::

   pbc box

Note that the molecules in your system might not lie inside this box.
You can *wrap* them inside the box using this command:

::

   pbc wrap -compound res -all

You may wish to center the box around a molecule. There are several ways
to do this. You can move the box manually this way:

::

   pbc wrap -compound res -all -shiftcenterrel {0.0 0.15 0.0}
   pbc box -shiftcenterrel {0.0 0.15 0.0}

This will shift the position of the box by 15% in the Y direction.
(Distances are measured in units of box-length fractions, not
Angstroms.)

*(Advanced usage: if you have a solute whose atoms are all of type “1”,
surrounded by a solvent of atoms of type “2” then you can also try this
to center the box around it using: “pbc wrap -sel type=1 -all -centersel
type=2 -center com”. The “1” and “2” are the @atom type numbers assigned
by moltemplate. This can be found in the
output_ttree/ttree_assignments.txt file. If you are viewing a
trajectory, then this will modify the appearance of every step in the
trajectory, centering the box around the solute atoms.)*

For more details visualizing periodic-boundaries, visit:
http://www.ks.uiuc.edu/Research/vmd/plugins/pbctools

To prevent atom overlap, you should also check if your periodic boundary
conditions are too small. To do that:

a) select menu option

b) click on the "Periodic" tab, and

c) click on the **+x**, **-x**, **+y**, **-y**, **+z**, **-z**, and
**self** checkboxes.

When doing so, inspect the system to make sure the atoms which appear
occupy non-overlapping volumes in space.
