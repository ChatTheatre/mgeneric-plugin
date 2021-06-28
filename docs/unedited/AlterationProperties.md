%META:TOPICINFO{author=\"carlg\" date=\"1059828319\" format=\"1.0\"
version=\"1.1\"}% %META:TOPICPARENT{name=\"WebHome\"}%

## Alteration Properties

For my own sanity I\'ve listed out, with brief descriptions, all the
core properties that need to be set on objects that are used in the
alteration process:

### On object to be altered

**alteration:alterabledetails** \<br\> An array of strings containing
the names of the details that can be altered on the object

**alteration:\[detailname\]:traits** \<br\> A mapping of \"\[alterable
trait\]\":\"\[skillname\]\" for a specific detail listed in
alteration:alterabledetails e.g. to make a persons back tattooable by
someone with the tattooist skill you would specify

    <Core:property property="export:alteration:back:traits">
             ([ "tattoo":"tattooist" ])
    </Core:Property>

**alteration:traits** \<br\> (see above. This mapping contains the
alterable traits of the main object)

**alteration:dictionary** \<br\> A mapping that binds the verb being
used for an alteration to the alterable property for example the
dictionary on UrHuman looks like

    ([ "alter":({ "haircolor", "hairstyle", "hairtype", "hairlength", "faciallength" }), "dye":({ "haircolor" }), "style":({ "hairstyle" }), "tattoo":({ "tattoo" }), "trim":({ "hairlength", "faciallength" }) ])

from this you can see that \"alter\" relates to all the alterable traits
where as \"dye\" is specific to haircolor. This allows the alteration
system to intrepret commands like \"dye bobs hair\" correctly.

**alteration:\[trait\]** \<br\> e.g. alteration:metal holds the value of
the current alterable trait. For an alterable detail this will be
alteration:\[detailname\]:\[trait\]

**alteration:\[detail\]:\[trait\]:amount** \<br\> or
alteration:\[trait\]:amount e.g. alteration:hair:haircolor:amount
specifies the amount of ingredient used when making an alteration

### On a skillDB

**list** \<br\> An array of arrays containing the values by level that
are allowed for an alteration. The first array contains the first level
values, the second array the second level values etc.

**exp** \<br\> This mapping is generated automatically from list. You do
not have to make alterations to this mapping if you add values to list.
(There is a setprop-post on list that calls generateexp).

**alternativevalues** \<br\> This is a mapping that provides an
alternative description to the alteration trait value.

**description** \<br\> This property gives an alternative description to
the trait and is used in the emits for a skill completion. i.e. You
change the \[description\] of fred. If description is not present the
trait name is used. This is used to substitute the word \'type\' for the
hairtype alteration so that the output reads \'You alter the type of
fred hair.\' rather than \'You alter the hairtype of freds hair.\'

**nohold** \<br\> Specifying this property in a skilldb means that the
check to see if you are holding the object before you can alter it is
ignored.

**requiresidentifier** \<br\> Specifying this property on a skilldb
means that the person making the alteration must specifically type in
the alteration value to be used as the system won\'t be able to figure
it out by itself (usually because there are no ingredients for the
alteration to give it a clue) For example if I am holding a brass ingot
and I type forge my sword the system recognises that \'forge\' is
connected to \'metal\' and that you are holding a brass ingot that can
be used as a metal so it alters the metal of your sword to brass. But,
for hairstyling there are no ingredients used to alter the style of your
hair\...therefore you must specify that an identifier is
required\...forcing the player to type style bob\'s hair \"ponytail

**requiresingredient** \<br\> This property specifies that an ingredient
is requried for this alteration. The ingredient object is identified as
being such by the mapping \"ingredients:traits\" which contains an entry
for the trait being altered. e.g. for black dye ingredients:traits is
(\[ \"color\":({ \"black\" }), \"haircolor\":({ \"black\" }) \])
indicating that it can be used as the specific alteration value
\'black\' in a color and a haircolor alteration. Therefore specifiying
requiresingredient in a skilldb means that the player must have an
ingredient who\'s ingredients:traits mapping has an entry for the
alterable trait that the skilldb refers to.

**requiredingredientsdescription** \<br\> A string describing the
required ingredient e.g. for haircolor this reads \'some hair dye\'. If
you do not have the ingredients and you try a haircolor alteration you
will receieve a message ending in \'You require XXX\' where XXX is the
ingredient description i.e. \'You require some hair dye.\'

**additionalingredients** \<br\> This mapping allows you to specify
additional SPECIFIC ingredients that are required to perform an
alteration. This can mean additional ingredients required for all the
alterations or addition ingredients required for a specific alteration.
For example the additional ingredients for the hairstyle skilldb
currently looks like

     ([ "all":([  ]), "ribbon-weave":([ "hairstyle:ribbon":"a ribbon" ]) ])

the \"all\" entry allows you to specify extra ingredients required for
all alterations (e.g. if you wanted to require all hairstyles to use
some silver you would put \"metal:silver\":\"some silver\" in the
\"all\" mapping)

The \"ribbon-weave\" entry indicates that the alteration requires an
ingredient that contains \"ribbon\" in the mapping entry for
\"hairstyle\" in its ingredients:traits property. The entry in the
mapping for additioningredients is also a mapping that contains the
traits required and the description of the ingredient, this description
is used in the same way as requiredingredientdescription.

N.B the trait entries for required ingredients must be in the form
\[alteration trait\]:\[value\] e.g. \"hairstyle:ribbon\"\...this string
is split on the : and the right hand string (ribbon) is searched for in
the left hand string (hairstyle) entry in a potential ingredients
ingredients:traits mapping.

### On an ingredient

**ingredient:name** \<br\> the brief description of the ingredient

**ingredients:traits** \<br\> A mapping that relates alterable traits to
specific trait values

**ingredients:amount** \<br\> A float dictating the amount of the
ingredient

**ingredients:weightconversion** \<br\> A float that is multiplied with
ingredients:amount to produce ingredients:quantity that is then used in
the ingredients description

\-- Main.CarlGriffiths - 02 Aug 2003
