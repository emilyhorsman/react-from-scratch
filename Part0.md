# Part 0

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
  avatar.classList.add('avatar', 'img-circle', 'media-object')
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
