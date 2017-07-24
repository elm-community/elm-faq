---
title: Elm FAQ
layout: page
---

**This page is mostly for folks *learning* Elm.** It aggregates questions that are commonly
asked on [the Slack channel][slack], [the IRC channel][irc], or [the mailing list][list].
Those are all friendly and helpful places to go if you do not find the answer to your
question here!

[slack]: http://elmlang.herokuapp.com/
[irc]: http://webchat.freenode.net/?channels=elm
[list]: https://groups.google.com/forum/#!forum/elm-discuss

* Contributions to [this document](https://github.com/elm-community/elm-faq)
  are welcome!

* This document is about the current version of Elm (0.18). See also the
  [Elm 0.17 FAQ](17.html) about upgrading to Elm 0.17. See the [Elm 0.16 FAQ](16.html)
  about that older version of Elm.

#### Contents

<div id="elm-content"></div>


### What is the difference between `type` and `type alias`?

`type` defines and names a new type (which behaves like an enum with data
attached), and `type alias` gives a name to an existing type.

`type alias` isn't creating a distinct type, it is literally just giving a name to an existing type. A type alias will save you keystrokes, but do nothing more. [ref](https://groups.google.com/forum/#!topic/elm-discuss/YgRqI5s2S7Y)


### Why are things called "union types" rather than "algebraic data types"?

The term [union type](https://en.wikipedia.org/wiki/Union_type) means:

> a value that may have any of several representations or formats within the same position in memory.

This is exactly what a union type is in Elm, and the term is used in languages like C, JavaScript, and TypeScript as well. If you want to be extra specific, you can say that Elm has [*tagged* union types](https://en.wikipedia.org/wiki/Tagged_union) so that we can tell the difference between values with case expressions.

Based on experiences teaching Elm to newcomers and discussions like [this](https://groups.google.com/forum/#!msg/elm-discuss/rI_IAf4TiAA/KTvQv1LQ6uAJ), we ended up going with the term union type over "algebraic data type" for this feature. The term "union type" seems to provide more context clues to newcomers, and we have had fewer questions about the feature since the switch.


### Why does elm-repl (or elm-make) report "cannot find module 'Html'"?
You need to install the Html module:

    elm package install elm-lang/html

Several modules are [available by default](http://package.elm-lang.org/packages/elm-lang/core/latest) in the base Elm tools but other common modules like Html have to be installed in the working directory before they can be used in elm-make, elm-repl, and elm-reactor.



### Why does elm-repl report a syntax problem for any type annotation, such as for `fib : Int -> Int`?

elm-repl does not support type annotations at all. If you try you'll see a "syntax problem" message saying something like "I ran into something unexpected when parsing your code!" and pointing to the colon.

To use type annotations you will have to use elm-reactor or [elm-lang.org/try](http://elm-lang.org/try), or build and run a complete app.



### How do I know what package name to use for `elm package install`?
Search on [package.elm-lang.org](http://package.elm-lang.org/) for the module name and use the package name that you find there.



### How can I write debugging information to the console?
Wrap any value with `Debug.log “some message”` and that message and the value will be written to the javascript console every time the value is evaluated. For example:

```haskell
case Debug.log "action" action of
```

If you want to just log a message and value without using that value, try this in a `let` clause:

```haskell
_ = Debug.log "my message" someValue
```

Also see [`Debug.crash`](http://package.elm-lang.org/packages/elm-lang/core/latest/Debug#crash)
which gets special treatment from the compiler to provide additional information in the output.



### How do I install an older version of Elm, 0.16 for example?

    npm install -g elm@0.16

If you need to switch between multiple versions of elm, consider [elmenv](https://github.com/sonnym/elmenv).



### What does `=>` mean?
A common idiom is to define the `(=>)` operator as a synonym for the `(,)` operator that constructs tuples (see just below). This makes a shorthand for long lists of tuple pairs, often used with the Html.style property.  So `["color" => "red", "padding" => "2px"]` means `[("color", "red"), ("padding", "2px")]`.

Note: Operators cannot be defined in elm-repl.


### What does `(,)` mean?

`(,)` is the tuple-forming operator in prefix form[^tupleInfix].
It constructs a 2-tuple from its two arguments. Its type is
`a -> b -> (a, b)`.

There are similar prefix operators for n-tuples. E.g, `(,,)` constructs a 3-tuple and `(,,,)` a 4-tuple.

The following expressions are true:

```haskell
(,) 3 4 == (3, 4)
(,) "foo" 42 == ("foo", 42)
(,,) "won" "too" "tree" == ("won", "too", "tree")
```

[^tupleInfix]: Note that `,` (the single character, not the prefix form) is not really an infix operator by itself. It's used as a syntactic separator when constructing a tuple value like `(1, 2)`. But just `1 , 2` alone is not a valid expression.


### What's the difference between `Html Msg` and `Html msg`?
`msg` is a placeholder used when the HTML doesn't send any messages of type `Msg` (as in `type Msg = ...`). This is just as `a` is used as a placeholder in `List a` when the list is of any type, rather than of strings (`List String`) or integers (`List Int`) etc.. `msg` means "message of any type".



### How can I output literal HTML and avoid escaping of entities?
Use the `innerHTML` property. For example:

```haskell
span [ property "innerHTML" (Json.Encode.string "&copy;") ] []
```



### What does `()` mean?

It is the empty tuple or [unit type](https://en.wikipedia.org/wiki/Unit_type). It serves much like "void", "null", or "None" in other languages.



### What good is the `<|` operator if it is just function application?

It has lower precedence than function application expressed by adjacency (e.g. `sqrt x`) and is right-associative, and so it can be used instead of parentheses to group function arguments. For example, `a b ( c d )` is the same as `a b <| c d`, and `f ( g ( h x ) ) )` can be written as `f <| g <| h x`.  More concretely, `max 3 (sqrt x)` can be written as `max 3 <| sqrt x`. [^application]

[^application]: Function application and the `<|` operator are discussed at some length [here](https://groups.google.com/d/msg/elm-discuss/-PLj_eamKVQ/Zzo7iNx2FgAJ).

Note: The `<|` operator is essentially the same as `$` in Haskell.



### What are the Elm operator precedences and associativities?

See an [Elm operator precedence table](operators.html).
See also [Basics.elm](https://github.com/elm-lang/core/blob/master/src/Basics.elm).



### How can I use multiple Elm programs on the same page?

You can compile multiple modules into a single elm.js and then instantiate whatever module you need on the appropriate div. [^multipleModules]  For example, bundle multiple main programs (without duplicating any code) into a single elm.js like this:

	elm-make Header.elm Footer.elm Login.elm --output=elm.js

and then use them like this:

	var headernode = document.getElementById('header');
	var footernode = document.getElementById('footer');
	var loginnode = document.getElementById('login');

	Elm.Header.embed(headernode);
	Elm.Footer.embed(footernode);
	Elm.Login.embed(loginnode);  

[^multipleModules]: Use of multiple main modules in one application is discussed
    [here](https://groups.google.com/d/msg/elm-discuss/eEJgNnl99ps/keWXnn1KCwAJ)
    and [here](https://www.reddit.com/r/elm/comments/5vh1fi/where_do_you_suffer_most_while_programming_in_elm/deb93ay/).


### Does the main module file have to be named "Main.elm"?

No, that is just a convention. Any module that binds `main` to a value of type `Program Never` can be an entry point to an Elm program.

For example, if both Foo.elm and Bar.elm contain an appropriate binding of `main`,
compiling via "elm-make Foo.elm Bar.elm \-\-output elm.js" creates an elm.js file
such that both `Elm.Foo.embed(someElement)` and
`Elm.Bar.embed(someOtherElement)` can be used from the same HTML file.


### Why doesn't the Elm compiler find the Native code in a module that I cloned from github?

To use native code not installed by `elm-package` you need to add this to your elm-package.json file:

    "native-modules": true,



### Why, when I import a module that defines a type, does the compiler know about the type name but not its constructors?

You need to import the module in one of the following ways:

    import MyModule exposing (MyType(..)) -- exposes all constructors
    import MyModule exposing (MyType(MyConstructor)) -- exposes only MyConstructor

Just exposing `MyType` without the `(..)` will leave the constructors undefined, while exposing `(MyConstructor)` will expose `MyConstructor` but hide `AnotherConstructor`.

Similarly, the module itself must export the constructors.

    module MyModule exposing (MyType(..)) -- exposes all constructors
    module MyModule exposing (MyType(MyConstructor)) -- exposes only MyConstructor

However, there are reasons for [keeping tags and record constructors secret](http://package.elm-lang.org/help/design-guidelines#keep-tags-and-record-constructors-secret).

You can also choose to export a type without exporting any constructors like this (just make sure you expose some other way of creating values of your type):

    module MyModule exposing (MyType, myOtherFunctions)



### Where can I use type annotations?

In addition to the top-level, type annotations can also be applied to `let` expressions.

```haskell
let
  hypotenuse : Float -> Float -> Float
  hypotenuse a b =
    sqrt (a^2 + b^2)
in
  hypotenuse 3 4
```



### How can I join the elmlang.slack.com community?
Sign up at [elmlang.herokuapp.com](http://elmlang.herokuapp.com/).



### How can I recover when elm-make fails with errors like "... openFile: does not exist"?

That can happen when switching between elm versions. Try removing all of elm-stuff or just the build-artifacts:

    rm -r elm-stuff/build-artifacts



### How do I install an Elm package that has not been published to packages.elm-lang.org for use in my project?

Clone the package into a separate directory and add its directory path to the `source-directories` section of the elm-package.json file for your project. As usual, you will also have to install any dependencies of the package. If the package includes any native javascript code you will have to also add `native-modules: true` to elm-package.json. In 0.17 onwards, you will also need to edit the Native files so that any references to `_user$project$module` will be replaced with `_your_user$your_project$module`.



### How can I parse Json into Elm data?

Currently you have to write the [parsing
code](http://package.elm-lang.org/packages/elm-lang/core/latest/Json-Decode).
Other than for [data passed over ports](http://elm-lang.org/guide/interop#customs-and-border-protection) there is no automatic conversion (and even
there, experts recommend writing the parser manually to be able to handle error
cases).

The [json-to-elm](https://github.com/eeue56/json-to-elm) tool is a Python script that generates Elm code to parse a given example of Json. That tool is available online at [json2elm.com](http://json2elm.com).

There are additional Json parsing tools in the [elm-json-extra](http://package.elm-lang.org/packages/circuithub/elm-json-extra/latest/Json-Decode-Extra) package.

The [Elm Json Decode interpreter](http://simonh1000.github.io/decoder/) is an online tool allowing you to experiment with decoders and test them in a simple context.

### How can I pass a record/object over ports?

You have to declare the type of each of its values.

```elm
port outgoing : { floatValue : Float, stringValue : String } -> Cmd msg

port incoming : ({ intValue : Int, stringValue : String } -> msg) -> Sub msg
```

### How can I report a compiler error message that could be better?

Report the problem at the [error-message-catalog issue tracker](https://github.com/elm-lang/error-message-catalog/issues)
with an [short, self-contained, correct, example](http://sscce.org/) showing both the program and the problematic error messages.



### Does Elm have HashMaps?

The core [Dict](http://package.elm-lang.org/packages/elm-lang/core/latest/Dict) package provides a dictionary mapping unique keys to values. There are some restrictions on key value types; in particular, records cannot be keys.


### Why does my app fail immediately with a console error of "Uncaught TypeError: Cannot read property 'appendChild' of null"?

Make sure that you are calling Elm's javascript `embed` function *after* the
referenced container has been defined in the HTML file.

Similarly, Elm's `fullscreen` function should be called only after the page body has started.

Good practice is to call `embed` or `fullscreen` at the end of the document body.


### How can I load CSS (or other resources) in elm-reactor?

It's not easy. Elm-reactor can serve CSS files and so you can write a custom
HTML file that links in your CSS and the elm.js generated by Elm and then load
that HTML file in elm-reactor. But you have to generate the elm.js outside of
elm-reactor; it is not automatically built or rebuilt on changes to the Elm source.

When you click an Elm source file in elm-reactor it compiles just that file (and
any dependencies) and sends back the generated javascript which then runs and
displays the full page view of the program. So to add any CSS you have to do it
in the Elm program itself. Here is an example (thanks to @pdamoc):

```elm
import Html exposing (..)
import Html.Attributes exposing (..)

withStyle html =
  div []
  [ node "style" [type' "text/css"]
    [text "@import url(https://cdnjs.cloudflare.com/ajax/libs/bulma/0.1.2/css/bulma.min.css)"]
  , html
  ]

main =
  div []
    [ a [class "button is-primary"] [text "Primary"]
    , a [class "button is-info"] [text "Info"]
    ]
  |> withStyle

```

For a more comprehensive solution, see the next question.


### How can I integrate Elm development into a larger app with CSS files and other resources?

Elm-reactor is *not* appropriate for that; it's geared toward simple pure-Elm applications.

Consider using other hot-reload tools:
[elm-live](https://github.com/tomekwi/elm-live),
[elm-hot-loader](https://github.com/fluxxu/elm-hot-loader),
[elm-webpack-starter](https://github.com/moarwick/elm-webpack-starter),
[gulp-elm](https://github.com/philopon/gulp-elm/tree/master/example), or
[devd](https://github.com/cortesi/devd).


### How does one render an HTML node conditionally?

Use `Html.text ""` as an empty element. E.g.

```haskell
if someCondition then
    Html.div [] [ {- ... some substantial Html value here ... -} ]
else
    Html.text ""
```

### Does Elm have ad-hoc polymorphism or typeclasses?

No. Elm provides three super-types that work like typeclasses:
`number`, `comparable`, and `appendable`. [^compappend]
The Elm language does not allow for defining our own super-types.

[^compappend]: There is also `compappend` in the compiler but it does not appear to be exposed for use.

The `number` type comprises Int and Float.

The `comparable` type comprises Int, Float, Char, String, List, and tuples[^tuple_compare].

[^tuple_compare]: Tuple comparison is supported only for tuples with 6 or fewer entries.

The `appendable` type comprises String, List, and text[^text_append].

[^text_append]: The compiler error messages refer to "text" being appendable; not sure what "text" means though. [FCY]

When declaring types the names "number", and "comparable" and "appendable" serve as prefixes. For example, a function declared as

```haskell
f : number -> number -> String
```

means that `f` can take two Int values and return a String, or two Float values and return a String. It cannot take a combination of Int and Float though; the actual types have to be consistent.

However, if declared as

```haskell
f : number1 -> number2 -> String
```

then it can also take an Int and a Float and return a String, or take a Float and an Int and return a String. The prefix gives the super-type that constrains the corresponding particular argument. The full super-type name, special prefix plus arbitrary (optional) suffix, determines whether the actual types have to be consistent.

#### Why doesn't Elm have user-defined typeclasses?

So far there have not been enough compelling cases to justify adding user-defined typeclasses to the language.
See [compiler issue #1039](https://github.com/elm-lang/elm-compiler/issues/1039).


### Which special type variables are there, and how do they work?

There are four special type vairables, which are `number`, `comparable`, `appendable`, and `compappend`. Please see question [Does Elm have ad-hoc polymorphism or typeclasses?](#does-elm-have-ad-hoc-polymorphism-or-typeclasses) for the details.

These built-in type variables work differently from the ones defined by user defined. The main difference is these built-in type variables can only mean what it defined to mean. For example:

```haskell
type alias PlusFn t =
  { fn: t -> t -> t
  }
```

It defines a type alias `PlusFn` which has one field `fn` which is a function that accepts two parameters which are of type `t`, and return a value of the same type. So, we can define any function which take two arguments and return one value, as long as they are of the same type, as the value of the `fn` field.

```haskell
> PlusFn (\x y -> x + y)
{ fn = <function> } : Repl.PlusFn number
> PlusFn (\x y -> x ++ y)
{ fn = <function> } : Repl.PlusFn appendable
```

However, if we change the name `t` to any built-in type variable, it will work differently. Let's say we change `t` to `number`, then `fn` must operate on `number`, i.e. either `Int` or `Float`. Since `Int` and `Float` don't support `(++)` operation, so the following code will not be valid any more:

```haskell
> PlusFn (\x y -> x ++ y)
-- TYPE MISMATCH --------------------------------------------- repl-temp-000.elm

The argument to function `PlusFn` is causing a mismatch.

4|   PlusFn (\x y -> x ++ y)
             ^^^^^^^^^^^^^^
Function `PlusFn` is expecting the argument to be:

    number -> number -> number

But it is:

    appendable -> appendable -> appendable
```

### How can I change the value of a field in a record?

Strictly speaking, you can't. Record values (like all Elm values) are
immutable. However, you can *copy* a record value and change one or
more of its field values while doing so. Here is an example in
elm-repl:

```haskell
> x = { name = "Chris", age = 30 }
{ name = "Chris", age = 30 } : { name : String, age : number }
> y = { x | age = x.age + 1 }
{ name = "Chris", age = 31 } : { name : String, age : number }
> y
{ name = "Chris", age = 31 } : { name : String, age : number }
> x
{ name = "Chris", age = 30 } : { name : String, age : number }
```

### How can I change the value of a nested field?

For example, given `model = { foo = { bar = 1 } }`, how does one update the value of the `bar` field?
The expression `{ model | foo.bar = 2 }` will not compile.

Here is an expression that returns a copy of `model` with `model.foo.bar` updated:

`let foo = model.foo in { model | foo = { foo | bar = 2 } }`

Or, perhaps more clearly, like this:

`let fooBefore = model.foo in { model | foo = { fooBefore | bar = 2 } }`

If you need to do this in more than one place, another way is to use a helper function:

```haskell
{ model | foo = updateHelp model.foo 2 }

updateHelp foo int =
  { foo | bar = int }
```

### How can I pattern match a record and its values at the same time?

You can use a special notation in function definition to pattern match a record and its values at the same time:

```haskell
changeFooBar ({foo} as model) =
  { model | foo = { foo | bar = 2 } }
```

See this [post](https://medium.com/elm-shorts/intro-to-records-in-elm-51bc5e933a57)


### Where are the `Cmd` and `Sub` types defined?

They are defined in the core
[Platform.Cmd](http://package.elm-lang.org/packages/elm-lang/core/4.0.0/Platform-Cmd)
and
[Platform.Sub](http://package.elm-lang.org/packages/elm-lang/core/4.0.0/Platform-Sub)
modules.

### What is this `! []` construct in the code?

The `!` operator is defined as a convenience function in the [Platform-Cmd](http://package.elm-lang.org/packages/elm-lang/core/4.0.0/Platform-Cmd#!) module.

Called as `model ! listOfCmds` it returns the `(model, cmd)` value needed as the return value of an `update` function.

### What is the difference between `Cmd Msg` and `Cmd msg`?

`Cmd Msg` is a type of command where the resulting messages are of type `Msg`.

`Cmd msg` is a more general type of command where the resulting message type is not known. It could just as well be written as `Cmd a` or `Cmd x`.

### Why does the compiler complain that "You are declaring port \`someName\` in a normal module."?

Any module using ports must start with `port module` on the first line.

### How do I generate a new message as a command?

The following function constructs a Cmd value from a message:

```haskell
message : msg -> Cmd msg
message msg =
    Task.perform identity (Task.succeed msg)
```

However, this is often unnecessary. To handle a message produced by a call to `update` you may pass it straight back to `update` recursively. The package [ccapndave/elm-update-extra](http://package.elm-lang.org/packages/ccapndave/elm-update-extra/latest) provides helper functions for implementing this approach elegantly.

The latter approach is likely to be more efficient in most cases. The former option may be attractive when recursive calls to `update` could cause an infinite loop, or for authors of reusable components interested in creating a clean encapsulation of their library's internal behavior.

### What is the difference between Cmd and Task?

- `Cmd` is just a bag (i.e. multiset) of chunks of data. It is a functor, but it is not applicative or monadic. This means all you can do is apply a function to all the entries in the bag with `map` and add to the bag with `batch`.

- `Task` is a way doing things in sequence. It is monadic, meaning it has an `andThen` in the API. This means you can say "Do X, and depending on the result, do Y or Z." From there you can keep chaining things.

The point of this bag of commands is that you can gather all the things that need to happen from your whole app and get them done. The point of a task is to describe a particular thing you want to happen.[^cmd-vs-task]

[^cmd-vs-task]: From Evan's #elm-dev Slack posting, 2016-05-16.

If you need to do any kind of chaining stuff, use `Task`, and don't turn your task chain into a `Cmd` until you have no more chaining stuff to do.
This is why APIs generally expose `Task` instances rather than `Cmd`:  so you can do all the chaining you like, and you're in charge of finishing the job and turning whatever chain you've constructed into a `Cmd`.[^task-chaining]

[^task-chaining]: From rtfeldman's #elm-dev Slack posting, 2016-05-16.

### How do I build an `onChange` handler for a `<select>`?

See [Building custom DOM event handlers in Elm](https://robots.thoughtbot.com/building-custom-dom-event-handlers-in-elm) for some examples of event handlers.

### How do I prevent weird DOM behavior (cursor jumps, duplicate input, etc.) for input and textarea fields?

This is a known issue or set of issues, see discussion and workarounds [here](https://github.com/elm-lang/html/issues/105) and [here](https://github.com/elm-lang/html/issues/55).

### What is the recommended way to organize modules, file system structure, etc.?

There is not one definitive way to do it. Generally, a module in Elm is organized around a data structure relevant to the domain rather than abstracted functions (such as putting all your view or update functions together). See [Evan's recommendations here](https://www.reddit.com/r/elm/comments/69hwta/can_i_split_my_code_into_viewmodelupdate_folders/dh6szt9/).

  > I suspect the heart of a great module in Elm is a data structure. A type or type alias that represents some important and coherent concept. From there, you have functions for working with that data structure. A good test for which functions to include might be to ask: does this function make sense if I only read this module?

For a more detailed approach to code organization for Single Page Applications (SPAs), check out [NoRedInk's style guide](https://github.com/NoRedInk/elm-style-guide).

And for a fully worked example of a simple SPA, see [Richard Feldman's Elm implementation](https://github.com/rtfeldman/elm-spa-example) of the ["Realworld" app](https://github.com/gothinkster/realworld) and [his tour through it](https://dev.to/rtfeldman/tour-of-an-open-source-elm-spa).

### What is "elm-community"?

See the [Elm-community Manifesto](https://github.com/elm-community/Manifesto#mandate). The elm-community organization came about when some useful packages were no longer being maintained by their creators. The organization formed to make a new home for those packages. Elm-community packages are not inherently more reliable than others but are perhaps more likely to be supported in the long run. Elm-community packages are not a staging area for core.

### Can Elm be used when developing with SEO in mind?

Most crawlers should work fine with Elm rendered pages. Follow general [recommendations for JavaScript sites](https://plus.google.com/u/0/+JohnMueller/posts/LT4fU7kFB8W) (please note that, even if Googlebot does not support requestAnimationFrame, Elm's fallback handles most cases just fine). If you want to support crawlers that don't support any JS, investigate using [elm-server-side-renderer](https://github.com/eeue56/elm-server-side-renderer).  

### What does the name "Elm" stand for?

It's just a name, not an acronym, and it's not capitalized as "ELM" either.
See this [elm-discuss message](https://groups.google.com/forum/#!msg/elm-discuss/S4zbHJWPXvU/JyavEHDDQucJ) for Evan's explanation of the name.[^elm-name]

[^elm-name]: But I still think it's an anagram of *E*van's *ML*. [FCY]


## Notes
