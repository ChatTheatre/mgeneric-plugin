---+++ MGeneric Morphing

---++ What is Morphing

Using the 'morph' verb (and given the appropriate skill) you can alter
any of the attributes on yourself or a character in much the same way as
alteration. However, morphing extends the alterable traits beyond
hairstyling and also ignores the needs for skill checks (apart from the
morph skill), ingredients and tools.

---++ How it works

To morph yourself you need morph skill of at least 8. At which point you
can use commands like

morph me "haircoverage bald`<br>`{=html} morph me"jawshape square

For a list of all the morphable attributes and their values simply type

morph me

To see the values of a specific attribute type

morph me "\[attribute\] e.g. morph me"hairstyle

To morph anyone else requires a morphing skill of 10.

---++ The Internals

All the code for morphing now lives in MGeneric:DB:Morph where the main
elements are

    * domorph<br>A simple function that calls morphcode followed by morphemits.<br>
    * morphcode<br>The main morph code the produces a result code.
    * morphemits<br>The code that produces emits to the actor and the environment depending on the result code produced by morphcode.
    * createmorphmapping<br>This function keeps the values used for mapping and those that also belong to alteration (and live in the skilldb's) in sync. It is called each time morphcode is called and updates a mapping in MGeneric:DB:Morph using the values in the skilldb's specified in MGeneric:DB:Morph morphable.
    * morphable<br>A mapping of arrays holding the valid values for each morphable property.

The following are the alterable properties for players in MGeneric:

    * alteration:hair:hairstyle
    * alteration:hair:hairlength
    * alteration:hair:haircolor
    * appearance:hairtype
    * appearance:haircoverage
    * appearance:facialhair
    * alteration:facialhair:faciallength
    * appearance:browshape
    * appearance:appearance:build
    * appearance:cheekshape
    * appearance:earshape
    * appearance:eyebrowshape
    * appearance:eyecolor
    * appearance:eyeshape
    * appearance:hands
    * appearance:height
    * appearance:jawshape
    * appearance:lipshape
    * appearance:mouthshape
    * appearance:noseshape
    * appearance:skin

nb: the properties begining with 'alteration' instead of 'appearance'
are also alterable via skills using the alteration system. These
properties are no longer have an 'appearance' counterpart and morph will
directly alter these properties.

---+++ Pseudo code

Here is the quick pseudo code for how the morphing works:

    * call domorph which calls morphcode followed by morphemits
    * morphcode
        * call createmorphmapping which automatically updates the morphable and morphaltdesc properties on the theatre specific data object from the skilldbs
        * check to see if you have the morphing skill and that you have the required amount of skill if you want to morph someone else (currently you require level 8 skill to morph yourself and level 10 to morph someone else).
        * check to see if the property you are trying to morph is indeed morphable (lookup in the morphable mapping)
        * look for an alternative value for the supplied morph value from morphaltdesc mapping
        * check to see if the requested alteration value is valid (checking the supplied value as existing in the morphable mapping).
        * look up adjectives for the requested alteration value in morphadjectivedetail and apply it to the appropriate detail.
        * set the property identified in morphproperty to the supplied value
        * set the property identified in the morphproperty plus :desc to the alternative value if there is one, otherwise set it to the supplied value
        * do some special case settings for facial hair and hair coverage

---+++ Properties in the datadb

    * *morphable* <br>generated from skilldb exp mappings this mapping contains the valid morphable properties and their valid values.

    * *morphadjectivedetail* <br> mapping that relates details that require adjectives adding to them to the property being altered.

    * *morphaltdesc* <br> mapping updated from skilldbs alternativevalues arrays.

    * *morphproperty* <br> mapping that relates the altered property name to the supplied requested property name e.g. mapping facialhair to alteration:facialhair

-- Main.CarlGriffiths - 08 May 2003
