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
-- in day to day life so let's polish the examples.

data MenuElement = MenuElement {
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

What's interesting here is that we can show several properties of the (javascript) list along with the concatenation operation. Specifically I used two things that are probably very weird if you don't know haskell, but they are the symptoms of things you cannot express in javascript;
`return` and `mempty`. The `return` here is not what you're used to in javascript, it's a function itself, not a _keyword used in functions_.

Now let's get out of programming for a second. I'm a french engineer and I studied in what we call the 'classes préparatoires',
what's called algebraic structures in mathematics (or rather Zermalo Fraenkel set theory but whatever, everyone call it mathematics nowadays).
Essentially [groups](https://en.wikipedia.org/wiki/Group_(mathematics)), [rings](https://en.wikipedia.org/wiki/Ring_(mathematics)), and [fields](https://en.wikipedia.org/wiki/Field_(mathematics)).

So whaat are these "algebraic structures" ? They are notions denoted by words, to begin with: group, ring, field, magma, semi-group, lattice etc. Right, but so is a "knife" or a "cake" so this does not tell us much, so what makes them "algebraic" and what makes them "structures" ?

Well the term "algebra" [comes from an arabic word "al-ğabr" which means balancing](https://en.wikipedia.org/wiki/History_of_algebra#Etymology) and originally it's all about resolving "polynomial" equations, which are all the equations composed of the elementary operations (+, -, x, /) with only one variable. Also they are structures because they "dictate" what you can do with them, they follow the set of rules bound to their name. A group only has one operation, and this operation is associative, has a neutral element and every element has an inverse.
What's important to understand here if you've never been exposed to this kind of things is that, there's no such thing as 1+1=2, out of the blue, unless you have defined what is 1, what is 2 and what's `+` and `=`. In a really formal way, the basic 1+1=2 should be written as:
```
// This is incomplete, we don't know what N is ...
we define
+ : N x N -> N
(n, m) -> n + m
∃ (1, 2) ∈ N,
1+1=2
```
Which essentially reads as _" We define an operation between the product (of set) of naturals to the naturals and there exists 1, 2 belonging to the naturals and there's an operation called addition where 1+1=2"._ At least in one formalization of mathematics that's how you should write your equation (there are other ways to express the same ideas). I won't dwelve into what's a mathematical set for now, because it's pretty complicated, let's just assume it's a container of elements and it defines only one "relation" in terms of (first order) logic: ``` myElement ∈ MySet ```. 

The most elementary algebraic structure is a magma. It's just a Set (a pack of stuff/elements), along with a stable binary operation. That is, an operation which expects two elements to "operate", that are within the same Set, and it's stable because it gives a single element of the same Set in the end). Pretty simple right ?
Let's go back to JS for a down to earth explanation:

```js
// Let's just assume that javascript lists are like mathematical sets, it's not true, but let's pretend.
//
const MyFullSet = [
  [], ['a'], ['b'], ['c'], ['a', 'b'], ['a', 'c'], ['a', 'b', 'c']
]
const anElement = ['a', 'b']

// Now let's examine if the concatenation is indeed forming a magma with the set MySet:
anElement.concat(['c'])
console.log(anElement)
// ['a', 'b', 'c']
// Well it seems stable enough to me !
// Of course if you take anything that's *not* in MyFullSet to do yuor concatenation you diverge,
// but remember the operation has to start from your MyFullSet to comply with the rules of the Magma.
// Here's something a little bit harder to explain:
['a', 'b', 'c'].concat(['a'])
// ['a', 'b', 'c', 'a']
// Hmm this is not stable, have you seen a, b, c, a in the original MyFullSet ? hmm neither did I.

// Let's assume concat is comparing elements of the list before adding them to the list, and does not add the element in case it's already there (it's actually the behavior of ```new Set([])``` in ES6, but it's less readable, so let's assume [].concat does the job).
// Then:
['a', 'b', 'c'].concat(['a']) === ['a', 'b', 'c']
// nice, it seems to work now
// There's another problem though, the elements in a list are ordered, which we've never heard of so far in our "Sets":
['b', 'c'].concat(['a']) === ['b', 'c', 'a']
// Same as above, let's assume the order of elements is irrelevant and thus:
['b', 'c', 'a'] === ['a', 'b', 'c']

```
So fine we (potentially) have a magma with MyFullSet and the concatenation-comparing thing.
Let's see what we can reach next, a `Monoid` is a `Magma` where you enforce associativity of your binary operation and the existence of a neutral element. Hmmm anyone sees a neutral element in there ? We have a few candidates. But let's reach for associativity first:

```
// we want to prove that for three variables var1, var2, var3  taking their values in MyFullSet, whatever this value is:
(var1.concat(var2)).concat(var3) === var1.concat(var2.concat(var3))
// I won't do the proof here, just assume it's true, but it seems reasonable
```
Now what about the neutral element, what we try to prove then is this:
```
// we want to prove that for all any variable var1 taking a value in MyFullSet, there exists a value (let's call it neutralElement) in MyFullSet such that:
var1.concat(neutralElement) === var1
```
So this is true for a few equations, take that for instance:
```
['a', 'b', 'c'].concat(['a']) === ['a', 'b', 'c']
// good !! 
// but:
['b', 'c'].concat(['a']) === ['a', 'b', 'c'] // remember we assumed the order of elements is irrelevant
// obviously the ['a'] thing does not work here, ['b', 'c'] !== ['a', 'b', 'c']

// But wait there is the empty list ! 
['a', 'b', 'c'].concat([]) === ['a', 'b', 'c']
// indeed this is the one, you can check for all elements that the concatenation is always the identity function
// for all the elements of MyFullSet (and even more than that as far as JS is concerned, but remember, you only get to use what you define in math).
```

Right so, my concat redefined to compare elements before doing its job along with the non ordered lists form a `Monoid`.
My point here is that, many many "containers" along with an operation actually constitute an algebraic structure, and it's fairly easy to reason about them in haskell, it's more complicated in JS because it lacks the native form of many data structures, but it still is useful in JS.







