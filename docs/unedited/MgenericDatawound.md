---++ MGeneric:DB:\[theaterID\]:WoundDB - the WoundDB data object

---+++ Properties *allowedlocations:bruise, allowedlocations:wound*
`<br>`{=html} Detail names for permissable wound and bruise locations.

*brawlpercent* `<br>`{=html} Percentage that must be exceeded by the
random call in bruisesub to inflict a bruise.

*bruisetime* `<br>`{=html} Length in seconds that a level of bruise
persists for. Where a 'level' is 0.1 of bruise amount. After this period
the bruise will be reduced by 0.1 until it disappears.

*locationpercentages* `<br>`{=html} Used when a location is not
specified for bruising or wounding. The wound type (kick,punch or wound)
indexes a table of locations against percentage rolls to determine
general body area struck.

*locationpercentages:head, locationpercentages:legs,
locationpercentages:torso* `<br>`{=html} These tables match specific
locations to percentage ranges for determining random hit locations.
Head, legs or torso are determined by the random list defined in
locationpercentages.

------------------------------------------------------------------------

---+++ Methods

*lib:emits:deadly_wound* `<br>`{=html}

*lib:emits_deathwarning* `<br>`{=html}

-- Main.CarlGriffiths - 25 Mar 2004
