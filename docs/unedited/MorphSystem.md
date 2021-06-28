WebHome \| TechnicalSummaries

---+ The Morph System

(See also AlterationSystem, PropertyBasedCharacters )

Marrach now has a morph command that lets appropriate players change
every editable aspect of their personal appearance. All of the
appearance properties created for Marrach are available.

To use the system, a player, Guide or Host must have his skill:morpher
property set to 8 (Master level). This allows the player to morph his or
her own appearance. Those with a skill:morpher level of 10 can Morph
other players as well.

To set your skill:morpher property, use: +setprop charname skill:morpher
8

The morph command is very similar to the alteration verbs in Marrach. It
is entirely self-documenting, and tries to catch most likely errors and
suggest proper syntaxes.

To see a list of all supported traits and values, and a sample syntax,
just type morph charname (or morph me if you're altering yourself).

To see a list of only the values supported by a particular trait, type
morph player "trait" (and don't forget the quotes!). Example: morph
hetchel "eyecolor"

To actually change a trait, type morph player "trait value" (with the
quotes). Example: morph hetchel "haircolor red"

------------------------------------------------------------------------

I found out today that it is possible and very easy to add
non-startstory body types to the +morph command. For instance, on
Marrach tonight I added "wrinkled" to the skin types in
Generic:DB:UrPeople:data and then +morphed an NPC to wrinkled and it
worked great. You can add "near-sighted" to eyeshape, etc.

This can be very useful plot device for non-Bilbo Admins.StoryPlotters
-- for instance, you could have a curse instantly make someone old with
a morph command and a good +emit.

Note that with the morph command that the "x morphs y" is emitted to
everyone, and that it requires a near prox for consent otherwise the
approach consent will be asked for, so you should do this OOC in
private, or may have to use a magical NPC and +force. Also, you'll want
to store what their old skin complexion was! Maybe "+setprop playername
appearance:skin:original" to the original skin value.

When you add new items to the Generic:DB:UrPeople:data they will only be
available to people with the morph command -- they will not be available
to people in the start story. You should also let us know if you make
major changes to this list so that we can copy it to other servers.

Obviously, some of these types should be made available as startstory
types. Does anyone want to update the start story list of body types?

BTW, for those of you on the Skotos-Seven server, to be able to morph
anything do a:

    +setprop yourownname skill:morpher 10

Then type "morph me" in the text interface to change all of your body
settings. (BTW, at level 8 you can only do this to yourself, not to
others, which is useful for players in Chat Theatres or NPCs/VPs in
Stages. Trusted Marrach StoryGuides maybe could be given this skill at
10 so that they can fix new players.)

-- Main.ChristopherAllen - 26 Dec 2001

I have added +morph command that can be done from a far prox and is also
private (i.e. it tells you and the target, but doesn't emit to everyone
else, like a whisper.)

+morph now works on Marrach, but you'll need to add two properties on
your body:

`<verbatim>`{=html} +setprop yourname bilbo:inherit:react:+morph
OBJ(Generic:DB:UrPeople:morph) +setprop yourname skill:morpher 10
`</verbatim>`{=html}

You may need to disconnect and reconnect before this inherit property
will work to allow the +morph command to be enabled on your body.

This +morph command does not require consent to do, and will emit only
to you and the person you are morphing. Kargh is thinking about
modifying the script it so that even your target will not know who is
doing the +morphing.

When we next move Generic and Socials folders over to the S7 server,
+morph will work there too.

Also, if someone wants to play around with the morph script, it should
probably save the original user-selected appearance values in a
appearance:\*:original property, mark who did the morphing, and then
maybe a "morph daris 'restore'" option could restore these values.

-- Main.ChristopherAllen - 31 Dec 2001 %META:TOPICMOVED{by="shannon"
date="1009827259" from="Builders.TheMorphSystem"
to="Builders.MorphSystem"}%
