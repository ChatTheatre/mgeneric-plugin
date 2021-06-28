---++ MGeneric:DB:\[theaterID\]:Death - the Death data object

---+++ Properties

*donotmute* `<br>`{=html} If this property is defined on the data object
with any value then dead players will not be muted.

*deathroom* `<br>`{=html} String woe name of room to which dead people
go to await being returned to their rooms.

*deathwaitperiod* `<br>`{=html} The time in seconds that a dead player
must wait in the deathroom before being returned to their room. NB. This
time only starts once their corpse is retrieved (if corpses are used).

*removebodyfinaltime* `<br>`{=html} The time in seconds before a corpse
will disappear on its own. i.e. not retrieved by Lith or spotted by a
servant.

*remoebodyservanttime* `<br>`{=html} The time in seconds before servants
will remove bodies.

*startclothes* `<br>`{=html} The string woe name of clothing that
players should be clothed in once they are dead. This is ignored if the
players retain their own clothes.

*wakeuproom* `<br>`{=html} Globally defined string woename for room
which resurrected character will be returned to after death.

*death:wakeuproom* `<br>`{=html} The string woename defined on the
player body of the room in which the player will awaken after returning
from death if this is not defined then the player will be returned to
virtualhome:home. If this does not exist the player will be returned to
the wakeuproom specified on the datadb for their theater.

*nobody* `<br>`{=html} If this property is defined then no body created
on death

*\$nobody* `<br>`{=html} A parameter passed to death code to prevent
corpse to be spawned.

*death:nobody* `<br>`{=html} This property defined on the player body
will prevent a corpse being spawned when they die.

*leaveclothingonbody* `<br>`{=html} If this property is defined with any
value on the data db all players in the game will retain their clothing
when they die.

*death:leaveclothingonbody* `<br>`{=html} If this property is defined on
either a garment it will stay on the player when they die. If it is
defined on the player object all of their clothes will remain with them
when they die.

*leaveclothinginenv* `<br>`{=html} If this property is defined with any
value on the data db all clothing on players will be placed in their
environment when they die.

*death:leaveclothinginenv* `<br>`{=html} If this property is defined on
a garment it will be placed in the players environment when they die. If
it is defined on the player object all of their clothes will be placed
in their environment when they die.

*leaveholding* `<br>`{=html} If this property is defined with any value
on the data db all items in a players inventory will remain with them
when they die.

*death:leaveholding* `<br>`{=html} If this property is defined on a item
it will remain with the player when they die. If it is defined on the
player object their items will remain with them when they die.

*leaveinenv* `<br>`{=html} If this property is defined with any value on
the data db all items in a players inventory will be placed in their
environment when they die.

*death:leaveinenv* `<br>`{=html} If this property is defined on a item
it will be placed in the players environment when they die. If it is
defined on the player object all of their items will be placed in their
environment when they die.

*deathperm* `<br>`{=html} If this property is defined with any value on
the data object death is permanent for all players and no-one will be
resurrected. Dead players will remain forever in the deathroom.

*death:deathperm* `<br>`{=html} If this property is defined with any
value on the player object death is permanent for that player and they
will not be resurrected. Dead players will remain forever in the
deathroom.

*noskillreduce* `<br>`{=html} If this property holds any value on the
data object then the skill reduction code will not be run globally
against any dead bodies.

*death:noskillreduce* `<br>`{=html} If this property is defined on a
character then that dead person will not suffer a reduction in skills
when dying.

------------------------------------------------------------------------

---+++ Methods

*dieemitsfirst* `<br>`{=html} First person emit for 'you are dead'.

*dieemitsthird* `<br>`{=html} Third person emit for 'the person is
dead'.

*deathEmitsDeathPerm* `<br>`{=html} emit on permanent death

*bodyEmitsDeadAndWaiting* `<br>`{=html} ooc emit to player when they are
in limbo waiting for their corpse to be picked up by lith

*bodyEmitsVanish* `<br>`{=html} emit into environment when corpse fades
away

*deathemitsleavebelongingsinroom* `<br>`{=html} emit to player when Lith
leaves their belongings in their room

*deathemitsleavepersoninroom* `<br>`{=html} Emit to player and env when
lith leaves body in room after resurrection.

*deathemitstakepersontoroom* `<br>`{=html} Emit to player and env for
lith collecting bodies and taking them away.

-- Main.CarlGriffiths - 25 Feb 2004
