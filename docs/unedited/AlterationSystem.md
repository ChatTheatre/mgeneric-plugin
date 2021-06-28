%META:TOPICINFO{author=\"ChristopherAllen\" date=\"1046211678\"
format=\"1.0\" version=\"1.4\"}% WebHome \| TechnicalSummaries

# The Marrach Alteration System

(See also MorphSystem )

Castle Marrach contains a simple system for changing the adjectives
associated with objects.

Objects to be altered must be duplicated from one of the following
Generic directories of the Tree:

\* Generic:clothing \* Generic:weapons \* Generic:literature \*
Generic:jewelry \* Generic:misc

The catalogs available in Generic:literature all provide suitably
Generic objects.

Adjectives are divided into classes called **traits**. The following
traits are currently supported:

\* Color (clothing, books, weapon hilts) \* Material (clothing) \* Fit
(clothing) \* Design (weapons, jewelry) \* Metal (weapons, jewelry) \*
Stone (jewelry, weapon pommels) \* Shape (jewelry) \* Binding (books) \*
Symbol (clothing, weapons, jewelry, books, player tattoos) \* Haircolor
(players) \* Hairlength (players) \* Hairstyle (players) \* Hairtype
(players)

Each trait can have one of several possible **values**. For example, the
\"Color\" trait can have values like \"red,\" \"green\" and \"purple\".

For a complete listings of traits and values, plus the skills required
to change them, see the in-game instruction books in Generic:literature,
Marrach.SkillBooks and/or Marrach.PgUpdateAlteration.

\<h3\>Adding New Trait Values\</h3\>

Adding a new trait is complicated. However, adding a new value to an
existing trait is fairly simple. Here\'s an example of how to do it:

Suppose somebody in the Castle is starting a Marsupial Appreciation
League and wants all of the members\' clothing to be embroidered with a
platypus.

Embroidery uses the Symbol trait, so open the Tree directory Generic:DB
and find the object called SkillSymbolDB. (For the Color trait, you\'d
go to SkillColorDB; for Metal, SkillMetalDB \... you get the idea).

Open SkillSymbolDB and look at the scary-looking list of initial
properties near the bottom. These properties control which values the
Symbol Trait is allowed to have.

Before proceding, you need to decide how much Embroidering skill will
required to sew a platypus. Skill levels are numbered from 1 to 10:

1 Novice 2 Junior Apprentice 3 Senior Apprentice 4 Fellowcraft 5
Journeyman 6 Artisan 7 Senior Artisan 8 Master 9 Master Adept 10 Grand
Master

Let\'s say you\'ve decided on Senior Apprentice, which is skill level 3.

Scroll down the list of initial properties and find the one named
\#list\[*n*\], where *n* is the skill level you\'re editing. In our
example, you\'d be looking for the property \#level\[3\]. This property
is simply a text list of the Symbol values available to a 3rd-level
Embroiderer. It might look like this:

    Base:InitialProperty    [   property='#list[3]' ] E X D
        flower dove owl duck fish hare ermine cat rat boat kite chair plume

Using the \<u\>E\</u\> tag, edit the \#level\[3\] property and add your
new symbol to the list. (For consistency and neatness, please make sure
there is exactly one space between the last list entry and your new
entry.) Save your change and the property should look like this:

    Base:InitialProperty    [   property='#list[3]' ] E X D
        flower dove owl duck fish hare ermine cat rat boat kite chair plume platypus

Now scroll through the property list until you find one called
\#cnt\[*n*\], with *n* again being the skill level of interest. This
property is simply a number that tells the system how many values there
are in the associated \#list\[*n*\]. In our example, you\'d be editing
the \#cnt\[3\] property, which would look like this:

    Base:InitialProperty    [   property='#cnt[3]'  ] E X D
        13

You\'ve added one new value to the \#list\[3\] property (platypus), so
edit the \#cnt\[\] property by adding 1:

    Base:InitialProperty    [   property='#cnt[3]'  ] E X D
        14

Finally, the system needs a quick way to look up the skill level
associated with each value. Scroll around and you\'ll notice a bunch of
properties called \#exp\[*name*\], with a different *name* for each
possible Symbol value. The value of these properties corresponds to
their skill level.

You need to add a \#exp\[\] property for each new value you want to add.
So create a new initial property by clicking on the \<u\>N\</u\> tag at
the top of the Initial Properties list:

    Base:InitialProperties E X N

Edit your new property, changing its name to \#exp\[platypus\] and its
value to 3. When you\'re done, you should have a property in the list
that looks like this:

    Base:InitialProperty    [   property='#exp[platypus]'   ] E X D 
        3

The Alteration System will now recognize \"platypus\" as a legitimate
Symbol value for Embroiderers of skill level 3 and above. That\'s it.
You\'re done!

\... except that all of the Library books, Twiki pages and other
scattered bits of documentation related to Symbols need to be updated to
include your new value. This is actually more work then changing the
symbol. :)

Start with the Library books. Go to the Library directory on the Tree
and edit the Embroiding, Embossing, Engraving and Tattooing books to
include your new platypus symbol. (These four skills all use the same
list of Symbols.) Do the same for the relevant Twiki pages, etc.

Have fun!

\- BM

%META:TOPICMOVED{by=\"shannon\" date=\"1009827196\"
from=\"Marrach.AlterationSystem\" to=\"Builders.AlterationSystem\"}%
