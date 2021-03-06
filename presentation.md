# Improving Your Code With Union Types

^ Welcome everyone. Today we're talking about improving code with union types

^ Don't know what that is, that's OK

^ Before we explain, address the elephant in the room

---

## What is "good code"?

^ Discussions on "good" code are controversial

---

![fit](images/your-opinion.jpg)

---

## Commonly accepted attributes

* Expressive
* Easier to change
* Easier to read
* More modular
* More correct

^ Expressive easy to translate real world ideas into code

^ Others...

^ These all sound great. How can Union Types help us?

---

![fit](images/js-to-elm.jpg)

^ Like me, most look at syntax and primitives

^ Just like my previous programming language

^ Elm has these really cool high-level constructs

---

## Terminology

![](images/terminology.png)

^ First we need to define some terminology

^ There are a lot of names for these things

---

## Sum types (AKA Enums)

```elm
type Suit
  = Hearts
  | Spades
  | Diamonds
  | Clubs
```

^ Sum types are a list of alternative values

^ Many languages call these Enums

^ Convenient because it allows us to specify what all the possible values are

^ Quick trick for remembering the name "Sum type"

^ How many suit types are there?

---

## 4 different suits

^ The number of possible values in a "sum type" is the sum of the definitions

---

## Product types (AKA Tagged values)

```elm
type Card = Card Value Suit
```

^ How many cards are possible?

---

## 13 values x 4 Suits = 52

^ That's why it's called a product type

^ What's missing?

---

![fit](images/joker.png)

---

## Combining them together

```elm
type Coloring = Colored | Grayscale

type Card
  = Card Value Suit
  | Joker Coloring
```

^ Called "Union Type" or "Algebraic Data Type" or "Tagged Union"

^ Now how many cards?

---

## (13 x 4) + 2 = 54

---

## Type variables

```elm
type Maybe a
  = Nothing
  | Just a
```

^ You can take it one level crazier and add variables

---

## What are they good for?

^ Dry stuff

^ What's it good for?

---

## Let's talk about Booleans

![](images/true-false.png)

---

## Three-State Boolean problem

```elm
type alias Order =
  { id : Int
  , delivery : Maybe Bool
  }
```

^ Anyone familiar with the three-state boolean problem?

^ Online restaurant checkout system

^ Delivery vs Takeout vs no decision

^ Maybe is a bit of a smell

---

## Three states

1. `Just True`
2. `Just False`
3. `Nothing`

^ This isn't really a boolean. This has three states

---

## Sum type

```elm
type Delivery = Delivery | Pickup | NoneSelected

type alias Order =
  { id : Int
  , delivery : Delivery
  }
```

---

## Improvements

* :white_check_mark: Easier to change
* :white_check_mark: Easier to read
* :x: More modular
* :x: More correct

---

![fit](images/double-trouble.jpg)

^ One boolean's easy

---

## Double Dependant Boolean

```elm
type alias Order =
  { id : Int
  , delivery : Bool
  , thirdParty : Bool
  }
```

^ New feature, add third-party delivery

---

## Four states

* True True
* True False
* False True
* False False

^ Some don't really make sense

---

## What does this even mean

```elm
{ id = 1
, delivery = False
, thirdParty = True
}
```

^ Third party takeout?

---

## Sum type

```elm
type Delivery
  = Delivery
  | Pickup
  | ThirdPartyDelivery
  | NotSelected

type alias Order =
  { id : Int
  , delivery : Delivery
  }
```

^ DDB is clunky way of getting 4 states

^ Explicit 4 states more readable

---

## Improvements

* :white_check_mark: Easier to change
* :white_check_mark: Easier to read
* :x: More modular
* :x: More correct

---

## Boolean Flags

![](images/flags.jpg)

^ More booleans

^ Restaurants are boring. Let's build a video game

---

## Less readable

```elm
npc : Character
npc =
  Character True True False
```

^ Any guesses about this?

---

## More readable

```elm
npc : Character
npc =
  Character Immortal Female Stationary
```

^ What about this

---

## Looks like

```elm
type Mortality = Mortal | Immortal
type Gender = Male | Female
type Mobility = Stationary | Mobile

type alias Character =
  { mortality : Mortality
  , gender : Gender
  , mobility : Mobility
  }
```

^ Full definitions

---

## More extensible

```elm
type Gender = Female | Male | NonBinary
```

---

## Improvements

* :white_check_mark: Easier to change
* :white_check_mark: Easier to read
* :x: More modular
* :x: More correct

---

## Why not Strings?

![](images/strings.jpg)

^ All along you've been thinking...

^ Let's take a look

---

## How Many Mobilities?

```elm
type Mobility = Mobile | Stationary
```

^ Exactly two mobilities

^ Remember Sum type

---

## How Many Mobilities?

```elm
type alias Mobility = String
```

^ Infinitely many mobilities

^ What difference does that make?

---

## Case statements

```elm
move : Int -> Character -> Character
move distance character =
  case character.mobility of
    "Mobile" ->
      { character | position = character.position + distance }

    "Stationary" ->
      character
    _ ->
      character
```

^ Strings need catch-all

^ Adding a new Flying mobility easy not to add a case

---

## Sum type gives error

```
-- MISSING PATTERNS ------------------------------------------------------------

This `case` does not have branches for all possibilities.

11|>    case character.mobility of                                                         
12|>      Mobile ->                                                                      
13|>        { character | position = character.position + distance }                       
14|>                                                                                       
15|>      Stationary ->                                                                  
16|>        character

You need to account for the following values:

    Flying
```

^ ADT gives us a actual compiler error

---

## Typos

```elm
npc : Character
npc =
  Character "Mobil" 55
```

^ Again, valid because of infinite valid values

---

## Sum type gives error

```
-- NAMING ERROR ----------------------------------------------------------------

Cannot find variable `Mobil`

20|   Character Mobil 55
                ^^^^^
Maybe you want one of the following?

    Mobile
```

^ ADT gives us compiler error

---

## Improvements

* :white_check_mark: Easier to change
* :white_check_mark: Easier to read
* :x: More modular
* :white_check_mark: More correct

---

## Primitive Obsession

![](images/caveman.jpg)

^ Concept in many languages

^ Use tools to build more powerful abstractions

^ Don't always go for the lowest common denominator

---

![](images/safety.jpg)

---

## Dangerous primitives

```elm
type alias User =
  { name : String
  , age : Int
  , bankBalance : Int
  , salary : Int
  }
```

---

## Working with Cash

```elm
addBalance : User -> Int -> User
addBalance user amount =
  { user | bankBalance = bankBalance + amount }
```

---

## Invalid operation

```elm
payday : User -> User
payday user =
  addBalance user user.age
```

---

## Custom type

```elm
type Dollar = Dollar Int

type alias User =
  { name : String
  , age : Int
  , bankBalance : Dollar
  , salary : Dollar
  }
```

---

## Using Dollar

```elm
addBalance : User -> Dollar -> User
addBalance user dollarAmount =
  let
      (Dollar balance) = user.bankBalance
      (Dollar amount) = dollarAmount
  in
      { user | bankBalance = Dollar (balance + amount) }
```

---

## Invalid operation

```elm
payday : User -> User
payday user =
  addBalance user user.age
```
---

## Compiler error

```
-- TYPE MISMATCH ---------------------------------------------------------------

The 2nd argument to function `addBalance` is causing a mismatch.

21|     addBalance user user.age
                        ^^^^^^^^
Function `addBalance` is expecting the 2nd argument to be:

    Dollar

But it is:

    Int
```

---

![](images/pain-points.jpg)

---

## Pain points

1. Unwrap value
2. Add the integers
3. Re-wrap value

---

## Sound familiar?

---

## Mapping

![](images/map.jpg)

1. Unwrap value
2. Apply function
3. Re-wrap value

---

## Mapping Maybe

```elm
doubleMaybe : Maybe Int -> Maybe Int
doubleMaybe maybe =
  Maybe.map (\n -> n * 2 ) maybe

```

---

## Summing two Maybes

```elm
sumMaybe : Maybe Int -> Maybe Int -> Maybe Int
sumMaybe m1 m2 =
  Maybe.map2 (+) m1 m2
```

---

## We need that

If only there was a `Dollar.map2`

---

## Mapping to the rescue

```elm
module Dollar exposing (Dollar, map2)

map2 : (Int -> Int) -> Dollar -> Dollar -> Dollar
map2 function (Dollar d1) (Dollar d2) =
  Dollar (function d1 d2)
```

---

## Using Dollar

```elm
addBalance : User -> Dollar -> Dollar
addBalance user amount =
  { user | bankBalance = Dollar.map2 (+) user.bankBalance amount }
```

---

## Getting clever


```elm
module Dollar exposing (Dollar, map2)

sum : Dollar -> Dollar -> Dollar
sum =
  map2 (+)
```

---

## General utility

* `map`
* `map2`
* `sum`
* `subtract`
* `multiply`
* `divide`

---

## Improvements

* :white_check_mark: Easier to change
* :white_check_mark: Easier to read
* :x: More modular
* :white_check_mark: More correct

---

## Opaque types

---

## Start with this

```elm
module Point exposing (Point)

type alias Point = { x : Int, y : Int }
```

---

## Use in in Code

```elm
path : List Point
path =
  [ Point 1 1,
  , Point 100 100
  ]
```

---

## Now you want to add 3D

```elm
module Point exposing (Point)

type alias Point = { x : Int, y : Int, z : Int}
```

---

## Broken code

---

## Wrapping in a type

```elm
module Point exposing (Point, fromXY)

type Point = Point { x : Int, y : Int }

fromXY : (Int, Int) -> Point
fromXY (x, y) =
  Point { x = x, y = y }
```

---

## Adding 3D

```elm
module Point exposing (Point, fromXY)

type Point = Point { x : Int, y : Int, z : Int }

fromXY : (Int, Int) -> Point
fromXY (x, y) =
  Point { x = x, y = y, z = 0 }
```

---

## Improvements

* :white_check_mark: Easier to change
* :white_check_mark: Easier to read
* :white_check_mark: More modular
* :x: More correct

---

## The shape of data

![fit](images/shapes.png)

---

## Primitives

```elm
type alias Card =
  { suit : String
  , value : Int
  }
```

---

## Looks like

```elm
Card "Hearts" 2
```

---

## How Many of this type of card?

---

## This is valid

```elm
Card "Starfish" 1999
```

---

## How do you represent the Joker?

![fit](images/joker.png)

---

## You have to force it

```elm
Card "Joker" 0
```

or

```elm
Card "Joker" Nothing
```

---

## Union types allow multiple shapes

```elm
type Card
  = Card Value Suit
  | Joker Coloring
```

---

## Improvements

* :white_check_mark: Easier to change
* :white_check_mark: Easier to read
* :x: More modular
* :white_check_mark: More correct
* :white_check_mark: More _expressive_

---

## HTTP

---

## Maybe

```elm
type alias Model =
  { lastOrder : Maybe Order
  }
```

---

## Explore the shape of data

```elm
type RemoteData e a
    = NotAsked
    | Loading
    | Failure e
    | Success a
```

---

## Improved Model

```elm
type alias Model =
  { lastOrder : RemoteData Http.Error Order
  }
```

---

## Improvements

* :white_check_mark: Easier to change
* :white_check_mark: Easier to read
* :x: More modular
* :x: More correct
* :white_check_mark: More _expressive_

---

## Improve your code with Union Types

---

## Questions?

---

## Slides are on GitHub

https://github.com/JoelQ/adts-elm-meetup

---

## About Me

![fit](images/ralph.jpg)

* Developer at thoughtbot
* @joelquen on Twitter
* @JoelQ on GitHub
