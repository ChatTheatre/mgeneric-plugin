---++ `<nop>`{=html}UrHuman`</nop>`{=html} details - overrides and
cacheing

Each detail now calls MGeneric:UrAvatar merry:lib:makebodypartdesc to
generate its brief, look and examine.

This allows for a number of things:

    * overrides available for all details
    * proper working disguises
    * cacheing of descritpions

---+++ Detail overrides.

The brief, look and examine of each detail can now be overridden. To do
this simply set the following properties:

    * appearance:[detailname]:brief:override
    * appearance:[detailname]:look:override
    * appearance:[detailname]:examine:override

these descriptions will now show on the appropriate detail overriding
the base `<nop>`{=html}UrHuman descriptions. Tattoos, scars and wounds
will still be added to the description. One thing to note is that the
descriptions for the look and examine will automatically have 'a' or
'an' prepended and a full stop appended if one is needed. There exists
an array called *descriptions:noa* on MGeneric:UrHuman in which you can
place the names of details that do not need the 'a' or 'an' at the
begining.

---+++ Disguises

One of the things makebodypartdescription considers when creating a
detail description is disguises. Once worn a disguise will set
disguise:descriptions on the player body and this is used by
makebodypartdescription to generate the description for the details
brief, look and examine (see the disguises document for full details of
disguises). Disguise descriptions override all other descriptions even
replacing bespoke descriptions whilst the disguise is worn.

---+++ Detail cacheing

So that the game doesn't lag horribly now that each detail in every
person calls makebodypartdescription the generated descriptions are
stored in the mapping *descriptions:cache* on each player.
`<br>`{=html}Makebodypartdescription looks first at this mapping and
retrieves the description from it if one exists for the detail in
question. `<br>`{=html} If a description is not cached it is generated
and placed in the cache for future use.

The cache is flushed on a number of occasions:

    * setprop-post trigger on appearance:*
    * setprop-post trigger on alteration:*
    * setprop-post trigger on wound:*
    * setprop-post trigger on bruise:*
    * setprop-post trigger on scar:*
    * setprop-post trigger on disguised:*
    * wearing and unwearing clothes

The setprop-post triggers can all be found on MGeneric:UrAvatar as can
the act-post:wear and unwear scripts.

The wear/unwear scripts look at the coverage areas for the garment,
cross-references them to a mapping called *coveragetodetail* on
`<nop>`{=html}MGeneric:UrHuman and using the detail names found in that
mapping, flushes the descriptions of the now covered or uncovered
details.

If you find that people are having problems with the detail descriptions
not reading correctly a quick fix before hunting out the bug would be to
+setp player "descriptions:cache nil" thus forcing all detail
descriptions to be regenerated.

-- Main.CarlGriffiths - 20 Oct 2004
