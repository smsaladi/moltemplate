Quick reference *(skip on first reading)*
=========================================

.. _note-new-users-should-skip-to-section-sectutorial:

 *Note: New users should skip to section*\ `4 <#sec:tutorial>`__ 
=================================================================

Moltemplate commands
--------------------

+-----------------------------------+-----------------------------------+
| **command**                       | **meaning**                       |
+===================================+===================================+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| *MolType* **{**                   |                                   |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \hspace{0.35cm}                |                                   |
|                                   |                                   |
| *content* ...                     |                                   |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| **}**                             |                                   |
+-----------------------------------+-----------------------------------+
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
|                                   | `4.1 <#sec:spce_example>`__,      |
|                                   | `6.1 <#sec:2bead>`__,             |
|                                   | `9.8 <#sec:inheritance>`__, &     |
|                                   | `9.4.1 <#sec:trappe>`__.          |
+-----------------------------------+-----------------------------------+
| *mol_name* = **new** *MolType*    | Create (instantiate) a copy of a  |
|                                   | molecule of type *MolType* and    |
|                                   | name it *mol_name*. (See section  |
|                                   | `4.1 <#sec:spce_example>`__.)     |
+-----------------------------------+-----------------------------------+
| *mol_name* = **new**              | Create a copy of a molecule and   |
| *MolType*.\ *xform()*             | apply coordinate transformation   |
|                                   | *xform()* to its coordinates.     |
|                                   | (See sections                     |
|                                   | `4.2 <#sec:coords_intro>`__ and   |
|                                   | `3.3 <#sec:xforms_table>`__.)     |
+-----------------------------------+-----------------------------------+
| *molecules* = **new** *MolType*   | Create *N* copies of a molecule   |
| [*N*].\ *xform()*                 | of type *MolType* and name them   |
|                                   | *molecules[0]*, *molecules[1]*,   |
|                                   | *molecules[2]*... Coordinates in  |
|                                   | each successive copy are          |
|                                   | cumulatively transformed          |
|                                   | according to *xform()*. (See      |
|                                   | sections                          |
|                                   | `4.2 <#sec:coords_intro>`__,      |
|                                   | `7.1 <#sec:arrays+xform>`__ and   |
|                                   | `3.3 <#sec:xforms_table>`__.)     |
|                                   | Multidimensional arrays are also  |
|                                   | allowed. (See section             |
|                                   | `7.6 <#sec:multidimensional_array |
|                                   | s>`__.)                           |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \hspace{3.7cm}                 |                                   |
|                                   |                                   |
| **[N]**.\ *xform2()*              |                                   |
+-----------------------------------+-----------------------------------+
|                                   | Apply coordinate transformations  |
|                                   | ( to , before making *N* copies   |
|                                   | of it while cumulatively applying |
|                                   | . (See section                    |
|                                   | `[sec:xform+arrays+xform] <#sec:x |
|                                   | form+arrays+xform>`__             |
|                                   | and `7.3 <#sec:xform_order>`__.)  |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \hspace{0.6cm}                 |                                   |
|                                   |                                   |
| **random**\ ([*M1.xf1()*,         |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \hspace{2.3cm}                 |                                   |
|                                   |                                   |
| *M2.xf2()*,                       |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \hspace{2.3cm}                 |                                   |
|                                   |                                   |
| *M3.xf2()*,...],                  |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \hspace{2.25cm}                |                                   |
|                                   |                                   |
| [:math:`p_1`, :math:`p_2`,        |                                   |
| :math:`p_3`,...],                 |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \hspace{2.25cm}                |                                   |
|                                   |                                   |
| *seed*)                           |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    | Generate an array of *N*          |
|                                   | molecules randomly selected from  |
|    \hspace{0.6cm}                 | with probabilities , using        |
|                                   | (optional) initial coordinate     |
| **[N]**.\ *xform()*               | transformations *xf1(), xf2(),    |
|                                   | xf3, ...*, and applying           |
|                                   | transformation *xform()*          |
|                                   | cumulatively thereafter. This     |
|                                   | also works with multidimensional  |
|                                   | arrays. **You can directly        |
|                                   | specify the number of each type   |
|                                   | of molecule** by replacing the    |
|                                   | list of probabilities , with a    |
|                                   | list of integers . (See sections  |
|                                   | `7.4 <#sec:random_arrays>`__ and  |
|                                   | `7.8 <#sec:random_advanced>`__.)  |
+-----------------------------------+-----------------------------------+
| *NewMol* = *OldMol*               | Create a new molecule **type**    |
|                                   | based on an existing molecule     |
|                                   | type. Additional atoms (or bonds, |
|                                   | etc...) can be added later to the |
|                                   | new molecule using . (See section |
|                                   | `8.4 <#sec:molecule_customization |
|                                   | >`__.)                            |
+-----------------------------------+-----------------------------------+
| *NewMol* = *OldMol*.\ *xform()*   | Create a new molecule **type**    |
|                                   | based on an existing molecule     |
|                                   | type, and apply coordinate        |
|                                   | transformation *xform()* to it.   |
|                                   | (See section                      |
|                                   | `8.4 <#sec:molecule_customization |
|                                   | >`__.)                            |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \hspace{0.35cm}                |                                   |
|                                   |                                   |
| *additional content* ...          |                                   |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| }                                 |                                   |
+-----------------------------------+-----------------------------------+
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
|                                   | `[sec:inheritance_intro] <#sec:in |
|                                   | heritance_intro>`__,              |
|                                   | `9.8 <#sec:inheritance>`__, and   |
|                                   | `9.8.1 <#sec:multiple_inheritance |
|                                   | >`__)                             |
+-----------------------------------+-----------------------------------+
| *MolType*.\ *xform()*             | Apply the coordinate transform    |
|                                   | *xform()* to the coordinates of   |
|                                   | the atoms in all molecules of     |
|                                   | type *MolType*. (See section      |
|                                   | `8.4 <#sec:molecule_customization |
|                                   | >`__.)                            |
+-----------------------------------+-----------------------------------+
| *molecule*.\ *xform()*            | Apply the coordinate transform    |
|                                   | *xform()* to the coordinates in   |
|                                   | *molecule*. (Here *molecule*      |
|                                   | refers to a specific instance or  |
|                                   | copy of a particular molecule     |
|                                   | type. See sections                |
|                                   | `8 <#sec:custom_xform>`__ and     |
|                                   | `4.2 <#sec:coords_intro>`__.)     |
+-----------------------------------+-----------------------------------+
| *molecules*\ [*range*].\ *xform() | Apply the coordinate transform    |
| *                                 | *xform()* to the coordinates of   |
|                                   | molecules specified by . (This    |
|                                   | also works for multidimensional   |
|                                   | arrays. See sections              |
|                                   | `7.5 <#sec:array_wildcards_intro> |
|                                   | `__                               |
|                                   | and `8 <#sec:custom_xform>`__.)   |
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
|                                   | `8.3 <#sec:delete>`__.            |
+-----------------------------------+-----------------------------------+
| **delete** *molecules*\ [*range*] | Delete a range of molecules       |
|                                   | specified by . (This also works   |
|                                   | for multidimensional arrays. See  |
|                                   | sections `8.3 <#sec:delete>`__    |
|                                   | and                               |
|                                   | `7.10 <#sec:delete_holes>`__.)    |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \hspace{0.35cm}                |                                   |
|                                   |                                   |
| *text* ...                        |                                   |
+-----------------------------------+-----------------------------------+
| }                                 |                                   |
+-----------------------------------+-----------------------------------+
|                                   | Write the text enclosed in curly  |
|                                   | brackets to file . The *text* can |
|                                   | contain @variables which are      |
|                                   | replaced by integers. (See        |
|                                   | sections `5.1 <#sec:write>`__ and |
|                                   | `5.2 <#sec:variables>`__.)        |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \hspace{0.35cm}                |                                   |
|                                   |                                   |
| *text* ...                        |                                   |
+-----------------------------------+-----------------------------------+
| :math:`\}`                        |                                   |
+-----------------------------------+-----------------------------------+
|                                   | Write the text enclosed in curly  |
|                                   | brackets to file *file*. *This is |
|                                   | done every time a new copy of     |
|                                   | this molecule is created using    |
|                                   | the “new” command.* The *text*    |
|                                   | can contain either @variables or  |
|                                   | $variables which will be replaced |
|                                   | by integers. (See sections        |
|                                   | `5.1 <#sec:write>`__ and          |
|                                   | `5.2 <#sec:variables>`__.)        |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \multicolumn{2}{p{16.5cm}} {   |                                   |
|    Note: \textit{file} names begi |                                   |
| nning with ``Data '' or ``In ''   |                                   |
|    (such as ``Data Atoms'' or ``I |                                   |
| n Settings'') are inserted        |                                   |
|    into the relevant section of t |                                   |
| he LAMMPS data file or input scri |                                   |
| pt.                               |                                   |
|    (See section \ref{sec:DataIn}. |                                   |
| )                                 |                                   |
|    }                              |                                   |
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
|                                   | `9.7 <#sec:using_namespaces>`__.) |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| *or*                              |                                   |
+-----------------------------------+-----------------------------------+
| **category**                      |                                   |
| *@catname*\ (:math:`i_0`,         |                                   |
| :math:`\Delta`)                   |                                   |
+-----------------------------------+-----------------------------------+
|                                   | Create a new variable category.   |
|                                   | See section                       |
|                                   | `14.2 <#sec:custom_categories>`__ |
|                                   | for details.                      |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| *or*                              |                                   |
+-----------------------------------+-----------------------------------+
| **create_static_var** {           |                                   |
| *@variable* }                     |                                   |
+-----------------------------------+-----------------------------------+
|                                   | Create a variable specific to     |
|                                   | this molecule object.             |
|                                   | (**create_var** is typically used |
|                                   | to create molecule-ID numbers     |
|                                   | that are shared between subunits  |
|                                   | of a larger molecule. See section |
|                                   | `[sec:2beadPolymer] <#sec:2beadPo |
|                                   | lymer>`__.                        |
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

| (See section `5.2 <#sec:variables>`__ for details.)

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
|                                   | `4.1 <#sec:spce_example>`__.      |
|                                   | Note: is shorthand for )          |
+-----------------------------------+-----------------------------------+
| $\ *mol:*...                      | The ID number assigned to the     |
|                                   | molecule to which this object     |
|                                   | belongs (if applicable). See      |
|                                   | sections                          |
|                                   | `[sec:2beadPolymer] <#sec:2beadPo |
|                                   | lymer>`__,                        |
|                                   | `9.6.2 <#sec:ellipsis_mol>`__,    |
|                                   | and appendix                      |
|                                   | `17 <#sec:adv_variable_syntax>`__ |
|                                   | .                                 |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \multicolumn{2}{p{16.5cm}} {   |                                   |
|    %Variable operations           |                                   |
|    \textit{The numbers assigned t |                                   |
| o each variable are saved in the  |                                   |
| \textbf{output\_ttree/ttree\_assi |                                   |
| gnments.txt} file}                |                                   |
|    %See section \ref{sec:output_t |                                   |
| tree}.                            |                                   |
|    }                              |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \multicolumn{2}{l} {           |                                   |
|    %Variable operations           |                                   |
|    \quad \textit{\textbf{Advanced |                                   |
|  variable usage}}                 |                                   |
|    }                              |                                   |
+-----------------------------------+-----------------------------------+
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
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| *$\ *\ **{**\ *\ category:variabl |                                   |
| e\ *\ **}**                       |                                   |
+-----------------------------------+-----------------------------------+
|                                   | Curly-brackets, **{}**, are used  |
|                                   | to refer to variables with        |
|                                   | non-standard delimiters or        |
|                                   | whitespace characters. (See       |
|                                   | section                           |
|                                   | `[sec:vardetails] <#sec:vardetail |
|                                   | s>`__.)                           |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| @{category:\ *type*.ljust(n)}  or |                                   |
+-----------------------------------+-----------------------------------+
| ${category:\ *name*.rjust(n)}  or |                                   |
+-----------------------------------+-----------------------------------+
| ${category:\ *name*.ljust(n)}     | Print the counter variable in a   |
|                                   | right-justified or a              |
|                                   | left-justified text-field of      |
|                                   | fixed width :math:`n` characters. |
|                                   | (This is useful for generating    |
|                                   | text files which require          |
|                                   | fixed-width columns.)             |
+-----------------------------------+-----------------------------------+

.. raw:: latex

   \pagebreak

.. _sec:xforms_table:

Coordinate transformations
--------------------------

| (See sections `4.2 <#sec:coords_intro>`__) and
  `7.1 <#sec:arrays+xform>`__) for details.)

| l|p10cm **suffix** & **meaning**
| *.move(x,y,z)* & Add numbers to the coordinates of every atom
| *.rot(\ :math:`\theta,x,y,z`)* & Rotate atom coordinates by angle
  :math:`\theta` around axis passing through the origin. (Dipole
  directions are also rotated.)
| *.rot(\ :math:`\theta,x,y,z,x_0,y_0,z_0`)* & Rotate atom coordinates
  by angle :math:`\theta` around axis pointing in the direction ,
  passing through the point . (This point will be a *fixed point*.)
| *.rotvv(\ :math:`v_{1x},v_{1y},v_{1z},v_{2x},v_{2y},v_{2z}`)* & Rotate
  atom coordinates with an angle which rotates the vector
  :math:`\mathbf{v}_1` to :math:`\mathbf{v}_2` (around an axis
  perpendicular to both :math:`\mathbf{v}_1` and :math:`\mathbf{v}_2`).
  If you supply 3 additional numbers :math:`x_0,y_0,z_0`, the axis of
  rotation will pass through this location.
| *.scale(ratio)* & Multiply all atomic coordinates by *ratio*.
  *(*\ **Important:**\ *The scale() command does not update force-field
  parameters such as atomic radii or bond-lengths. Dipole magnitudes are
  affected.)*
| *.scale(\ :math:`x_r,y_r,z_r`)* & Multiply coordinates by ,
  respectively

+---------------------------------------------+
| *.scale(ratio,\ :math:`x_0,y_0,z_0`)*  or   |
+---------------------------------------------+
| *.scale(\ :math:`x_r,y_r,z_r,x_0,y_0,z_0`)* |
+---------------------------------------------+

| & You can supply 3 optional additional arguments which specify the
  point around which you want the scaling to occur. (This point will be
  a *fixed point*. Of omitted, the origin is used.)

+----------------------------------------+
| *.quat(\ :math:`a,b,c,d`)*             |
+----------------------------------------+
| *.quat(\ :math:`a,b,c,d,x_0,y_0,z_0`)* |
+----------------------------------------+

| & Rotate atom coordinates by the rotation corresponding to quaternion
  :math:`a+b\mathbf{i}+c\mathbf{j}+b\mathbf{k}` (around , if specified).
| *.matrix(\ :math:`M_{1,1}`,\ :math:`M_{1,2}`,\ :math:`M_{1,3}`,\ :math:`M_{2,1}`,*
| *:math:`M_{2,2}`,\ :math:`M_{2,3}`,\ :math:`M_{3,1}`,\ :math:`M_{3,2}`,\ :math:`M_{3,3}`)*
  & Apply a general linear coordinate transformation.

  .. math::

     \begin{aligned}
     \left(\begin{array}{c}
            x' \\
            y' \\
            z'
     \end{array}\right)
     =
     \left(\begin{array}{ccc}
            M_{1,1} & M_{1,2} & M_{1,3} \\
            M_{2,1} & M_{2,2} & M_{2,3} \\
            M_{3,1} & M_{3,2} & M_{3,3}
     \end{array}\right)
     \left(\begin{array}{c}
            x \\
            y \\
            z
     \end{array}\right)\end{aligned}

+--------------------------------------+
| *push*\ (rot(152.3,0.79,0.43,-0.52)) |
+--------------------------------------+
| monomer1 = new Monomer               |
+--------------------------------------+
| *push*\ (move(0.01,35.3,-10.1))      |
+--------------------------------------+
| monomer2 = new Monomer               |
+--------------------------------------+
| *pop*\ ()                            |
+--------------------------------------+
| *pop*\ ()                            |
+--------------------------------------+

| & Coordinate transformations introduced using the *push()* command are
  applied to molecules instantiated later (using the *new*) command, and
  remain in effect until they are removed using the *pop()* command.
  (And transformations appearing in arrays accumulate as well, but do
  not need to be removed with *pop()*.) In this example, the first
  transformation, “rot()”, is applied to both “monomer1” and “monomer2”.
  The last transformation, “move()”, is applied after “rot()” and only
  acts on “monomer2”.

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
|                                   | `4.2 <#sec:coords_intro>`__.)     |
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
|                                   | `4.2 <#sec:coords_intro>`__.)     |
+-----------------------------------+-----------------------------------+
| -a ’\ *variable* *value*\ ’       | Assign *variable* to *value*.     |
|                                   | (The *variable* should begin with |
|                                   | either a @ character or a $       |
|                                   | character. Single-quotes and a    |
|                                   | space separator are required. See |
|                                   | appendix                          |
|                                   | `14.1 <#sec:manual_assignment>`__ |
|                                   | .)                                |
+-----------------------------------+-----------------------------------+
| -a bindings_file’                 | The variables in column 1 of      |
|                                   | *bindings_file* (which is a text  |
|                                   | file) will be assigned to the     |
|                                   | values in column 2 of that file.  |
|                                   | (This is useful when there are    |
|                                   | many variable assignments to      |
|                                   | make. See appendix                |
|                                   | `14.1 <#sec:manual_assignment>`__ |
|                                   | .)                                |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| .. raw:: latex                    |                                   |
|                                   |                                   |
|    \hspace{0.35cm}                |                                   |
|                                   |                                   |
| *or*                              |                                   |
+-----------------------------------+-----------------------------------+
| -b *bindings_file*                |                                   |
+-----------------------------------+-----------------------------------+
|                                   | Assign variables to values.       |
|                                   | Unlike assignments made with      |
|                                   | “-a”, assignments made using “-b” |
|                                   | are non-exclusive. (They may      |
|                                   | overlap with other variables in   |
|                                   | the same category. See appendix   |
|                                   | `14.1 <#sec:manual_assignment>`__ |
|                                   | .)                                |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| -overlay-angles                   |                                   |
+-----------------------------------+-----------------------------------+
| -overlay-dihedrals                |                                   |
+-----------------------------------+-----------------------------------+
| -overlay-impropers                |                                   |
+-----------------------------------+-----------------------------------+
|                                   | By default moltemplate overwrites |
|                                   | duplicate bonded interactions     |
|                                   | which involve the same set of     |
|                                   | atoms. These flags disable that   |
|                                   | behavior. This can be useful when |
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
|                                   | `4.3 <#sec:vmd_topotools>`__,     |
|                                   | `13 <#sec:vmd_advanced>`__ for    |
|                                   | details.)                         |
+-----------------------------------+-----------------------------------+
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| -improper-sym file.py             |                                   |
+-----------------------------------+-----------------------------------+
| -bond-symmetry file.py            |                                   |
+-----------------------------------+-----------------------------------+
| -angle-symmetry file.py           |                                   |
+-----------------------------------+-----------------------------------+
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
|                                   |                                   |
+-----------------------------------+-----------------------------------+
| -full-comment-names               | Moltemplate writes atom type      |
|                                   | names in the comments following   |
|                                   | the “Masses” section of a LAMMPS  |
|                                   | data file. These two arguments    |
|                                   | control whether or not the        |
|                                   | *short* or *full* versions of the |
|                                   | atom type names are printed       |
|                                   | there. (Default: short. See       |
|                                   | section                           |
|                                   | `5.2.3 <#sec:full_names>`__ for   |
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
