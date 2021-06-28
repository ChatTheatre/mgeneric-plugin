%META:TOPICINFO{author=\"carlg\" date=\"1052437920\" format=\"1.0\"
version=\"1.7\"}% %META:TOPICPARENT{name=\"WebHome\"}%

## MGeneric Alteration

### Overview

Well, hello and welcome to the second edition of MGeneric alteration. I
rewrote alteration again for two reasons. \* One - I wanted to make the
players interface to the alteration system more natural. \* Two - my
original code was very messy So with this rewrite I\'ve done a number of
things: \* Given as much freedom as I can with the interface \*
Structured the code modularly \* Prepared the code for the further
integration with other systems

### The alteration interface

The previous interface with the alteration system saw people using their
skills with the following syntax -

tailor my shirt \"material silk\<br\> style fred \"haircolor brown

I thought this seemed a bit awkward and with the introduction of
ingredients I decided to make the system a bit more intelligent. As a
result the following type of commands now run the alteration system -

dye fred\'s hair\<br\> dye fred\'s hair with my red dye\<br\> dye my
shirt \"brown\<br\> style bob\'s hair \"ponytail\<br\> trim fred\'s hair
\"short\<br\>

The alteration system figures out which trait you are trying to alter
and the value you are attempting to alter it to using the verb, object,
detail and ingredients presented to it after which it will produce the
appropriate emits as before.

### Code Structure

All the code for the alteration system is now contained in
MGeneric:DB:Alteration and is partitioned into three major parts

\* lib:doalteration \* lib:alterationcode \* lib:alterationemits

#### lib:doalteration

This basically calls lib:alterationcode and passes the results to
lib:alterationemits. So if you want to do any alterations silently you
can simply call lib:doalteration. lib:doalteration takes one parameter
\$altverb which is a string holding the verb that the alteration will be
perfomed with.

So to dye something you do
Call(\${MGeneric:DB:Alteration},\"doalteration\",\$altverb:\"dye\");

#### lib:alterationcode

This is the guts of alteration. However I\'ve tried to keep it as simple
as possible by making it a list of descriptive function calls.
lib:alteration expects \$altverb and \$target and returns an array that
consists of a return code, the previous value of the trait being altered
and the new value of the trait.

N.B previous and current values are only supplied if valid.

#### lib:alterationemits

Again, modularised into seperate emit functions for clarity, this
function creates the output to the \$actor and the environment

### Debugging

All of the function calls in alteration produce two levels of debugging
emits. If you +setprop debug:1 on yourself you will see the flow of the
function calls. If you then setprop debug:2 you\'ll get more detailed
information about parameter values for each function. \<br\>In this way
you can see just exactly what alteration is doing.

### Outline of code flow

Ok then\....here\'s what lib:alterationcode does (disclaimer:ok ok so my
function names get a bit verbose..but at least they\'re kind of
descriptive)-

\* get the object to be altered..if we\'re altering a detail of an
object then generate a temporary object for the detail and if we\'re
altering a detail that has been generated via construction (see
construction system) retrieve this object. The resulting object is used
throughout the rest of the alteration. (lib:getobjecttobealtered)

\* compile an array of ingredients either from \$using or in the
\$actor\'s inventory. Ingredients are denoted by the property
\"ingredients:isingredient\". (lib:getingredients)

\* get the trait to be altered. Firstly the string in \$altverb is
looked up in MGeneric:DB:Alteration}.traitdictionary to retrieve an
array of all the traits that this verb can alter. If we get multiple
matches at this point we look at the object being altered and try to
find a single match. If we\'re still unsuccessful we look through the
ingredients array at each ingredient in turn trying to get a single
match by comparing the verbs trait array against the indices of the
\'ingredients:trait\' mapping of each ingredient.\<br\>The result of
this function can either be \* a single alterable trait name \* a
failure indicating that no matches were found \* a failure indicating
that we couldn\'t determine which trait to alter. If we cannot determine
a single trait to alter then we stop here and output an appropriate emit
(either \'You cant alter that..\' or a listing of the traits you could
alter with the values you could alter them too). (lib:gettrait)

\* retrieve the current value of the trait to be altered from the afore
mentioned object to be altered.

\* get the value that we want to change the trait to. This is obtained
from one of two places either from the \$evoke or from the ingredients
being used. Firstly the code checks to see if the skill being employed
specifically requries an evoke (indicated by the \'requiresidentifier\'
property on the skill db object) as some traits such as hairstyling will
still require the player to indicate the new value in this way. However,
if ingredients are being used in the alteration we will attempt to
determine the new trait value from the ingredient. For example if we
\"dye fred\'s hair with my red dye we know that we want to alter
haircolor to red. (lib:getalterationvalue)

\* having retrieved an alteration value we next check to see that it is
valid for the trait being altered. This is a simple check against the
exp mapping of the appropriate skills db object.
(lib:validalterationvalue)

\* next we check to see if the item being altered requries it to be held
to make the alteration and if it does we ensure that it is being held.
(lib:checkisholding)

\* the skill check is next. Initially we check that the \$actor has any
skill at all with which to make the alteration. the requried skill is
indicated in the skill db onject and the existance of this skill
property is checked for against the \$actor. (lib:checkskill)

\* moving on we check that the \$actor actually has the required skill
level to make the requested alteration. The requried level is obtained
from the exp mapping in the appropriate skill db and compared the the
\$actors current skill level (lib:checkskilllevel)

\* now we check to see if the \$actor is changing the trait value or if
they have inadvertently specified the traits current value.
(lib:checkcurrentvalue)

\* the next check looks to see if the trait has been previously altered.
First we look at the appropriate skill db to see if this check is
needed, the presence of the property \'checklevelonchange\' indicates
that it is. If the trait has a last altered by value the we check that
the \$actor has at least skill level 8 to be able to continue with the
alteration.(lib:checknotalreadychanged)

\* I know that in some areas of alteration it has been requested that
the level in which you can alter a previously altered value be far lower
than 8. Might wish to allow this to be soft-set somewhere rather than
hardset in the code. \-- Jess

\* now we check for tools. The tools requirement is specified on the
skill db object in the properties requiredtools and
requiredtoolsdescriptions. Requiredtools is an array of arrays, each
array containing a property name to identify the tool (e.g.
\"<tool:hair:brushandcomb:brush>\") and then a mapping to indicate the
design level of the tool required to perform a level of skill
(e.g.(\[0:0,5:7,10:10\]) which would indicate that for an alteration for
level 0-5 the tool must have a \'alteration:design\' value of level 0 or
above, for an alteration of skill 5 to 9 the tool would have to have an
\'alteration:design\' value of 7 or above and for a level 10 alteration
the tool must also have an \'alteration:design\' value of 10.
(lib:checktools)

\* This.. this is just confusing. I\'ll wait for an expansion before
trying to figure out what exactly is going on :) \-- Jess

\* now we check the ingredients. Again the required ingredients are
defined in the skill db in the properties \'requriedingredients\' and
\'requiredingredientdescriptions\'. Requiredingredients is a simple
array of property names that must exist on the objects being used as
ingredients. (lib:checkingredients)

\* if all the ingredients are present we then check that we have the
required amounts. These amounts are specified on the object being
altered in the properties \'alteration:\<required ingredient
property\>:amount\' (e.g. alteration:haircolor:amount). These amounts
are then compared against the current amounts of the ingredients the
\$actor is using (in \'ingredients:amount\' on the ingredient
object).(lib:checkingredientamounts)

If all of these checks are passed we can go ahead with the alteration
which proceeds as follows -

\* reduce the amounts of the ingredients being used. The required
ingredients amounts are removed from the appropriate ingredients by
calling the ingredients objects \'reduceamount\'
method.(lib:reduceingredientamounts)

\* the value of the trait is then set on the object being altered
(lib:setvalue)

\* next we look to see if a new detail is to be created for this
alteration. If the base object being altered (NRefOb(\$target)) has a
property called \'alteration:\<detail\>:createdetail\'\....(more later)

\* Everything else looks great! Fairly straight forward. Cant wait to
see it in action. \-- Jess

### Alterable details

Details on an object can now become alterable so, for example, you can
dye the hem on your gown a different colour from the gown itself.
Alterable details aren\'t even restricted to having the same alterable
traits as their parent object.

To make a detail alterable requires the following:

\* the details id (e.g. \'chest\', \'rightarm\', \'hair\' etc.) must be
entered into an array on the parent object called
\'alteration:alterabledetails\'.

\* a property called \'alteration:\[detail id\]:traits\' holds an array
containing the names of the alterable traits for that details. For
example \'alteration:hair:traits\' on MGeneric:UrHuman holds the array
({\"haircolor\", \"hairlength\", \"hairstyle\"})

\* create any hooks if you want to extend the alteration system.
Currently alteration looks for two hooks called
\'merry:lib:alteration:hooks:createdetail-\[altered trait\]\' and
\'alteration:hooks:post-\[altered trait\]\'. For an example see
\'alteration:hooks:createdetail-tattoo\' on MGeneric:UrHuman.

### Ingredients

### Tools

### Skill databases
