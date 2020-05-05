.. _sec:quick_reference:

Quick reference *(skip on first reading)*
=========================================

*Note: New users should skip to section* :ref:`sec:tutorial`

Moltemplate commands
--------------------

+-----------------------------------+-----------------------------------+
| **command**                       | **meaning**                       |
+===================================+===================================+
| *MolType* **{**                   |                                   |
| *content* ...                     |                                   |
| **}**                             |                                   |
|                                   | Define a new type of molecule (or |
|                                   | namespace) named *MolType*. The   |
|                                   | text enclosed in curly brackets   |
|                                   | (*content*) typically contains    |
|                                   | multiple write(), write_once()    |
|                                   | commands to define Atoms, Bonds,  |
|                                   | Angles, Coeffs, etc... *(If that  |
|                                   | molecule type exists already,     |
|                                   | then this will append             |
|                                   | additional*\ **content**\ *to its |
|                                   | definition.)* **new** and         |
|                                   | **delete** commands can be used   |
|                                   | to create or delete molecular     |
|                                   | subunits *within* this molecule.  |
|                                   | (See the *SPCE*, *Monomer*, and   |
|                                   | *Butane* molecules, and the       |
|                                   | *TraPPE* namespace defined in     |
|                                   | sections                          |
|                                   | :ref:`sec:spce_example`,          |
|                                   | :ref:`sec:2bead`,                 |
|                                   | :ref:`sec:inheritance`, &         |
|                                   | :ref:`sec:trappe`.                |
+-----------------------------------+-----------------------------------+
| *mol_name* = **new** *MolType*    | Create (instantiate) a copy of a  |
|                                   | molecule of type *MolType* and    |
|                                   | name it *mol_name*. (See section  |
|                                   | :ref:`sec:spce_example`.)         |
+-----------------------------------+-----------------------------------+
| *mol_name* = **new**              | Create a copy of a molecule and   |
| *MolType*.\ *xform()*             | apply coordinate transformation   |
|                                   | *xform()* to its coordinates.     |
|                                   | (See sections                     |
|                                   | :ref:`sec:coords_intro` and       |
|                                   | :ref:`sec:xforms_table`.)         |
+-----------------------------------+-----------------------------------+
| *molecules* = **new** *MolType*   | Create *N* copies of a molecule   |
| [*N*].\ *xform()*                 | of type *MolType* and name them   |
|                                   | *molecules[0]*, *molecules[1]*,   |
|                                   | *molecules[2]*... Coordinates in  |
|                                   | each successive copy are          |
|                                   | cumulatively transformed          |
|                                   | according to *xform()*. (See      |
|                                   | sections                          |
|                                   | :ref:`sec:coords_intro`,          |
|                                   | :ref:`sec:xform_arrays` and       |
|                                   | :ref:`sec:xforms_table`.)         |
|                                   | Multidimensional arrays are also  |
|                                   | allowed. (See section             |
|                                   | :ref:`sec:multidimensional_arrays`|
|                                   | .)                                |
+-----------------------------------+-----------------------------------+
| *molecules* =                     |                                   |
| **new** *MolType*.\ xform1()      |                                   |
| **[N]**.\ *xform2()*              |                                   |
|                                   | Apply coordinate transformations  |
|                                   | (*xform1()* to *MolType*,         | 
|                                   | before making *N* copies          |
|                                   | of it while cumulatively applying |
|                                   | *xform2()*. (See section          |
|                                   | :ref:`sec:xform_arrays`           |
|                                   | and :ref:`sec:xform_order`.)      |
+-----------------------------------+-----------------------------------+
| *molecules* = **new**             |                                   |
| **random**\ ([*M1.xf1()*,         |                                   |
|                                   |                                   |
| *M2.xf2()*,                       |                                   |
|                                   |                                   |
| *M3.xf2()*,...],                  |                                   |
|                                   |                                   |
| [:math:`p_1, p_2, p_3, ...`,      |                                   |
|                                   |                                   |
| *seed*)                           |                                   |
|                                   |                                   |
| **[N]**.\ *xform()*               |                                   |
|                                   | Generate an array of *N*          |
|                                   | molecules randomly selected from  |
|                                   | with probabilities , using        |
|                                   | (optional) initial coordinate     |
|                                   | transformations *xf1(), xf2(),    |
|                                   | xf3, ...*, and applying           |
|                                   | transformation *xform()*          |
|                                   | cumulatively thereafter. This     |
|                                   | also works with multidimensional  |
|                                   | arrays. **You can directly        |
|                                   | specify the number of each type   |
|                                   | of molecule** by replacing the    |
|                                   | list of probabilities , with a    |
|                                   | list of integers . (See sections  |
|                                   | :ref:`sec:random_arrays` and      |
|                                   | :ref:`sec:random_advanced`.)      |
+-----------------------------------+-----------------------------------+
| *NewMol* = *OldMol*               | Create a new molecule **type**    |
|                                   | based on an existing molecule     |
|                                   | type. Additional atoms (or bonds, |
|                                   | etc...) can be added later to the |
|                                   | new molecule using . (See section |
|                                   | :ref:`sec:molecule_customization` |
|                                   | .)                                |
+-----------------------------------+-----------------------------------+
| *NewMol* = *OldMol*.\ *xform()*   | Create a new molecule **type**    |
|                                   | based on an existing molecule     |
|                                   | type, and apply coordinate        |
|                                   | transformation *xform()* to it.   |
|                                   | (See section                      |
|                                   | :ref:`sec:molecule_customization` |
|                                   | .)                                |
+-----------------------------------+-----------------------------------+
| *NewMol* **inherits** *Mol1*      |                                   |
| *Mol2* ... {                      |                                   |
|                                   |                                   |
|  *additional content* ...         |                                   |
|                                   |                                   |
| }                                 |                                   |
|                                   | Create a new molecule **type**    |
|                                   | based on multiple existing        |
|                                   | molecule types. Atom types, bond  |
|                                   | types, angle types (etc) which    |
|                                   | are defined in *Mol1*, or *Mol2*, |
|                                   | ... are available inside the new  |
|                                   | molecule. *Additional content*    |
|                                   | (including more *write()* or      |
|                                   | *write_once()* or *new* commands) |
|                                   | follows within the curly          |
|                                   | brackets. (See sections           |
|                                   | :ref:`sec:inheritance_intro`,     |
|                                   | :ref:`sec:inheritance`, and       |
|                                   | :ref:`sec:multiple_inheritance`)  |
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| *MolType*.\ *xform()*             | Apply the coordinate transform    |
|                                   | *xform()* to the coordinates of   |
|                                   | the atoms in all molecules of     |
|                                   | type *MolType*. (See section      |
|                                   | :ref:`sec:molecule_customization` |
|                                   | .)                                |
+-----------------------------------+-----------------------------------+
| *molecule*.\ *xform()*            | Apply the coordinate transform    |
|                                   | *xform()* to the coordinates in   |
|                                   | *molecule*. (Here *molecule*      |
|                                   | refers to a specific instance or  |
|                                   | copy of a particular molecule     |
|                                   | type. See sections                |
|                                   | :ref:`sec:custom_xform` and       |
|                                   | :ref:`sec:coords_intro`.)         |
+-----------------------------------+-----------------------------------+
| *molecules* [*range*].\ *xform()* | Apply the coordinate transform    |
|                                   | *xform()* to the coordinates of   |
|                                   | molecules specified by . (This    |
|                                   | also works for multidimensional   |
|                                   | arrays. See sections              |
|                                   | :ref:`sec:array_wildcards_intro`  |
|                                   | and :ref:`sec:custom_xform`.)     |
+-----------------------------------+-----------------------------------+
| **delete** *molecule*             | Delete the *molecule* instance.   |
|                                   | (This command can appear inside a |
|                                   | molecule’s definition to delete a |
|                                   | specific molecular subunit within |
|                                   | a molecule. In that case, it will |
|                                   | be carried out in every copy of   |
|                                   | that molecule type. **delete**    |
|                                   | can also be used to delete        |
|                                   | specific atoms, bonds, angles,    |
|                                   | dihedrals, and improper           |
|                                   | interactions.) See section        |
|                                   | :ref:`sec:delete`.                |
+-----------------------------------+-----------------------------------+
| **delete** *molecules*\ [*range*] | Delete a range of molecules       |
|                                   | specified by . (This also works   |
|                                   | for multidimensional arrays. See  |
|                                   | sections :ref:`sec:delete`        |
|                                   | and :ref:`sec:delete_holes`.)     |
+-----------------------------------+-----------------------------------+
| **write_once** ('*file*') {       |                                   |
|                                   |                                   |
| *text* ...                        |                                   |
|                                   |                                   |
| }                                 |                                   |
|                                   | Write the text enclosed in curly  |
|                                   | brackets to file . The *text* can |
|                                   | contain @variables which are      |
|                                   | replaced by integers. (See        |
|                                   | sections :ref:`sec:write` and     |
|                                   | :ref:`sec:variables`.)            |
+-----------------------------------+-----------------------------------+
| **write** ('*file*') {            |                                   |
|                                   |                                   |
| *text* ...                        |                                   |
|                                   |                                   |
| }                                 |                                   |
|                                   | Write the text enclosed in curly  |
|                                   | brackets to file *file*. *This is |
|                                   | done every time a new copy of     |
|                                   | this molecule is created using    |
|                                   | the “new” command.* The *text*    |
|                                   | can contain either @variables or  |
|                                   | $variables which will be replaced |
|                                   | by integers. (See sections        |
|                                   | :ref:`sec:write` and              |
|                                   | :ref:`sec:variables`.)            |
+-----------------------------------+-----------------------------------+
|    Note:  *file* names beginning with 'Data' or 'In'                  |
|    (such as 'Data Atoms' or 'In Settings') are inserted               |
|    into the relevant section of the LAMMPS data file or input script. |
|    (See section :ref:`sec:DataIn`.)                                   |
+-----------------------------------+-----------------------------------+
| **include** *file*                | Insert the contents of file       |
|                                   | *file* here. (Quotes optional.)   |
+-----------------------------------+-----------------------------------+
| **import** *file*                 | Insert the contents of file       |
|                                   | *file* here, preventing circular  |
|                                   | inclusions. *(recommended)*       |
+-----------------------------------+-----------------------------------+
| **using namespace** *X*           | This enables you to refer to any  |
|                                   | of the molecule types, defined    |
|                                   | within a **namespace** object     |
|                                   | (*X* in this example), *without*  |
|                                   | needing to refer to these objects |
|                                   | by their full path. (This does    |
|                                   | not work for atom types. See      |
|                                   | section                           |
|                                   | :ref:`sec:using_namespaces`.)     |
+-----------------------------------+-----------------------------------+
| **category**                      |                                   |
| *$catname*\ (:math:`i_0`, \Delta`)|                                   |
|                                   |                                   |
| *or*                              |                                   |
|                                   |                                   |
| **category**                      |                                   |
| *@catname*\ (:math:`i_0`, \Delta`)|                                   |
|                                   | Create a new variable category.   |
|                                   | See section                       |
|                                   | :ref:`sec:custom_categories`      |
|                                   | for details.                      |
+-----------------------------------+-----------------------------------+
| **create_var** {                  |                                   |
| *@variable* }                     |                                   |
|                                   |                                   |
| *or*                              |                                   |
|                                   |                                   |
| **create_static_var** {           |                                   |
| *@variable* }                     |                                   |
|                                   | Create a variable specific to     |
|                                   | this molecule object.             |
|                                   | (**create_var** is typically used |
|                                   | to create molecule-ID numbers     |
|                                   | that are shared between subunits  |
|                                   | of a larger molecule. See section |
|                                   | :ref:`sec:twobead_polymer`.       |
|                                   | **create_static_var** is          |
|                                   | typically used to define shared   |
|                                   | @atom types. )                    |
+-----------------------------------+-----------------------------------+
| **replace** { *oldvariable*       | Allow alternate names for the     |
| *newvariable* }                   | same variable. This replaces all  |
|                                   | instances of *oldvariable* with   |
|                                   | *newvariable*. Both variable      |
|                                   | names must have a “@” prefix.     |
|                                   | This is typically used to reduce  |
|                                   | the length of long variables, for |
|                                   | example to allow the shorthand    |
|                                   | “@atom:C2” to refer to            |
|                                   | “@atom:C2_bC2_aC_dC_iC”           |
+-----------------------------------+-----------------------------------+
| **#**\ *commented text*           | All text following a “#”          |
|                                   | character is treated as a comment |
|                                   | and ignored.                      |
+-----------------------------------+-----------------------------------+

.. raw:: latex

   \pagebreak

Common $ and @ variables
------------------------

| (See section :ref:`sec:variables` for details.)

+-----------------------------------+-----------------------------------+
| **variable type**                 | **meaning**                       |
+===================================+===================================+
| $atom:\ *name*                    | A unique ID number assigned to    |
|                                   | atom *name* in this molecule.     |
|                                   | (Note: The *:name* suffix can be  |
|                                   | omitted if the molecule in which  |
|                                   | this variable appears only        |
|                                   | contains a single atom.)          |
+-----------------------------------+-----------------------------------+
| @atom:\ *type*                    | A number which indicates an       |
|                                   | atom’s *type* (typically used to  |
|                                   | lookup pair interactions.)        |
+-----------------------------------+-----------------------------------+
| $bond:\ *name*                    | A unique ID number assigned to    |
|                                   | bond *name* (Note: The *:name*    |
|                                   | suffix can be omitted if the      |
|                                   | molecule in which this variable   |
|                                   | appears only contains a single    |
|                                   | bond.)                            |
+-----------------------------------+-----------------------------------+
| @bond:\ *type*                    | A number which indicates a bond’s |
|                                   | *type*                            |
+-----------------------------------+-----------------------------------+
| $angle:\ *name*                   | A unique ID number assigned to    |
|                                   | angle *name* (Note: The *:name*   |
|                                   | suffix can be omitted if the      |
|                                   | molecule in which this variable   |
|                                   | appears only contains a single    |
|                                   | angle interaction.)               |
+-----------------------------------+-----------------------------------+
| @angle:\ *type*                   | A number which indicates an       |
|                                   | angle’s *type*                    |
+-----------------------------------+-----------------------------------+
| $dihedral:\ *name*                | A unique ID number assigned to    |
|                                   | dihedral *name* (Note: The        |
|                                   | *:name* suffix can be omitted if  |
|                                   | the molecule in which this        |
|                                   | variable appears only contains a  |
|                                   | single dihedral-angle             |
|                                   | interaction.)                     |
+-----------------------------------+-----------------------------------+
| @dihedral:\ *type*                | A number which indicates a        |
|                                   | dihedral’s *type*                 |
+-----------------------------------+-----------------------------------+
| $improper:\ *name*                | A unique ID number assigned to    |
|                                   | improper *name* (Note: The        |
|                                   | *:name* suffix can be omitted if  |
|                                   | the molecule in which this        |
|                                   | variable appears only contains a  |
|                                   | single improper interaction.)     |
+-----------------------------------+-----------------------------------+
| @improper:\ *type*                | A number which indicates an       |
|                                   | improper’s *type*                 |
+-----------------------------------+-----------------------------------+
| $\ *mol* or $\ *mol:.*            | This variable refers to the ID    |
|                                   | number of *this* molecule object. |
|                                   | (See section                      |
|                                   | :ref:`sec:spce_example`.          |
|                                   | Note: ``$mol`` is shorthand       |
|                                   | for ``$mol:.``)                   |
+-----------------------------------+-----------------------------------+
| $\ *mol:*...                      | The ID number assigned to the     |
|                                   | molecule to which this object     |
|                                   | belongs (if applicable). See      |
|                                   | sections                          |
|                                   | :ref:`sec:twobead_polymer`,       |
|                                   | :ref:`sec:ellipsis_mol`,          |
|                                   | and appendix                      |
|                                   | :ref:`sec:adv_variable_syntax`.   |
+-----------------------------------+-----------------------------------+

The numbers assigned to each variable are saved in the
**output\_ttree/ttree\_assignments.txt**. See section :ref:`sec:output_ttree`.

Advanced variable usage
-----------------------

+-----------------------------------+-----------------------------------+
| **variable type**                 | **meaning**                       |
+===================================+===================================+
| *$category*:**query**\ ()         | Query the current value of the    |
|                                   | counter in this *$category*       |
|                                   | without incrementing it. (The     |
|                                   | “*$category*” is usually either   |
|                                   | *$atom*, *$bond*, *$angle*,       |
|                                   | *$dihedral*, *$improper*, or      |
|                                   | *$mol*.) This is useful for       |
|                                   | counting the number of atoms,     |
|                                   | bonds, angles, molecules, etc...  |
|                                   | created so far.                   |
+-----------------------------------+-----------------------------------+
| *@category*:**query**\ ()         | Query the current value of the    |
|                                   | counter in this *@category*       |
|                                   | without incrementing it. (The     |
|                                   | “*@category*” is usually either   |
|                                   | *@atom*, *@bond*, *@angle*,       |
|                                   | *@dihedral*, or *@improper*.)     |
|                                   | This is useful for counting the   |
|                                   | number of atom types, bond types, |
|                                   | angle types, etc... declared so   |
|                                   | far.)                             |
+-----------------------------------+-----------------------------------+
| ${*category:variable*} or         | Curly-brackets, **{}**, are used  |
| @{*category:variable*}            | to refer to variables with        |
|                                   | non-standard delimiters or        |
|                                   | whitespace characters. (See       |
|                                   | section                           |
|                                   | :ref:`sec:vardetails`.)           |
+-----------------------------------+-----------------------------------+
| @{category:\ *type*.rjust(n)} or  | Print the counter variable in a   |
|  @{category:\ *type*.ljust(n)} or | right-justified or a              |
| ${category:\ *name*.rjust(n)} or  | left-justified text-field of      |
|  ${category:\ *name*.ljust(n)}    | fixed width :math:`n` characters. |
|                                   | (This is useful for generating    |
|                                   | text files which require          |
|                                   | fixed-width columns.)             |
+-----------------------------------+-----------------------------------+

.. raw:: latex

   \pagebreak

.. _sec:xforms_table:

Coordinate transformations
--------------------------

See sections :ref:`sec:coords_intro` and :ref:`sec:xform_arrays` for details.

+-------------------------------------------------------------------------------+-------------------------------------------------------------------------------------+
| **suffix**                                                                    | **meaning**                                                                         |
+===============================================================================+=====================================================================================+
| :math:`\textit{.move}(x,y,z)`                                                 | Add numbers to the coordinates of every atom                                        |
+-------------------------------------------------------------------------------+-------------------------------------------------------------------------------------+
| :math:`\textit{.rot}(\theta,x,y,z)`                                           | Rotate atom coordinates by angle                                                    |
|                                                                               | :math:`\theta` around axis passing through the origin.                              |
|                                                                               | (Dipole directions are also rotated.)                                               |
+-------------------------------------------------------------------------------+-------------------------------------------------------------------------------------+
| :math:`\textit{.rot}(\theta,x,y,z,x_0,y_0,z_0)`                               | Rotate atom coordinates by angle                                                    |
|                                                                               | :math:`\theta` around axis pointing in the direction ,                              |
|                                                                               | passing through the point.                                                          |
|                                                                               | (This point will be a *fixed point*.)                                               |
+-------------------------------------------------------------------------------+-------------------------------------------------------------------------------------+
| :math:`\textit{.rotvv}(v_{1x},v_{1y},v_{1z},v_{2x},v_{2y},v_{2z})`            | Rotate atom coordinates with an angle which rotates the vector                      |
|                                                                               | :math:`\mathbf{v}_1` to :math:`\mathbf{v}_2` (around an axis                        |
|                                                                               | perpendicular to both :math:`\mathbf{v}_1` and :math:`\mathbf{v}_2`).               |
|                                                                               | If you supply 3 additional numbers :math:`x_0,y_0,z_0`, the axis of                 |
|                                                                               | rotation will pass through this location.                                           |
+-------------------------------------------------------------------------------+-------------------------------------------------------------------------------------+
| :math:`\textit{.scale}(\text{ratio})`                                         | Multiply all atomic coordinates by *ratio*.                                         |
|                                                                               | *(*\ **Important:**\ *The scale() command does not update force-field               |
|                                                                               | parameters such as atomic radii or bond-lengths. Dipole magnitudes are              |
|                                                                               | affected.)*                                                                         |
+-------------------------------------------------------------------------------+-------------------------------------------------------------------------------------+
| :math:`\textit{.scale}(x_r,y_r,z_r)`                                          | Multiply coordinates :math:`x,y,z`) by :math:`x_r,y_r,z_r`, respectively,           |
+-------------------------------------------------------------------------------+-------------------------------------------------------------------------------------+
| :math:`\textit{.scale}(\text{ratio},x_0,y_0,z_0)`  or                         | You can supply 3 optional additional arguments which specify the                    |
| :math:`\textit{.scale}(x_r,y_r,z_r,x_0,y_0,z_0)`                              | point around which you want the scaling to occur. (This point will be               |
|                                                                               | a *fixed point*. Of omitted, the origin is used.)                                   |
+-------------------------------------------------------------------------------+-------------------------------------------------------------------------------------+
| :math:`\textit{.quat}(a,b,c,d)`  or                                           | Rotate atom coordinates by the rotation corresponding to quaternion                 |
| :math:`\textit{.quat}(a,b,c,d,x_0,y_0,z_0)`                                   | :math:`a+b\mathbf{i}+c\mathbf{j}+b\mathbf{k}` (around , if specified).              |
+-------------------------------------------------------------------------------+-------------------------------------------------------------------------------------+
| :math:`\textit{.matrix}(                                                      |                                                                                     |
| M_{1,1}, M_{1,2}, M_{1,3},                                                    | Apply a general linear coordinate transformation.                                   |
| M_{2,1}, M_{2,2}, M_{2,3},                                                    |                                                                                     |
| M_{3,1}, M_{3,2}, M_{3,3})`                                                   |   .. math::                                                                         |
|                                                                               |                                                                                     |
|                                                                               |      \begin{aligned}                                                                |
|                                                                               |      \left(\begin{array}{c}                                                         |
|                                                                               |             x' \\                                                                   |
|                                                                               |             y' \\                                                                   |
|                                                                               |             z'                                                                      |
|                                                                               |      \end{array}\right)                                                             |
|                                                                               |      =                                                                              |
|                                                                               |      \left(\begin{array}{ccc}                                                       |
|                                                                               |             M_{1,1} & M_{1,2} & M_{1,3} \\                                          |
|                                                                               |             M_{2,1} & M_{2,2} & M_{2,3} \\                                          |
|                                                                               |             M_{3,1} & M_{3,2} & M_{3,3}                                             |
|                                                                               |      \end{array}\right)                                                             |
|                                                                               |      \left(\begin{array}{c}                                                         |
|                                                                               |             x \\                                                                    |
|                                                                               |             y \\                                                                    |
|                                                                               |             z                                                                       |
|                                                                               |      \end{array}\right)\end{aligned}                                                |
+-------------------------------------------------------------------------------+-------------------------------------------------------------------------------------+
| *push*\ (rot(152.3,0.79,0.43,-0.52))                                          | Coordinate transformations introduced using the *push()* command are                |
|                                                                               | applied to molecules instantiated later (using the *new*) command, and              |
| monomer1 = new Monomer                                                        | remain in effect until they are removed using the *pop()* command.                  |
|                                                                               | (And transformations appearing in arrays accumulate as well, but do                 |
| *push*\ (move(0.01,35.3,-10.1))                                               | not need to be removed with *pop()*.) In this example, the first                    |
|                                                                               | transformation, “rot()”, is applied to both “monomer1” and “monomer2”.              |
| monomer2 = new Monomer                                                        | The last transformation, “move()”, is applied after “rot()” and only                |
|                                                                               | acts on “monomer2”.                                                                 |
| *pop*\ ()                                                                     |                                                                                     |
|                                                                               |                                                                                     |
| *pop*\ ()                                                                     |                                                                                     |
|                                                                               |                                                                                     |
|                                                                               |                                                                                     |
+-------------------------------------------------------------------------------+-------------------------------------------------------------------------------------+


.. raw:: latex

   \pagebreak

.. _sec:args_table:

moltemplate.sh command line arguments:
--------------------------------------

+-----------------------------------+-----------------------------------+
| **argument**                      | **meaning**                       |
+===================================+===================================+
| -atomstyle *style*                | Inform moltemplate which          |
|                                   | atom_style you are using.         |
|                                   | (*style* is "full" by default).   |
|                                   | Other styles like "molecular" or  |
|                                   | "hybrid full dipole" are          |
|                                   | supported. For custom atom        |
|                                   | styles, you can also specify the  |
|                                   | list of column names manually.    |
|                                   | For example: **-atomstyle "molid  |
|                                   | x y z atomid atomtype mux muy     |
|                                   | muz"** Atom styles should be      |
|                                   | enclosed in quotes (").           |
+-----------------------------------+-----------------------------------+
| -raw coords.raw                   | Read all of the atomic            |
|                                   | coordinates from an external RAW  |
|                                   | file. (RAW files are simple       |
|                                   | 3-column ASCII files contain X Y  |
|                                   | Z coordinates for every atom,     |
|                                   | separated by spaces.)             |
+-----------------------------------+-----------------------------------+
| -xyz coords.xyz                   | Read all of the atomic            |
|                                   | coordinates from an external XYZ  |
|                                   | file (XYZ files are 4-column      |
|                                   | ascii files in ATOMTYPE X Y Z     |
|                                   | format. The first column,         |
|                                   | ATOMTYPE, is skipped. The first   |
|                                   | line should contain the number of |
|                                   | atoms. The second line is         |
|                                   | skipped. See section              |
|                                   | :ref:`sec:coords_intro`.)         |
+-----------------------------------+-----------------------------------+
| -pdb coords.pdb                   | Read all of the atomic            |
|                                   | coordinates from an external PDB  |
|                                   | file (Periodic boundary           |
|                                   | conditions are also read, if      |
|                                   | present. Atoms are sorted by the  |
|                                   | chainID, resID, insertCode, and   |
|                                   | atomID fields on every line       |
|                                   | beginning with “ATOM” or          |
|                                   | “HETATM”. This order must match   |
|                                   | the order that the atoms appear   |
|                                   | in the data file. See section     |
|                                   | :ref:`sec:coords_intro`.)         |
+-----------------------------------+-----------------------------------+
| -a '\ *variable* *value*\'        | Assign *variable* to *value*.     |
|                                   | (The *variable* should begin with |
|                                   | either a @ character or a $       |
|                                   | character. Single-quotes and a    |
|                                   | space separator are required. See |
|                                   | appendix                          |
|                                   | :ref:`sec:manual_assignment`.)    |
+-----------------------------------+-----------------------------------+
| -a 'bindings_file'                | The variables in column 1 of      |
|                                   | *bindings_file* (which is a text  |
|                                   | file) will be assigned to the     |
|                                   | values in column 2 of that file.  |
|                                   | (This is useful when there are    |
|                                   | many variable assignments to      |
|                                   | make. See appendix                |
|                                   | :ref:`sec:manual_assignment`.)    |
+-----------------------------------+-----------------------------------+
| -b '\ *variable* *value*\'        | Assign variables to values.       |
|                                   | Unlike assignments made with      |
| *or*                              | “-a”, assignments made using “-b” |
|                                   | are non-exclusive. (They may      |
| -b *bindings_file*                | overlap with other variables in   |
|                                   | the same category. See appendix   |
|                                   | :ref:`sec:manual_assignment`.)    |
+-----------------------------------+-----------------------------------+
| -overlay-angles                   | By default moltemplate overwrites |
|                                   | duplicate bonded interactions     |
| -overlay-dihedrals                | which involve the same set of     |
|                                   | atoms. These flags disable that   |
| -overlay-impropers                | behavior. This can be useful when |
|                                   | you want to superimpose multiple  |
|                                   | angular or dihedral forces on the |
|                                   | same set of atoms (eg. to enable  |
|                                   | more complex force fields). Note: |
|                                   | Each duplicate must still be      |
|                                   | given an unique $bond, $angle,    |
|                                   | $dihedral, $improper style        |
|                                   | variable name.                    |
+-----------------------------------+-----------------------------------+
| -nocheck                          | Do *not* check for common         |
|                                   | LAMMPS/moltemplate syntax errors. |
|                                   | (This might be useful when using  |
|                                   | moltemplate with simulation       |
|                                   | software other than LAMMPS, *or*  |
|                                   | to build systems which need new   |
|                                   | non-standard LAMMPS features.)    |
+-----------------------------------+-----------------------------------+
| -checkff                          | This forces moltemplate.sh to     |
|                                   | check that there are valid angle  |
|                                   | and dihedral interactions defined |
|                                   | for every 3 or 4 consecutively    |
|                                   | bonded atoms in the system        |
|                                   | (defined in “Data Angles By Type” |
|                                   | and “Data Dihedrals By Type”      |
|                                   | sections).                        |
+-----------------------------------+-----------------------------------+
| -vmd                              | Invoke VMD after running          |
|                                   | moltemplate to view the system    |
|                                   | you have just created. (VMD must  |
|                                   | be installed. See sections        |
|                                   | :ref:`sec:vmd_topotools`,         |
|                                   | :ref:`sec:vmd_advanced` for       |
|                                   | details.)                         |
+-----------------------------------+-----------------------------------+
| -dihedral-sym file.py             |                                   |
|                                   |                                   |
| -improper-sym file.py             |                                   |
|                                   |                                   |
| -bond-symmetry file.py            |                                   |
|                                   |                                   |
| -angle-symmetry file.py           |                                   |
|                                   | Normally moltemplate.sh reorders  |
|                                   | the atoms in each bond, angle,    |
|                                   | dihedral, and improper            |
|                                   | interaction before writing them   |
|                                   | to the DATA file in order to help |
|                                   | avoid duplicate interactions      |
|                                   | between the same atoms if listed  |
|                                   | in different but equivalent       |
|                                   | orders. Sometimes this is         |
|                                   | undesirable. **To disable this    |
|                                   | behavior, set “file.py” to        |
|                                   | “None”.** You can also manually   |
|                                   | choose alternate symmetry rules   |
|                                   | for unusual force fields. (Such   |
|                                   | as class2 force fields,           |
|                                   | dihedral_style spherical, etc...  |
|                                   | For an example of the file format |
|                                   | for “file.py”, see the            |
|                                   | “nbody_Impropers.py” file.)       |
+-----------------------------------+-----------------------------------+
| -molc                             | Helpful additional                |
|                                   | post-processing for users of the  |
|                                   | **MOLC** coarse-grained model.    |
+-----------------------------------+-----------------------------------+
| -full-comment-names               | Moltemplate writes atom type      |
|                                   | names in the comments following   |
|                                   | the “Masses” section of a LAMMPS  |
|                                   | data file. These two arguments    |
|                                   | control whether or not the        |
|                                   | *short* or *full* versions of the |
|                                   | atom type names are printed       |
|                                   | there. (Default: short. See       |
|                                   | section :ref:`sec:full_names` for |
|                                   | details.)                         |
+-----------------------------------+-----------------------------------+
| -forbid-wildcards                 | Forbid the use of “\*” and “?”    |
|                                   | characters in “pair_coeff”,       |
|                                   | “bond_coeff”, “angle_coeff”,      |
|                                   | “dihedral_coeff”, and             |
|                                   | “improper_coeff” commands. (eg:   |
|                                   | “bond_coeff @bond:CH?? ...”,      |
|                                   | “pair_coeff @atom:C\* @atom:C\*   |
|                                   | ...”. These are allowed by        |
|                                   | default.)                         |
+-----------------------------------+-----------------------------------+
