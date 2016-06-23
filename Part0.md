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

function EventItem(event) {
    this.event = event
}

EventItem.prototype.getMediaItem = function() {
    var elm = document.createElement('li')
    elm.classList.add('media')

    var mediaLeft = document.createElement('div')
    mediaLeft.classList.add('media-left', 'media-middle')
    mediaLeft.appendChild(this.getAvatar())
    elm.appendChild(mediaLeft)

    var mediaBody = document.createElement('div')
    mediaBody.classList.add('media-body')
    mediaBody.appendChild(this.getHeader())  
    mediaBody.appendChild(this.getMediaBody())

    elm.appendChild(mediaBody)
    return elm
}

EventItem.prototype.getAvatar = function() {
    var avatar = document.createElement('img')
    avatar.src = this.event.actor.avatar_url
    avatar.classList.add('img-circle', 'media-object')
    avatar.width = 48
    avatar.height = 48
    return avatar
}

EventItem.prototype.getHeader = function() {
    var mediaHeader = document.createElement('span')
    mediaHeader.style.display = 'block'
    mediaHeader.classList.add('media-heading', 'h6')
    mediaHeader.innerText = this.event.repo.name
    return mediaHeader
}

EventItem.prototype.getMediaBody = function() {
    var text = event.type + ' at ' + (new Date(this.event.created_at)).toLocaleString()
    return document.createTextNode(text)
}

function renderEvents(container, events) {
    var fragment = document.createDocumentFragment()
    events.forEach(function(event) {
        var item = new EventItem(event)
        fragment.appendChild(item.getMediaItem())
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

console.assert(React.isValidElement(Heading({ name: 'foobar' })))

var container = document.getElementById('app')
http('GET', getEventsUrl('emilyhorsman'),
    renderEvents.bind(null, container))
```

https://twitter.com/dan_abramov/status/745757519994294281
