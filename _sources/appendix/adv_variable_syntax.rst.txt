.. _sec:adv_variable_syntax:

Variable syntax details
=======================

Counter variables have names like:

$\ **cpath**/**catname**:**lpath**

or

@\ **cpath**/**catname**:**lpath**

(Note: All of the variable examples in this appendix can refer to either
static @ variables or instance $ variables. Both variable types obey the
same syntax rules. For brevity, only the instance $ variables are
shown.)

All counter variables have 3 parts:

**cpath**, the category scope object (which is usually omitted)

**catname**, the category name

**lpath**, the “leaf path”. This includes the variable’s name and
(optionally) the location of that variable in the object tree relative
to the object in which the variable is referenced (the current-context
object)

Typically the **cpath** is omitted, in which case it means that the
category has global scope. *(This is true for all of the standard
counter variable types: “@atom”, “$atom”, “$mol”, “@bond”, “$bond”,
“@angle”, “$angle”, “@dihedral”, “$dihedral”, “@improper”, and
“$improper”.)* However the **cpath** can be specified explicitly, as in
this example: “$/atom:” (“/” denotes explicitly that the counter has
global scope). Another example with an explicit **cpath** is the custom
local counter variable named “$/proteins[5]/monomerid:.” (See section
:ref:`sec:cpath_simple`.) In this example, the **cpath** is
“$/proteins[5]”, the **catname** is “monomerid”, and the **lpath** is
“.”. (In section :ref:`sec:cpath_simple`, we never explicitly
specified the **cpath**. This is a source of confusion. When **cpath**
is omitted, then the program searches up the tree for an ancestor node
containing a category with a matching **catname**. Consequently the
**cpath** rarely ever needs to be stated explicitly. See section
`17.2 <#sec:variables_shorthand>`__ for more details.)

General variable syntax
-----------------------

The ellipsis (“...”) commonly appears in counter variables (or it is
implied). The most complex and general variable syntax is:

$\ **cpath**/.../**catname**:**lpath**

This means: find the closest ancestor of the **cpath** object containing
a category named “**catname**”. This ancestor determines the category’s
scope. Counter variables in this category are local to ancestors of that
object. In this usage example, **lpath** identifies the location of the
variable’s corresponding “leaf” object relative to the category scope
object (**cpath**). On the other hand, if the the category’s scope
(**cpath**) was not explicitly stated by the user (which is typical),
then the **lpath** identifies the location of the leaf object relative
to the object in which the variable was referenced (the current-context
“.”).

.. _sec:variables_shorthand:

Variable shorthand equivalents
------------------------------

$\ **catname**:**lpath** is equivalent to “$.../\ **catname**:**lpath**”
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This means: find the closest direct ancestor of the current object
containing a category whose name matches **catname**. If not found,
create a new category (at the global level). *This is the syntax used
most frequently in LT files.*

If the colon is omitted, as in $\ **lpath**/**catname**, then it is
equivalent to: $\ **catname**:**lpath**. Again, in these cases,
**lpath** is a path which is relative to the object in which the
variable was referenced.

If $\ **lpath** is omitted, then this is equivalent to $\ **catname**:.
In other words, the the leaf node is the current node, “.”. (This syntax
is often used to count keep track of molecule ID numbers. You can use
the counter variable “$mol” to keep track of the current molecule id
number, because it counts the molecular objects in which this variable
was defined. In this case the name of the category is “mol”. As in most
examples, the category object, **cpath**, is not specified. This means
the category object is automatically global. A global category object
means that every molecule object is given a unique ID number which is
unique for the entire system, not just unique within some local
molecule. As a counter-example, consider amino acid residue counters.
Each amino acid in a protein can be assigned a residue ID number which
identifies it within a single protein chain. However because their
category was defined locally at the protein level, these residue ID
numbers are not global, and are not uniquely defined if there are
multiple protein chains present.) (See section
:ref:`sec:cpath_simple` for details.)

$\ **cpath**/**catname**:**lpath**/...
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*(SHORTHAND equivalent)*

Find the category name and object corresponding to
“$\ **cpath**/**catname**:” (see above) If $\ **cpath**/ is blank, then
search for an ancestor with a category whose name matches **catname**,
as described above. To find the variable’s corresponding “leaf object”,
start from the CURRENT object (not the category object). If **lpath** is
not empty, follow **lpath** to a new position in the tree. Otherwise,
start at the current object. (An empty **lpath** corresponds to the
current object.) From this position in the object tree search for a
direct ancestor which happens to also be “leaf object” for some other
variable which belongs to the desired category. If no such variable is
found, then ttree creates a new variable whose leaf object is the object
at the **lpath** position, and put it in the desired category.

$\ **lpath**/.../**catname** is equivalent to $\ **catname**:**lpath**/...
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*(SHORTHAND equivalent)*

If **lpath** is omitted, then start from the current node. (In the
molecular examples, “$.../mol” is a variable whose category name is
“mol”. The “leaf object” for the variable is either the current object
in which this variable was defined, OR a direct ancestor of this object
which has been assigned to a variable belonging to the category named
“mol”. In this way large objects (large molecules) can be comprised of
smaller objects, without corrupting the “mol” counter which keeps track
of which molecule we belong to. In other words, “$.../mol” unambiguously
refers to the ID# of the large molecule to which this sub-molecule
belongs (regardless of however many layers up that may be).)

$\ **cpath**/**catname**:**lpath**
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*Variables in the output_ttree/ttree_assignments.txt file use the this
syntax.*

If the user explicitly specifies the path leading up to the cat node,
and avoids using “...”, then **lpath** is interpreted relative to the
category object, not the current object (however **cpath** is
interpreted relative to the current object). This happens to be the
format used in the “ttree_assignments.txt” file (although you can use it
anywhere else in an “.LT” file). In “ttree_assignments.txt” file,
**cpath** is defined relative to the global object. The variables in
that file always begin with “$/” or “@/”. The slash at the beginning
takes us to the global environment object (to which all the other
objects belong). (Since the variables in the “ttree_assignments.txt”
always begin with “$/” or “@/”, this distinction is usually not
important because the category object for most variables usually is the
“global” root object.)
