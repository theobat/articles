This is a very simple introduction to FP & mathematical concepts through a translation of several javascript ES6 
idioms in haskell.


Let's say you have an array of menu items composed of routes, pictograms and names.
Depending on your current route some of them have to be shown or not. It's a very usual situation in front end dev.
In javascript it could roughly look like that:
```javascript

const menuElements = [
  {name: 'people', url: '/people', picto: '/pictoPeople.svg'},
  {name: 'pictures', url: '/pictures', picto: '/pictoPictures.svg'},
  {name: 'stuff', url: '/stuff', picto: '/pictoStuff.svg'}
]

// Now the conditional part,
// Let's say we have two route flavors, SIMPLE and FULL, SIMPLE is when we only
// want a subset of the current set of routes (say the first one). 

// first the imperative way to do it
function imperativeMenuElements (routeFlavor) {
  const menuElements = [
    {name: 'people', url: '/people', picto: '/pictoPeople.svg'},
    {name: 'pictures', url: '/pictures', picto: '/pictoPictures.svg'},
    {name: 'stuff', url: '/stuff', picto: '/pictoStuff.svg'}
  ]
  if (routeFlavor === 'SIMPLE') {
    menuElements.splice(1, 2)
  }
}

// And now the "declarative" way of doing it:
const menuElementsThunk = (routeFlavor) => [
  ...(routeFlavor === 'FULL' ? [
    {name: 'people', url: '/people', picto: '/pictoPeople.svg'}
  ] : [])
  {name: 'pictures', url: '/pictures', picto: '/pictoPictures.svg'},
  {name: 'stuff', url: '/stuff', picto: '/pictoStuff.svg'}
]
```

What I find really interesting here, is the ternary operator and the spread.
Because it's following very simple and yet useful algebraic rules, and in fact it could be generalized very thoroughtly.
In haskell the imperative part is feasible but complicated (let's just say I don't want to implement it here).
The declarative part on the other hand is simple:

```haskell
-- For those not used to haskell, it's statically typed, like typescript, you have to define data structures before using them
-- we could use JSON (as it exists as a data structure implemented in a haskell library already) but it's not really idiomatic
-- This is a naive way of doing it in haskell but it's just for the purpose of showing that algebraic structures are useful
-- in day to day life so let's not bother.

data MenuElement = {
  name :: Text
  , url :: Text
  , picto :: Text
}

-- | This is the JS translation not the most efficient/terse/idiomatic haskell implementation
menuElementsThunk :: Text -> [MenuElement]
menuElementsThunk routeFlavor = (if routeFlavor == "FULL" 
  then (return MenuElement {name = "people", url = "/people", picto = "/pictoPeople.svg"})
  else mempty) ++ baseMenuElemList
  where
    baseMenuElemList = [
      MenuElement {name = "pictures", url = "/pictures", picto = "/pictoPictures.svg"},
      MenuElement {name = "stuff", url = "/stuff", picto = "/pictoStuff.svg"}
    ]
```

What's interesting here is that we can show several qualities of the (javascript) list (it's encoded in the lawful typeclass in haskell,
so if you know haskell you already know that). Specifically I used two things that are probably very weird if you don't know haskell;
`return` and `mempty`.

Now let's get out of programming for a second. I'm a french engineer and I studied in what we call the 'classes préparatoires',
what's called algebraic structures in mathematics (or rather Zermalo Fraenkel set theory but whatever, everyone call it mathematics nowadays).
Essentially [groups](https://en.wikipedia.org/wiki/Group_(mathematics)), [rings](https://en.wikipedia.org/wiki/Ring_(mathematics)), and [fields](https://en.wikipedia.org/wiki/Field_(mathematics)).

If you don't know about this, think of it as levels & skills for a character in a Role Playing Game. If you don't know about Role playing games
think of this as some kind typology, where just like plants or species, we like to categorize things and group of things by their behaviors.
Except in mathematics it's not really about categorizing things, but rather about defining things but whatever.

The most elementary algebraic structure is a magma. It's just a Set (a pack of stuff/elements), along with a stable binary operation
(that is, an operation which expects two elements to "operate", that are within the same Set, and it's stable because it gives a single 
element of the same Set in the end). Pretty simple right ?
Let's go back to JS for a down to earth explanation:
```js
// Let's just assume that javascript lists are like mathematical sets, it's not true, but let's pretend.
//
const MySet = [
  
]

```







