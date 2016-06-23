# Part 0

<p data-height="265" data-theme-id="light" data-slug-hash="jrVeRp" data-default-tab="result" data-user="emilyhorsman" data-embed-version="2" class="codepen">See the Pen <a href="http://codepen.io/emilyhorsman/pen/jrVeRp/">React from Scratch Part 0</a> by Emily Horsman (<a href="http://codepen.io/emilyhorsman">@emilyhorsman</a>) on <a href="http://codepen.io">CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

```html
<div class="container-fluid m-t-1">
    <ul id="app" class="media-list">
    </ul>
</div>
```

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

function createAvatar(actor) {
    var avatar = document.createElement('img')
    avatar.src = actor.avatar_url
    avatar.classList.add('img-circle', 'media-object')
    avatar.width = 48
    avatar.height = 48
    return avatar
}

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

function renderEvents(container, events) {
    var fragment = document.createDocumentFragment()
    events.forEach(function(event) {
        fragment.appendChild(createEventItem(event))
    })

    container.appendChild(fragment)
}

function getEventsUrl(user) {
    return 'https://api.github.com/users/' + user + '/events'
}

var container = document.getElementById('app')
http('GET', getEventsUrl('emilyhorsman'),
    renderEvents.bind(null, container))
```

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

function createElement(tag, props) {
    var elm = document.createElement(tag)
    Object.keys(props).forEach(function(key) {
        // Special behaviour when given a class prop.
        if (key === 'class') {
            DOMTokenList.prototype.add.apply(elm.classList, props[key])
            return
        }

        if (key === 'style') {
            var ruleset = props[key]
            Object.keys(ruleset).forEach(function(property) {
                elm.style.setProperty(property, ruleset[property])
            })

            return
        }

        elm[key] = props[key]
    })

    // Remaining arguments not in method signature.
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

function Avatar(actor) {
    return createElement('img', {
        src: actor.avatar_url,
        class: ['img-circle', 'media-object'],
        width: 48,
        height: 48
    })
}

function Heading(repo) {
    return createElement('span', {
            style: { display: 'block' },
            class: ['media-heading', 'h6']
        },

        repo.name
    )
}

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

function renderEvents(container, events) {
    var fragment = document.createDocumentFragment()
    events.forEach(function(event) {
        fragment.appendChild(EventItem(event))
    })

    container.appendChild(fragment)
}

function getEventsUrl(user) {
    return 'https://api.github.com/users/' + user + '/events'
}

var container = document.getElementById('app')
http('GET', getEventsUrl('emilyhorsman'),
    renderEvents.bind(null, container))
```


```html
<div class="container-fluid m-t-1" id="app"></div>
```

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

function renderEvents(container, events) {
    ReactDOM.render(Root({ events: events }), container)
}

function getEventsUrl(user) {
    return 'https://api.github.com/users/' + user + '/events'
}

// React elements are a layer of abstraction: the virtual DOM.
console.assert(React.isValidElement(Heading({ name: 'foobar' })))

// This is a DOM element.
console.assert(document.createElement('div') instanceof Element)

// React elements are not.
console.assert(!(Heading({ name: 'foobar'}) instanceof Element))

// We can render the virtual DOM to actual markup on a server:
console.log(ReactDOMServer.renderToString(Heading({ name: 'foobar' })))
// <span style="display:block;" class="h6 media-heading" data-reactroot="" data-reactid="1" data-react-checksum="966468459">foobar</span>

var container = document.getElementById('app')
http('GET', getEventsUrl('emilyhorsman'),
    renderEvents.bind(null, container))
```

https://twitter.com/dan_abramov/status/745757519994294281
