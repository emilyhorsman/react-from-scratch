# Part 0: Introduction

Feel free to [skip down straight into the code](#lets-go).

## Intro

### Motivation

I write a lot of [React](https://facebook.github.io/react/index.html) these days.
Almost all applications I write involving React include surrounding JavaScript ecosystem: webpack, babel, redux, etc.
Many curious individuals I talk to have been led to believe that one should learn all of this surrounding ecosystem at the same time.
They’re curious about React, but all the extra things are overwhelming.
Well-written React also utilizes many patterns that not everyone may be familiar with — less imperative and more functional ways of writing code.
I’ll get into what that means later.

### Intent

I want to write a series of articles that teach React with the simplest principles first.
Then I’ll sprinkle in things as we go, and hopefully enlighten the reader on the motivations for using all of this.
This first article is going to include very little React, actually.
I’m going to write a very small application without any frameworks, transpilers, or libraries.
Then we’re going to look at how to write the same thing using React. Without any of the fancier stuff.

### Disclaimer

React is not the right choice for everything.
This example in Part 0 will not be a good usage of React.
React’s lack of opinions on various concerns in your application, and its [minimal API surface](https://www.youtube.com/watch?v=4anAwXYqLG8) do make it a good choice in many applications though!
Tessa Thornton ([@tessthornton](https://medium.com/shopify-ux/how-to-learn-web-frameworks-9d447cb71e68#.7c6loosm2)) has an excellent—and very relevant—article on learning web frameworks.

I once read a tweet that went something like this:

>The hardest part of being a React instructor is convincing all my students they don’t need to re-write everything in React.

(If anyone knows the attribution for this tweet, please send it my way and I will include it here.)

### Who is this for?

Everyone!
Even experienced developers can get lost in all the JavaScript ecosystem.
I aim to introduce ES6+ concepts and tools like webpack at The Right Time™.
Individuals who are new to programming may want to go check out some [JavaScript tutorials](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Introduction) beforehand.
That said, I intend to link to good resources on trickier concepts as we go.

### Why not the official React documentation?

Actually, you should check out the [official docs](https://facebook.github.io/react/docs/getting-started.html)!
Particularly, [Thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html).
(You don’t need to for this article though).
I’d like to cover more than the official docs though, and I’d also like to avoid some [ES5](http://benmccormick.org/2015/09/14/es5-es6-es2016-es-next-whats-going-on-with-javascript-versioning/) concepts, such as `React.createClass` found in their docs.
Eventually, this article series should get into specific ES6+ examples, and tools like webpack.

## Let’s Go

We’re going to build this:

<p data-height="265" data-theme-id="light" data-slug-hash="jrVeRp" data-default-tab="result" data-user="emilyhorsman" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/emilyhorsman/pen/jrVeRp/">React from Scratch Part 0</a> by Emily Horsman (<a href="http://codepen.io/emilyhorsman">@emilyhorsman</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

It gets some data from the [GitHub API](https://developer.github.com/v3/) and displays it in a list.
It doesn’t update or do anything fancy.

[This](https://api.github.com/users/emilyhorsman/events) is the data we get back from the GitHub API.

### Without React

This first example is all [on CodePen](http://codepen.io/emilyhorsman/pen/wWoExK). Feel free to go play with it.

Let’s get some quick stuff out of the way first.
The following is the only markup (HTML) in the application.
It’s just a `<div>` to contain everything, and an unordered list that JavaScript can insert things into.
The `<ul>` has an `id` that our JavaScript will use to know where to put events into.

```html
<div class="container-fluid m-t-1">
    <ul id="app" class="media-list">
    </ul>
</div>
```

#### Fetching Data

GitHub provides us an API that gives back some JSON.
This all works over HTTP.
We need a function that fetches some data from an API.
We’re only going to make a single request for this example, but let’s right something to take care of that for the whole article.

```js
function http(method, url, onSuccess) {
    var request = new XMLHttpRequest()
    request.open(method, url)
    request.onload = function() {
        if (request.status === 200) {
            onSuccess(JSON.parse(request.responseText))
        }
    }

    request.send()
}
```

This can be called with something like `http('GET', 'http://google.com/', handleSuccess)`.

>🤔
>Hey!
>This method roughly demonstrates the level of JavaScript knowledge this article operates at.
>It includes some concepts such as callbacks (JavaScript’s notion of “higher-order functions”), performing an XMLHttpRequest (Ajax), and parsing some JSON received from an HTTP request.
>I strongly recommend resources such as [JavaScript Garden](http://bonsaiden.github.io/JavaScript-Garden/) and the [MDN wiki on JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript).
>I don’t want to explain too many of these concepts in this article, as others have done a better job than I can.

We’re also going to write a quick helper method that takes a username on GitHub and returns the API URL.

```js
function getEventsUrl(user) {
    return 'https://api.github.com/users/' + user + '/events'
}
```

Now let’s call it and get this out of the way now.

```js
var container = document.getElementById('app')
http('GET', getEventsUrl('emilyhorsman'), renderEvents.bind(null, container))
```

Notice we’re already getting the `<ul>` element we had earlier.
We’re also telling the `http` method to give our data to this `renderEvents` function.
We haven’t written this function yet, it’s going to take a container element in the DOM and put some stuff in it—our list of events from the GitHub API.
(
Unsure what the DOM is? Check out [this CSS tricks article](https://css-tricks.com/dom/).
Essentially, it stands for Document Object Model and it’s what the browser uses to represent all the elements on our page.
That `<ul>` tag we wrote becomes an unordered list Element in the DOM.
)
You may also notice the `bind` call. If this is new to you, check out the [MDN page](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind) on it—particularly, the examples.
Essentially, when the `renderEvents` function is eventually called (when we get data back from the API), it will receive the `container` reference as an argument.

#### Rendering Stuff

The `renderEvents` function needs to take all the event objects we’re receiving from GitHub and insert a list of these things into the `container`:

![Render outlines](images/Part0Outlines.png)

The final product won’t have all the borders I added here.
I’ve done this to demonstrate that a single list item is split up into a bunch of pieces.
The list item representing the event is one big piece itself, and it contains an avatar piece, a heading piece, and a body piece.

>Note!
>[Bootstrap](http://v4-alpha.getbootstrap.com/) is included in the CodePen.
>We’re just using the [media object](http://v4-alpha.getbootstrap.com/layout/media-object/) to represent an event, a common pattern in web design.
>It consists of `media-left`, `media-body`, and `media-heading` classes.

Each media item is represented in the browser’s DOM as a hierarchy of [`Element`](https://developer.mozilla.org/en-US/docs/Web/API/Element) objects.
These objects each have their own _children_.

A single event item ends up having a representation that looks something like the following.

```
HTMLLIElement
├──HTMLDivElement
│  └──HTMLImageElement
└──HTMLDivElement
   ├──HTMLSpanElement
   │  └──Text
   └──Text
```

This is how our browser represents a document (a webpage) in the DOM.
We typically write our documents in markup, like `<li><div><img src="foo.jpg"></div></li>`.
This ends up being converted into a structure like above though, as the browser compiles markup into a DOM tree.

>Note!
>This is a simplistic explanation.
>If you’d like to learn more about how browsers work their magic, check out [Tali Garsiel](http://taligarsiel.com/)’s excellent
>_[How Browsers Work: Behind the scenes of modern web browsers](http://www.html5rocks.com/en/tutorials/internals/howbrowserswork/)_.

The important thing to take away is that the browser **models your webpage with a DOM**.
This will be important for later.

Alright, let’s go ahead and write that `renderEvents` function.
It’s going to take the aforementioned `container`, as well as a list of events.

```js
function renderEvents(container, events) {
    var fragment = document.createDocumentFragment()
    events.forEach(function(event) {
        fragment.appendChild(createEventItem(event))
    })

    container.appendChild(fragment)
}
```

As you can see, I’ve kept this function short.
We create a [`DocumentFragment`](https://developer.mozilla.org/en/docs/Web/API/DocumentFragment) to put all the events in.
This acts like a buffer.
We put all the items in the buffer, and then empty the buffer into the `container` — the `<ul>` list — once we’re done everything.
The alternative to this would be to add the items directly to the `container`.
Doing this with a `DocumentFragment` ensures the browser doesn’t have to recompute anything until we’re finished.

Anyway, the inner portion of this function passes each event object into `createEventItem`.
`createEventItem` needs to return a DOM `Element` that we append to the fragment.

>Note!
>You should check out [Introduction to the DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction) on MDN
>if you’ve never written any JavaScript that utilizes the browser’s DOM API with functions like `appendChild` and `createElement`.
>The page on [`appendChild`](https://developer.mozilla.org/en-US/docs/Web/API/Node/appendChild) is also a good refresher.

Onwards and upwards!

```js
function createEventItem(event) {
    var elm = document.createElement('li')
    elm.classList.add('media')

    var mediaLeft = document.createElement('div')
    mediaLeft.classList.add('media-left', 'media-middle')
    mediaLeft.appendChild(createAvatar(event.actor))
    elm.appendChild(mediaLeft)

    var mediaBody = document.createElement('div')
    mediaBody.classList.add('media-body')
    mediaBody.appendChild(createHeader(event.repo))  
    mediaBody.appendChild(createMediaBody(event))

    elm.appendChild(mediaBody)
    return elm
}
```

Alright, there’s a lot going on here.

1. We create a list item to contain the event.
2. We append a div to it representing the left-hand side of the event.
3. We append a div to it representing the body of the event.

In the left-hand div, we append whatever `Element` gets returned from `createAvatar`.
We give `createAvatar` the `actor` property of the event. This looks something the following.

```
"actor": {
  "id": 3536823,
  "login": "emilyhorsman",
  "display_login": "emilyhorsman",
  "gravatar_id": "",
  "url": "https://api.github.com/users/emilyhorsman",
  "avatar_url": "https://avatars.githubusercontent.com/u/3536823?"
},
```

We’ll let `createAvatar` worry about what to do with this.

The body div then gets the `Element`s from `createHeader` and `createMediaBody` appended to it.
There isn’t anything special about all these methods, we’re just organizing the creation of all these DOM elements.
Think of it as a bit of separation of concerns — each method is getting the data they need to render the bit they’re responsible for.
This yields a really useful idea too.
Since these are all individual methods that only care about what they’re given — and not about the surrounding application — to produce their output, they can be re-used anywhere.
These functions will _always_ return the same output given the same input.
It’s really good practice to write your functions this way.
Functions should be bite-sized pieces that are simple to reason about.

>Note!
>We make heavy use of [`classList`](https://developer.mozilla.org/en-US/docs/Web/API/Element/classList).
>This just adds CSS classes to the elements.
>You may be familiar with something more like `className="media-left media-middle"`.
>This accomplishes the same thing.
>`classList` is considered the modern, better practice.

```js
function createAvatar(actor) {
    var avatar = document.createElement('img')
    avatar.src = actor.avatar_url
    avatar.classList.add('img-circle', 'media-object')
    avatar.width = 48
    avatar.height = 48
    return avatar
}
```

Nothing new here.
We create an image element, set its `src` attribute to the `avatar_url` and then give it some aesthetics.

```js
function createHeader(repo) {
    var mediaHeader = document.createElement('span')
    mediaHeader.style.display = 'block'
    mediaHeader.classList.add('media-heading', 'h6')
    mediaHeader.innerText = repo.name
    return mediaHeader
}

function createMediaBody(event) {
    var text = event.type + ' at ' + (new Date(event.created_at)).toLocaleString()
    return document.createTextNode(text)
}
```

These two are similar — we render the repository name and some information about the event.
And we’re done!

#### Take Two: Declarative Rendering

So this is neat and all, but it’s all a little unwieldy.
Everything is very _procedural_ and we have to think about appending things in the right places.
This isn’t really how we necessarily _think_ about the problem at hand, however.
We’re trying to render an event.
We know an event is a list item, with an image, a heading, and some body text inside of it.
We know each of these elements should have a bunch of properties.
I’d personally rather think of things just like that, rather than a series of commands on how all this should be manipulated.

I want to write some JavaScript that takes the list of the events from the server and describes the desired outcome, _what_ gets rendered.
I don’t want to think about things in terms of _how_ they should be rendered and _how_ we should set properties.
Describing _what_ should be accomplished is a style of programming known as [“declarative programming”](https://en.wikipedia.org/wiki/Declarative_programming).

Let’s write a method that receives the following.

1. The type of element we want to render.
2. A list of properties the element should have.
3. A list of children the element should have.

This way, we could nest calls to this function in a hierarchy similar to the visual outlines above, or the aforementioned DOM tree.
This feels a lot cleaner and easy to reason about than a procedural list of rules.

We’re going to re-write the whole example a bit, then. The code for take two is also available altogether on [CodePen](http://codepen.io/emilyhorsman/pen/VjmOZO) as well. Note that we’re re-using some methods, such as `http`.

#### createElement Helper Function

```js
function createElement(tag, props) {
    var elm = document.createElement(tag)
    Object.keys(props).forEach(function(key) {
        // Special behaviour when given a class prop.
        if (key === 'class') {
            DOMTokenList.prototype.add.apply(elm.classList, props[key])
            return
        }

        // Special behaviour when given a style prop.
        if (key === 'style') {
            var ruleset = props[key]
            Object.keys(ruleset).forEach(function(property) {
                elm.style.setProperty(property, ruleset[property])
            })

            return
        }

        // Any other prop can just be set normally.
        elm[key] = props[key]
    })

    // Remaining arguments not in method signature are children.
    var children = Array.prototype.slice.call(arguments, 2)
    children
        .map(function(child) {
            if (typeof child === 'string') {
                return document.createTextNode(child)
            }

            return child
        })
        .forEach(function(child) { elm.appendChild(child) })

    return elm
}
```

This function is a bit ridiculous.
Let’s go through it step by step.

>Note!
>Observant readers will notice that I said this function would take three arguments, but this signature only has two — `tag` and `props`.
>This is because the “third” argument is in fact just an arbitrary list of children.
>Any arguments after `tag` and `props` will be considered children.
>The inner scope of a function in JavaScript can access the arguments it was called with through the `arguments` keyword.
>You can read about this on MDN [here](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/arguments).
>Readers who have played with more recent specs of JavaScript, perhaps with a transpiler, might be jumping up and down yelling “spread operator”.

1. We create an element with the tag passed in, such as `div` or `img`.
2. We iterate over the object of properties we’re given, such as `{ width: 48, height: 48 }`.
3. If one of these properties is declaring a `class`, we need to handle it specially. We need to whip out `classList`.
4. If one of these properties is declaring a `style`, we also need to handle it specially. We to use `elm.style.setProperty` to set a CSS declaration.
5. Any remaining properties can be set with the `=` assignment operator.
6. Iterate over the remaining arguments after `tag` and `props`. These are our children.
7. If the child is a `string` object, create a text node for it.
8. Append each of these children in order to our created element.
9. Return the final element.

Yeah, this is kind of gross.

>Note!
>This function contained the most complicated JavaScript in this article.
>As mentioned in a previous note, other people have written better explanations than I can on JavaScript concepts.
>Check out: [`Object.keys`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Object/keys);
>[“Array-like objects”](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Array/slice#Array-like_objects) for the nasty `Array.prototype.slice` bit;
>[`Array.prototype.map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map);
>[`CSSStyleDeclaration`](https://developer.mozilla.org/en/docs/Web/API/CSSStyleDeclaration) for the `style.setProperty` bit;
>[JavaScript Garden’s “Accessing Properties”](http://bonsaiden.github.io/JavaScript-Garden/#accessing-properties) for the usage of `obj[key] = value`.

This procedurally written function allows us to think of the rest of our example in a declarative fashion.
Let’s get into how we do that.

The simplest example is the avatar. It has a collection of properties (also known as “props”), but no children.
Computer science dorks might think of this as a “leaf node”?
Something like that.

```js
function Avatar(actor) {
    return createElement('img', {
        src: actor.avatar_url,
        class: ['img-circle', 'media-object'],
        width: 48,
        height: 48
    })
}
```

Woo, no more steps!
We just have the tag we want, an `img`, and an object of props.
Notice that we’re not calling this `createAvatar` anymore.
I want to start thinking of these functions as a declaration of the element, instead of instructions to create an element.
Thus, let’s just use the name of what this element is — an Avatar.
`Avatar` is a “component”.
We’re going to plug these components together.

Next, the `Heading` component.
This one has a single child.
In this case, the child isn’t another component — nothing provided by another function — it’s just text.
The only child is just the string found in `repo.name`.

```js
function Heading(repo) {
    return createElement('span', {
            style: { display: 'block' },
            class: ['media-heading', 'h6']
        },

        repo.name
    )
}
```

Now let’s put them all together.
This final `EventItem` component will have some children divs.
Nested in these children will be the `Heading` and `Avatar` components.

```js
function EventItem(event) {
    return createElement('li',
        { class: ['media'] },

        createElement('div',

            { class: ['media-left', 'media-middle'] },
            Avatar(event.actor)
        ),

        createElement('div',
            { class: ['media-body'] },

            Heading(event.repo),
            event.type + ' at ' + (new Date(event.created_at)).toLocaleString()
        )
    )
}
```

We have our final `EventItem` component.
Let’s modify our previous `renderEvents` function to use it, instead of `createEventItem`.

```js
function renderEvents(container, events) {
    var fragment = document.createDocumentFragment()
    events.forEach(function(event) {
        fragment.appendChild(EventItem(event))
    })

    container.appendChild(fragment)
}
```

The only line that changed was the `fragment.appendChild` call.

So this is all pretty decent.
We’re thinking in terms of re-usable components now and we’re doing so in a fairly declarative way.
It could be improved, but it’s not bad.
We could keep improving this, but I think we’re ready to take things to React.

#### Methodology

Yes, I just dragged you through a whole lot of things and not a single line used anything from the React library.
Just before we get into React, I want to mention some other ways of doing things.
The way we just took data from a server and turned it into a document with JavaScript was a relatively low-level way of doing things.
There are alternate ways we could have done this, each with their own trade-offs.
Some of them may have offered a slightly better “abstraction”.

For instance, we could have put all the markup we’d traditionally write for this in a string.
Something like the following.

```html
<script id="eventItemTemplate" type="text/template">
    <li class="media">
        <div class="media-left media-middle">
            <img src="$src" class="img-circle media-object" width="48" height="48">
        </div>

        <div class="media-body">
            <span class="media-heading h6" style="display: block">$heading</span>
            $body
        </div>
    </li>
</script>
```

```js
document.getElementById('eventItemTemplate').innerText
```

We could then do a simple string replacement, replacing `$src`, `$heading`, and `$body` with the right variables from our `event` object.
Then we could concatenate all these strings together and set the `container.innerHTML`.
We could even just concatenate the variables directly with the markup (this wouldn’t be the best, because you’re coupling your data and presentation together).
This would work just fine.
It wouldn’t technically be as performant, but it would work.
For something this small, one wouldn’t notice a difference.
We’d even yield the benefit of giving someone without JavaScript and DOM knowledge the ability to modify the presentation of this.
This is a really notable trade-off.
It’s only worth noting as we get into learning React.
It’s an issue we’re going to solve, but the way we learn React at first will lose this same trade-off.

Doing this sort of string replacement is essentially “templating”.
This means we could use one of the popular libraries for this.
Many of them are pretty lightweight and easy to use.
The colloquial example is [`handlebars`](http://handlebarsjs.com/).
There are [many](https://garann.github.io/template-chooser/) of these JavaScript templating libraries.

Recently, many browsers also implemented a [`<template>`](https://developer.mozilla.org/en/docs/Web/HTML/Element/template) tag used for this sort of thing.
You can use this to clone an element created once, and then query children and fill in the elements.

A central point to all of this is that there are many ways to render dynamic data during the runtime of your application, after the page has been loaded from the initial server response.
There are trade-offs to all of these, but it’s more-or-less a solved problem*.

>\* Okay fine, there’s plenty of room for improvement.

### (enter React)

So, I want to say it right up front.
This problem of rendering data after a page is loaded, from a server response or something — React didn’t break new ground here.
If your page is completely static — not interactive — and doesn’t have a lot of data to manage during runtime, you should equally consider a templating library!
However, I also really like React’s approach to presentation development.
We’ll end Part 0 with an explanation of what React _is_ exactly.

>Note!
>All code is available for this last example altogether [on CodePen](http://codepen.io/emilyhorsman/pen/jrVeRp).

To begin, we’re going to modify our markup slightly.
React constructs a tree akin to what we’ve played with above.
It expects this tree to have a “root” element.
This means we can’t render a list of `<li>` elements into a container, this would be considered multiple roots.
The solution is simple however, React will render the `<ul>` element instead of placing it in our markup.
This `<ul>` element will be our root element.

```html
<div class="container-fluid m-t-1" id="app"></div>
```

Notice we moved the `id` to the container `div`.
React requires a target element in the DOM to render into.

```js
var container = document.getElementById('app')
http('GET', getEventsUrl('emilyhorsman'), renderEvents.bind(null, container))
```

These are the exact same calls as before.
We need to change our `renderEvents` function to use React now though.
Specifically, `ReactDOM`.
React actually has different “renderers”.
This means you can take React over to a native platform, such as iOS and Android, using most of the same concepts and codebase.
Or, you can render on the server-side, instead of in a visitor’s browser.
We only care about `ReactDOM` right now.
It provides a [`render`](https://facebook.github.io/react/docs/top-level-api.html#reactdom.render) function.
We call this function with a `ReactElement` and a DOM `Element`, it renders the `ReactElement` to a DOM `Element`.

```js
function renderEvents(container, events) {
    ReactDOM.render(Root({ events: events }), container)
}
```

Notice the key difference here.
Before, we created a helper function that just returned DOM `Element`s, and we stuck those in another DOM `Element` — our container.
Now, we are going to play with `ReactElement`s and only ever worry about one DOM `Element` — our container.
`ReactElement`s are not DOM `Element`s.
`ReactElement`s constitute an abstraction of the browser’s DOM, managing a lot of complexity for us.
We’ll look at the properties of these as we go.
For now, let’s look into creating some `ReactElement`s.
You’ll notice we called a `Root` function and gave the result to `render`s first argument.
This means `Root` needs to return a `ReactElement`.

```js
function Avatar(props) {
    return React.createElement('img', {
        src: props.avatar_url,
        className: 'img-circle media-object',
        width: 48,
        height: 48
    })
}

function Heading(props) {
    return React.createElement('span', {
            style: { display: 'block' },
            className: 'h6 media-heading'
        },

        props.name
    )
}

function EventItem(props) {
    return React.createElement('li',
        { className: 'media', key: props.id },
        React.createElement('div',
            { className: 'media-left media-middle' },
            Avatar(props.actor)
        ),

        React.createElement('div',
            { className: 'media-body' },
            Heading(props.repo),
            props.type + ' at ' + (new Date(props.created_at)).toLocaleString()
        )
    )
}

function Root(props) {
    return React.createElement('ul', {
        className: 'media-list'
    }, props.events.map(EventItem))
}

// React elements are a layer of abstraction: the virtual DOM.
console.assert(React.isValidElement(Heading({ name: 'foobar' })))

// This is a DOM element.
console.assert(document.createElement('div') instanceof Element)

// React elements are not.
console.assert(!(Heading({ name: 'foobar' }) instanceof Element))

// We can render the virtual DOM to actual markup on a server:
console.log(ReactDOMServer.renderToStaticMarkup(Heading({ name: 'foobar' })))
// <span style="display:block;" class="h6 media-heading">foobar</span>

```

https://twitter.com/dan_abramov/status/745757519994294281
