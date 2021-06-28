---++ Guide to MGeneric - Alteration, States and Construction.

---++++ A brief history.

MGeneric was initially created to replace the old BILBO alteration
system. To start with this was a simple re-write of the BILBO code
straight to Merry. However, three quaters of the way through this
conversion, after I had a grasp of how BILBO and the system in general
worked, I decided it would be much better to not only convert to code,
but to standardize and enhance it.

So, the new MGeneric has grown to include the following:

    * Standard property naming convention.<br>All the properties belonging to a system are now named for that system. i.e. all alteration properties are prefixed 'alteration:', construction properties 'construction:', healing properties 'healing:' etc.

    * Alterable details.<br>Details on alterable objects can now be alterable themselves, with alterable properties different than their parent object.

    * Constructable details.<br>This allows you to combine one object with another so that one object becomes a detail of the other. This detail is alterable via the alteration system and can be turned back into a seperate object via deconstruction.

    * States.<br>State descriptions and values can now be associated with objects (more on this in states later).

    * Spliting of 'systems' code into their own objects.<br>The systems (alteration, construction etc.) can now easily be found contained in their own objects such as MGeneric:DB:Alteration, MGeneric:DB:Construction and MGeneric:DB:StateDB.

    * Debug informations.<br>All MGeneric systems functions emit two levels of debug information that can be accessed by setting debug:1 and debug:2 to the value of 1 on your body (these emits are to the $actor only, not into the environment). With debug:1 set to 1 you will be able to see the names of the functions as they are called. With debug:2 you will be able to see details of the information passed to and from the functions as well as internal details of the functions as they run, this can be quite verbose.

    * Similarly structure 'Systems' code.<br>All of the MGeneric systems have their code structured in a similar manner. There is a 'do' function (doConstruction, doDeconstruction, doAlteration) which calls a 'code' and then an 'emit' function i.e. doAlteration calls alterationCode() followed by alterationEmits(). This seperates the emits from the actual system code allowing the systems to be called silently if needed. The 'code' functions return (amongst other things) a return code which the 'emits' code uses the provide the correct emit.

    * Everything is now in Merry.

There are many, many more additions and enhancements within the systems
themselves and it is these, along with the inner workings that the rest
of this doc is concerned with.

---+++ Converting your body from Generic to MGeneric

The MGeneric bodies (UrCharacter-\>UrHuman-\>UrMale/UrFemale) have a
setprop-post hook on the converttomgeneric property that runs the script
MGeneric:DB:General lib:converttomgeneric on the player. This script is
written with Marrach player bodies in mind and does such things as \*
removes all bilbo code from the player \* copies around and tweaks some
alteration properties \* sets up locational scars, tattoos and wounds

To convert your current Generic body do the following:

    * +setprop [player] base:urparent &lt;MGeneric:UrHuman&gt;
    * +setprop [player] converttomgeneric true

You should then see a lot of spammy emits that let you know exactly what
has been altered on your old body to make it into a shiny new MGeneric
one. Then....punch your left arm...after a couple of punches you should
bruise, proving that you are indeed an MGeneric person.

---+++ Alteration.

The changing of the value of a name value pair according to allowed
skills, skill levels, ingredients, ingredient amounts, additional
ingredients, tools and tool levels.

The old alteration system basically did the following :-

    * check the person has the required skill
    * check the person has the required skill level
    * check that the alteration value is a valid one
    * change the alteration value

The new system has added to this to produce the following (as a basic
run through)

    * attempt to work out the alteration property and its value from the verb used and the items held by the player
    * check the requried skill
    * check the required skill level
    * check whether the item to be altered needs holding
    * check for required ingredients
    * check for required ingredient amounts
    * check for additionally require ingredients
    * check for additionally require ingredient amounts
    * check for required tools
    * check for required tool levels
    * change the alteration value
    * reduce ingredient amounts
    * reduce additional ingredient amounts
    * combine ingredients with the player to make details (for combinable ingredients)

As we can see, the alteration system has grown by quite a bit, not to
mention that ingredients can be consumable or non-consumable (more on
this later) which uses the construction system. So, take a deep breath,
get a BIG cup of coffee and lets dive into the alteration system code.

---+++ Alteration Code - step by step

In this section we'll go through the code in MGeneric:DB:Alteration
lib:alterationcode line by line so that we can learn how the different
properties on alterable objects, players, ingredients and tools interact
so that we end up with an altered property value. These are the
individual steps taken in alteration, we'll list them here and then take
an in-depth look at each step:

    * To perform an alteration complete with emits call MGeneric:DB:Alteration lib:doalteration. This function takes one parameter $altverb which is the verb used to perform an alteration. Thus on MGeneric:Thing in merry:react:verb/tailor-dob there is a call that looks like mgalteration::doalteration($altverb:"tailor");

    * doAlteration calls MGeneric:DB:Alteration lib:alterationcode followed by lib:alterationemits. The results array from alterationcode is passed to alterationemits.

    * MGeneric:DB:Alteration lib:alterationcode performs the following steps

        * returns the object to have the alteration performed upon it. This can be either the main object, a temp object created for a detail, or a stored object that has been used as part of a construction. All this takes place in MGeneric:DB:Alteration lib:getobjectobealtered. This function also does error checking to determine whether the object to be altered is generic and that if a detail has been requested that the detail is alterable. It also creates a temp object for an alterable detail. If the object is unalterable at this point or is not generic the function returns with the appropriate code to generate the 'Alas your X cannot be altered.' type of message.

        * obtain a list of ingredients from the person performing the alteration. MGeneric:DB:Alteration lib:getingredients returns an array of objects from either the $using variable or the users inventory.

        * MGeneric:DB:Alteration lib:gettools return an array of tool objects that the actor has in their inventory (see tools later on).

        * identify the property to be altered. MGeneric:DB:Alteration lib:gettrait uses $altverb, ingredients and any evoke in conjunction with the property alteration:dictionary to try to identify a single alterable property that matches the skillverb being used and the ingredients being used by the person performing the alteration. If a single alterable property cannot be determined or the requested alteration value is not valid, alteration will stop at this point returning a result code to allow alterationemits to generate the appropriate message.

        * the current value for the property to be altered is obtained. If this value is nil the currentvalue is set to "none".

        * MGeneric:DB:Alteration lib:getalterationvalue returns an array of possible values that the property could be altered to. This is taken either directly from the evoke or, if the value is not explicitly specified by the player, it is taken from the ingredients being used (i.e. red dye will indicate that 'red' is the alteration value if 'color' is the property being altered). If at this point we have more than one possible alteration value we exit the function. Before doing this we must perform a skill check on the actor. If they do not possess the skill required to perform the alteration then they will see a 'Alas you do not have the skill...' type of message. Otherwise the possible list of alteration values available to them is displayed.

        * If we only have one alteration value specified MGeneric:DB:Alteration lib:validalterationvalue checks to see that the requested alteration is a valid one. If the alteration value is not valid the function returns, passing the appropriate return code back so that alterationemits can produce the correct message.

        * next, MGeneric:DB:Alteration lib:checkisholding ensures that the object to be altered is being held if the flag alteration:nohold is not set on the object. Again, if this test fails the appropriate return code is generated. This in turn is used by alterationemits to generate the correct response to the player.

        * MGeneric:DB:Alteration lib:checkskill ensures that the actor has the required skill to perform the alteration.

        * MGeneric:DB:Alteration lib:checkskilllevel ensures that the actor has the correct level of skill to perform the requested alteration

        * MGeneric:DB:Alteration lib:checkcurrentvalue ensures that the value of the property to be altered and the value that it will be altered to is not the same.

        * MGeneric:DB:Alteration lib:checknotalreadychanged ensures that if the object to be altered has already been altered and that if skill DB for the alteration indicates checklevelonchange the actor must have a skill level of 8 or higher to perform the alteration.

        * MGeneric:DB:Alteration lib:checktools ensures that the tools specified for the alteration in the skilldb are present. It also checks that the tools are of a high enough level to perform the alteration.

        * MGeneric:DB:Alteration lib:checkingredients ensures that the actor has the correct ingredients required for the alteration.

        * MGeneric:DB:Alteration lib:checkadditionalingredients ensures that the actor has the additional ingredients that may have been specified for the particular alteration they are attempting.

        * MGeneric:DB:Alteration lib:checkingredientamounts ensures that the actor has enough of the required ingredients to perform the alteration.

        * MGeneric:DB:Alteraiton lib:checkadditionalingredientamounts ensures that the actor has enough of the specified additional ingredients required to perform the alteration.

        * At this point...if all of these checks have been passed the alteration can begin. To perform the alteration the following steps are taken:

        * MGeneric:DB:Alteration lib:deconstructionadditionalingredients. If a previous alteration has required an additional ingredient that is non-consumable, this ingredient is returned to the person performing the alteration at this point. i.e. if I previously had my hair styled to ribbon-weave I would have used a non-consumable ribbon ingredient that would be turned into a detail on my body. On altering my hair style to ponytail this ribbon will be returned.

        * MGeneric:DB:Alteration lib:reduceingredientamounts calls reduceamounts for each ingredient being used in the alteration.

        * MGeneric:DB:Alteration reduceadditionalingredientamounts. This function either reduces the amount of a consumable ingredient or performs the construction of a non-consumable ingredient using MGeneric:DB:Construction.

        * MGeneric:DB:Alteration lib:setvalue sets the property value to the specified alteration value.

        * at this point if the object being altered has the hook alteration:hooks:createdetail-<detailname> specified it is called. This allows extra details to be created (tattoo's and master marks being examples of this).

        * MGeneric:DB:Alteration lib:setadjectives alters the adjectives of the object to reflect the alteration being performed.

        * MGeneric:DB:Alteration lib:remakedetail converts any temporary objects (due to detail alteration) copying the alteration values back to the parent object and regenerating descriptions for details that are a result of construction.

        * MGeneric:DB:Alteraiton lib:makedetaildescriptions generates a comma seperated list of descriptions of altered details (their briefs) that are not plain or ordinary so that this can be included in the main object descriptions to produce lines like 'with a pink hem, yellow seam and purple lining.'

        * MGeneric:DB:Alteration lib:tidyup slays any temporary created objects.

        * a return code is now generated indicating success and this is passed to MGeneric:DB:Alteration lib:alterationemits.

MGeneric:DB:Alteration lib:alterationemits is a simple case statement
that uses the return values from alterationcode to generate the correct
emits to the actor and their environment. This includes listing out
alteration values for single and multiple properties, error messages and
the success message.

So, that's the run through of the alteration process. As a side note,
all the variables used in the functions are local, \$variables are only
used as parameters and where delays require them. This applies to all of
the MGeneric code.

---+++ Alteration properties.

The following properties are used in the alteration process.

    * Alteration:traits<br>This mapping contains the alterable properties of the main object and the titles of the people doing the alteration e.g. (["color":"dyer","metal","jeweler"]). This property is declared on the item to be altered and allows different crafts people to alter the same property depending on the item being altered. e.g. jewelry may allow the metal property to be altered by a jeweler, but weaponry may only allow the metal property to be altered by a weaponsmith.

    * Alteration:dictionary<br>This mapping assoicates skill verbs with the alteration values for the object. For example (["alter",({"color","metal","material"}),"dye":({"color"}),"tailor",({"material")]) shows us that all of the properties can be altered using the verb alter but that the skill verb dye is specific to the color property and the skillverb tailor is specific to the material property. The trait is declared on the object being altered (or its base class).

    * Alteration:alterabledetails<br>This is an array of the detail names that are alterable on the object. For example alteration:alterabledetails on MGeneric:UrHuman looks something like ({"leftarm","rightarm","chest","leftcheek","rightcheek","hair" etc. etc.})

    * Alteration:[property]<br>e.g. alteration:color. This property holds the current value for the main object. This property is created on the object to be altered by the alteration system.

    * Alteration:[property]:amount<br>e.g. alteration:color:amount. This property specifies the amount of a 'color' ingredient that is required for each alteration. This property is defined on the object to be altered.

    * Alteration:[detail]:[property]<br>e.g. alteraiton:hair:haircolor. This property holds the current value for the alteration property for the specific detail (in this case the haircolor of the hair detail).This property is created on the object being altered by the alteration system.

    * Alteration:[detail]:[property]:amount<br>e.g. alteration:hair:haircolor:amount. This specifies the amount of ingredient needed to perform a single alteration of the hair details haircolor. This property should be set on the object being altered.

N.B. the actual checking for ingredient amounts is as
follows:`<br>`{=html} check the skillDB (i.e. SkillColorDB) for the
property alteration:\[property\]:amount i.e. alteration:color:amount.
Next, check for the property alteration:\[property\]:\[value\]:amount
i.e. alteration:color:red:amount. After this look for these properties
on the object being altered. Which ever value was last read is the
amount of ingredient needed to perform the alteration.

---+++ Alteration - how to make something alterable.

    * Inherit the object from one of the MGeneric Ur objects or from MGeneric:Thing or MGeneric:UrMale/UrFemale.

    * Define the following properties on the object
        * Alteration:dictionary
        * Alteration:traits

    * If ingredients are required for the alteration you can specify amounts specific to the item being altered using Alteration:[property]:[value]:amount e.g. alteration:color:red:amount or the more general Alteration:[property]:amount e.g. alteration:color:amount. However, you can ignore these and use the default settings on the appropriate skilldb.

    * If you require alterable details on the item define the array alteration:alterabledetails to hold the names of the details to be altered. e.g. ({"blade","pommel","guard"})

    * For each of the alterable details define their alterable properties using the mapping alteration:[detail name]:traits e.g. alteration:blade:traits = (["metal":"weaponsmith","design","weaponsmith"])

    * as above, if you require ingredients for a details alteration you can define required ingredient amounts using the properties alteration:[detailname]:[property]:[value]:amount e.g. alteration:blade:metal:silver:amount or the more general alteration:[detailname]:[property]:amount e.g. alteration:blade:metal:amount. Or again, you can leave these and rely on the default amounts as defined in the skillDB.


    * Lastly...ensure that the alteration properties are included in the SAM for the object or the detail. Alteration will produce values under the properties alteration:[property]or alteration:[detailname]:[property] e.g. alteration:metal or alteraition:blade:metal. You can also include alteration:detaildescriptionstring in the defaul detail descriptions to identify the altered details on the item to produce an overall description in the form :<br> 'A silver longsword with a skymetal pommel and a gold guard.'

And that's all there is to it. You can see examples of all of the above
by looking at the MGeneric Ur objects.

------------------------------------------------------------------------

---+++ SkillDBs

SkillDB's hold the list of valid alterations by level for alterable
properties. So far there are SkillDBs for haircolor, hairlength,
hairtype, hairstyle, color, metal, wood, symbol, tattoo, stain, fit,
design, binding, engraving, facialhair, faciallength, materail, paper,
shape, stone and stuffing.

Internally the skillDB's are composed of the following properties:

*list* `<br>`{=html}An array of arrays holding the valid alteration
values. The array at list\[0\] holds the first level valid alterations,
the array at list\[1\] holds the second level valid alterations etc.
This property is used to generate the exp mapping and there is a
setprop-post trigger that calls generateExp() in order to do this.

*exp* `<br>`{=html}This mapping associates each valid alteration value
with its level.

*checklevelonchange* `<br>`{=html}Defining this property on the skillDB
forces the alteration system to check if the property on the item being
altered has previously been changed. If this is the case the player
performing the alteration can only do so if they have the required skill
at level 8 or above.

*description* `<br>`{=html}The value of this property, if defined, will
replace the name of the alteration in the alteration emits.
e.g. SkillHaircolorDB has "color" defined as its description. Emits from
the alteration system now read 'You change the color of your hair.'
instead of 'You change the haircolor of your hair.'

*example1 and example2* `<br>`{=html}These values are using in some
alteration emits as examples of valid alteration values.

*nocheckvalues* `<br>`{=html}This array holds the values that do not
require an ingredient or tool check. This array usually holds the values
'none' and 'ordinary'.

*nohold* `<br>`{=html}Defining this flag on the skillDB means that the
alteration does not require the player to be holding the object being
altered.

*requiresingredients* `<br>`{=html}Defining this flag forces the
alteration system to check for ingredients of the alteration type.
SkillHaircolorDB has this property defined and thus the player must have
an ingredient with the property 'ingredients:haircolor:\[alteration
value\]' in order to perform their requested alteration. e.g. if they
typed dye fred's hair \"red they will require an ingredient with
'ingredients:haircolor:red' defined upon it in order to perform the
alteration.

*requiredingredientsdescription* `<br>`{=html}This string gives a short
description of the ingredients indicated by the requriesingredients flag
so that if the user does not have the required ingredients a suitable
emit can be generated. e.g. SkillHaircolorDB has 'some hair dye' defined
as its requiredingredientsdescription so that the emit they would see if
they tried to dye someone's hair without any hair dye would be.

'Alas you cannot dye fred's hair. You require some hair dye.'

*additionalingredients* `<br>`{=html}This mapping allows for the
specification of specific required ingredients for all or some of the
valid alterations. As an example the mapping on SkillHairstyleDB is
`<verbatim>`{=html} (\[ "all":(\[ \]), "ribbon-weave":(\[
"hairstyle:isribbon":"a ribbon" \]) \]) `</verbatim>`{=html}

The indicies of the mapping consist of either valid alteration values or
the special 'all' value. Additional ingredients specified for the 'all'
value are required by all alterations. The mapping values are mappings
themselves. For the alteration value specified by the index the required
ingredient property and description are given. So, for the alteration
value "ribbon-weave" an additional ingredient that has the value
"isribbon" in it's value array for its index of "hairstyle" in the
mapping 'ingredients:traits\" is required (see ingredients later and
MGeneric:clothing:isribbon as an example). If the player performing the
alteration does not have this ingredient the string "a ribbon" will be
added to the required ingredients description giving the emit "Alas you
cannot style Fred's hair to ribbon-weave. You require a ribbon."

*alternative values* `<br>`{=html} This mapping stores alternative
strings for valid alterations that will be used as the value of the
alteration property. E.g. SkillHairstyleDB has the name value pair
"Ardan-twists":"worn in Ardan twists" defined so that if the players
hairstyle is altered to Ardan-twists their alteration:hair:hairstyle
property will contain the value "worn in Ardan twists" which is what
will be seen in the hair details descriptions.

*requriedtools* `<br>`{=html} This is an array of arrays. Each element
is an array containing a string and a mapping. The string identifies a
property that uniquely identifies a tool, the mapping contains
information that relates required tool level to alteration skill level.
For example, in SkillHairstyleDB the requiredtools property is
`<verbatim>`{=html} ({ ({ "tool:hairstyling:isbrush", (\[ 0:0 \]) }) })
`</verbatim>`{=html} from this we can see that to perform a hairstyling
alteration we require a tool holding the property
tool:hairstyling:isbrush. The mapping (\[0:0\]) indicates that for an
alteration of level 0 or above we require a tool with a quality level of
0 or above (for quality levels see tools later in this document). As a
further example the mapping (\[0:0,8:5\]) would indicate that for
alterations of level 0 to 7 a tool of quality level 0 is required but
for alterations of level 8 or above a tool with a quality level of 5 or
above is required.

*requiredtoolsdescription* `<br>`{=html}This is an array of strings
describing the tools defined in requriedtools. This array is used to
generate a string that lets the player performing the alteration which
tools they require.

*usemap* `<br>`{=html}This property (as used in SkillTattooDB) is used
to indicate that the list and exp properties of another skillDB should
be used. In the case of SkillTattooDB the list and exp properties of
SkillSymbolDB are used. This property allows you to define seperate tool
list, required ingredients etc. for alterations that share the same
value lists.

---+++ Adding a new alteration value.

The simplest way of adding in a new alteration value is to decide which
level the alteration value is and then to insert it into the appropriate
array in the list property of the appropriate skillDB. This can be done
using Woe. The setprop-post trigger will take care of the rest.

If you require a different description for the alteration value you can
add a name value pair into the alternativevalues mapping (see
MGeneric:DB:SkillHairstylingDB for a good example of this).

If you then require specific additional ingredients for this new
alteration value then you will have to add in the appropriate
information into the 'additionalingredients' property. You will also
need to create a property called \[alteration\]\[ingredient\]:amount to
specify the amount of the additional ingredient you require. e.g. in
SkillHairstyleDB the property hairstyle:isribbon:amount specifies the
amount of ribbon required for the additional ingredients required by the
ribbon-weave alteration.

  -------------------------
  ---+++ Ingredients
  Ingredients are used in
  the alteration system to
  restrict the availability
  of the alteration value.
  Ingredients come in a
  couple of guises.

  Firstly there is the
  ingredient that
  corresponds directly to
  the alteration being
  performed. If I am
  altering the color of my
  shirt then I'm going to
  require a 'color'
  ingredient. More
  specifically I'm going to
  require the specific
  ingredient for the color
  I'm trying to alter my
  shirt to. This is the
  type of ingredient that
  is required when the
  property
  'requiresingredient' is
  set on the appropriate
  skillDB.

  Next up we have
  additional ingredients.
  These ingredients are
  also required to perform
  an alteration but they
  can be specific to an
  alteration value. They
  are also independant of
  the alteration value and
  therefore have to be
  specifically defined.
  Additional ingredients
  are described in the
  'additionalingredients'
  mapping of the
  appropriate SkillDB (see
  SkillDBs above).

  So, we've seen where the
  ingredient requirements
  are specified, lets take
  a look at how an
  ingredient is defined.
  -------------------------

(skill to do)

Ingredients - ingredient properties, how to make a new ingredient

Tools - tool properties, how to make a new tool

MGeneric bodies - heirarchy, property list with descriptions.

Bruising, Wounding, Scaring, Healing and Death - how these states
interact and the new

death code (step by step). Property listings and descriptions for these
states.

Construction and Deconstruction - following the same outline as
alteration above.

States - following the same outlien as above.

-- Main.CarlGriffiths - 19 Sep 2003
