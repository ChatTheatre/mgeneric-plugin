---++ MGeneric - Data objects

MGeneric now consists of two sets of objects. Those living under
MGeneric:DB:\* contain the code that makes up the MGeneric systems.
Those that exist under MGeneric:Data:\* contain theater specific data
that allows individual games to tailor the functionality provided by the
MGeneric systems.

---+++ Data object naming

Data objects are named MGeneric:Data:\[theaterID\]:\[dbname\].

i.e. MGeneric:Data:Marrach:Death or MGeneric:Data:Oasis:Death

If a theater specific data object is not present the MGeneric code will
use MGeneric:Data:default:\* instead.

---+++ The Data objects

Currently the data objects are:

    * [[MgenericDatadeath][Death]]
    * [[MgenericDatawound][WoundDB]]
    * [[MgenericDatahealing][Healing]]
    * [[MgenericDatamorph][Morph]]
    * [[MgenericDatadatadb][DataDB]]

-- Main.CarlGriffiths - 25 Feb 2004
