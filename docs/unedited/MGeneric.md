---++ MGeneric

    * [[MgenericData][MGeneric Data]]
    * [[MgenericDatawound][MGeneric Data Wound]]
    * [[MgenericDatadeath][MGeneric Data Death]]
    * [[MgenericMorph][MGeneric Morph]]

    * [[NIPLibRefWoundingMGeneric][NIP Library Reference: Wounding MGeneric]]

------------------------------------------------------------------------

Welcome to what will hopefully be an semi-regular updated article that
will document my journey through the re-writting of Generic.

---++ Generic, what's that then?

Good question, glad you asked...`<br/>`{=html} Put simply its the set of
base objects that all the other objects are inherited from. Starting
right at the bottom with Thing and its associated `<nop>`{=html}CodeDB
and `<nop>`{=html}DataDB the `<nop>`{=html}UrClasses inherit most of
their functionaily from here and in turn the main objects in the game
(armbands, pants, people etc) are children of the
`<nop>`{=html}UrClasses.

---++ Why a re-write?

If you take a look at the classes defined under Generic you'll notice a
predominance of BILBO script and arrays. I've spent some time looking at
these in the earlier days of my SPship to try to get a handle on what
was going on and how things generally worked. One thing I did notice was
lots and lots of spurious code, stuff that just didn't seem to get
used...anywhere. Anyhow, with lots of other things to do I kind of
ignored it and tried not to replicate the bad points I'd
seen.`<br/>`{=html} Little did I know what was on the cards. Aparently
Generic had been ear-marked for an overhaul for some time especially
since the introduction of MERRY so when the idea floated past me I
thought "Why not?".`<br/>`{=html} The new Generic will be as much MERRY
as possible without affecting other code (such as the dueling or death
code) that maybe defined elsewhere. The secondary aim is to tidy up as
much as possible, removing antiquated inheritance code and other things
that have crept in over time (I mean, does `<nop>`{=html}UrPillow really
need an array of pages as it it were a book?). So with this in mind it's
time to look for somewhere to start.

---++ Initially

The first problem is always "Where do I start?" shortly followed by
"Just what do I do and how far can I go?".`<br/>`{=html} To answer these
questions I spent a little time getting used to the Generic hierarchy
and decided that one of the root objects (in this case Thing) and its
related code and database objects would have to form the base of my new
development so I'd better start there.`<br/>`{=html} The next problem
"Just how far do I go?" always has the same answer for me..."may as well
do a good job and do it all!". In this case it means converting as much
of the BILBO scripts as possible into MERRY, replacing BILBO arrays with
MERRY arrays and mappings and generally tidying up the structure and
hacking away all the spurious code that had accumulated in some if not
all of the objects.`<br/>`{=html} So with a small semblance of a
plan...off we go...

    * [[ThingandCode][Thing, CodeDB, DataDB and alteration]]

    * [[SkillsDB][The skills databases]]

    * [[UrClasses][The UR Classes]]

-- Main.CarlGriffiths - 03 Sep 2002

Helpfully cleaned up by -- Main.PeterCorless - 21 Apr 2004
