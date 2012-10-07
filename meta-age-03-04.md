# Meta Age #

##The Meta Aspect of Node.js##
I've been **exploring** since few months now; the last Buzzy programming environnement : **[Node.Js][1]** .

And as a **Developer** fighting for a kind of **"hyper generic"** : universal exchange network, and as a **Hacker** wishing to **exceed limitations**.

I'm quite interested in the **"Meta"** aspect of programming environnement, the way you could dynamically **push forward the limit** of a system.

And I found with [Node.JS][1] a **really prolific activity** around the development of such **abstracting layers** (I described below) that **I never found before** in any other programming environnement.

Pushed by the **HTML5 wave**, javascript development ( ECMAScript 5 ) and community is becoming **really active and exciting** : [Kinect interfacing, Face recognition](http://www.nodejs-news.com/fun-with-nodejs/face-recognition-with-nodejs/), [Drone control](http://t.co/ePzJAFa7), [p2p Webcam Streaming](http://vimeo.com/36229857), [3D data flow editor](http://idflood.github.com/ThreeNodes.js/public/index.html#example/spreads1.json) / [Physics engine](http://soulwire.co.uk/coffeephysics/), IDE, etc...

Below are some problematic to push forward programming limitations and really good Libraries that illustrate how to find a nice solutions using [Node.js][1] and/or [CoffeeScript][2] :

## How enabling you to create your own semantic programming language : DSL (Domain Specific Language) ? ##


#### **[CoffeeScript][2]** is a meta language over Javascript, taking the best from ruby & python =  a really readable code with less verbosity ####
Javascript compilation can be executed on runtime in both client ( browser ) and server ( node.js )

**Bored by syntax** like Javascript :

```javascript
var logFunc;
logFunc = function() {
  var args;
  args = 1 <= arguments.length ? Array.prototype.slice.call(arguments, 0) : [];
  return console.log.apply(console, args);
};
```

**A nice alternative** [CoffeeScript][2] :

```coffeescript
logFunc = (args...) -> console.log args...
```


#### **[CoffeeKup](http://coffeekup.org/)** is an HTML DSL over [CoffeeScript][2] as a template engine ( used in [Zappa below](#zappa) ) ####

Writing your **template in [CoffeeScript][2]**, your HTML is becoming less verbose, and block more evident

```coffeescript
    html ->
      head ->
      body ->
        h1 @title or "Untitled"
        div "#divID.className", ->
           span style: "font-weight: bold", 
             "Content"
           text " of my Div"
```

**Generated HTML** :

```html
    <html>
      <head>
      </head>
      <body>
        <h1>Untitled</h1>
        <div id="divID" class="className">
          <span style="font-weight: bold">Content</span>
     of my Div    </div>
      </body>
    </html>
```

 


## In which way you can write and distribute the same code for browser / server / node ? ##

<a name="zappa"/> 
**[Zappa](http://zappajs.org/)** enable you to write clear [CoffeeScript][2] Web App in only one file.

  * Client [CoffeeScript][2] code

```coffeescript
# Will be compiled and served as static Javascript
@client '/index.js': ->
    #Client Scope
    alert "I'm at : #{window.location.href}"
```


  * HTML Server Templating with [CoffeeKup](http://coffeekup.org/) /  [CoffeeScript][2]

```coffeescript
# Server HTTP Routes
@get '/': ->
   @render 'index', 
       title: "Hello"
       myContainerContent: "World!"
# View rendered by default template engine CoffeeKup
@view 'index': ->
   h1 @title
   div '.myContainer', ->
      @myContainerContent
```


  * "Universal WebSocket" client / server communication using [Socket.io](http://socket.io/)

```coffeescript
#Server Scope events receiver
@on 
   connection: ->
     @emit welcome: "client"
   "welcome.back": -> console.log 'welcome ack'
@client '/index.js': ->
  # Client Scope
  # Socket Connection
  @connect()
  # Client event receiver
  @on welcome: (me) ->
    alert 'server say welcome to #{me} !'
    @emit 'welcome.back'
```

  * Client routing with [Sammy.js](http://sammyjs.org/)

```coffeescript
@client '/index.js': ->
  # Client Hash Route
  @get '#/route': -> alert 'client routes!'
```


  * Shared Client / Server [CoffeeScript][2] code

```coffeescript
# Code will be executed on server and served as static javascript for browser, nice for creating a bootlooader
@shared '/shared.js': ->
   root = window ? global
   alert if window? then "I'm in the Browser" else "I'm in node"
   root.sum = (x, y) -> x + y
```

  It is a really nice way to wrap main libs clearly, see the [Zappa.coffee annotated source](http://zappajs.org/docs/zappa.html) and hack it ;)



## Is it possible to **switch** from a **programming paradigm** to an other ? ##
Node.js use the **async** programming paradigm, like Javascript in the Browser. which really boost performance on I/O for a given CPU core 

I came to node.js seeing my Javascript Semantic Web library on Browser side (using HTTP [SPARQL](http://en.wikipedia.org/wiki/SPARQL) Query) taking 10x less time to load a graph by recursive loading that the ruby ORM : [Spira](http://blog.datagraph.org/2010/05/spira) I was using on [ruby](http://www.ruby-lang.org/) backend server

ruby had a solution to **write asynchronous code synchronously** it has introduced [Fibers](http://www.igvita.com/2009/05/13/fibers-cooperative-scheduling-in-ruby/) in v1.9, which had an implementation on Node.js using [node-fibers](https://github.com/laverdet/node-fibers).
But it is not working on browser side ... unless you discover an other wonderfull lib [Streamline.js](https://github.com/Sage/streamlinejs)

**[Streamline.js](https://github.com/Sage/streamlinejs)** is *Asynchronous Javascript for dummies*, you write your code using the "_" (underscore) as callback and chain your operations, replacing this : 

```coffeescript
lineCount = (path, callback) ->
  fs.readFile(path, "utf8", (err, data) ->
    if err
      callback err
      return
    callback null, data.split('\n').length
```

by this : 

```coffeescript
lineCount = (path, _) ->
  fs.readFile(path, "utf8", _).split('\n').length
```

The code could run with node-fibers in Node.js or generated to javascript asynchronous code, see the [Streamline.js Code Generator](http://sage.github.com/streamlinejs/examples/streamlineMe/streamlineMe.html)

## How to create meta architecture ##

By breaking communication barriers, with a distributed evented network

#### **[Hook.io](https://github.com/hookio/hook.io)** a distributed EventEmitter network  ####

```coffeescript
hookA = new Hook name: "a"
hookB  = new Hook name: "b"

hookA.on "*::sup", (data) ->
   console.log "#{@event} #{data}"
hookB.on "hook::ready", ->
  hookB.emit "sup", "dog"

hookA.start()
hookB.start()
```

By decoupling components and building a network architecture on top like in [flow based programming](http://en.wikipedia.org/wiki/Flow-based_programming)

#### **[Noflo](https://github.com/bergie/noflo)** a flow based programming library ####
Creating compnents with named input and output streams, linked together by  graphs.

A File count line network definition in FBP


    # FBP Network Syntax
    'somefile.txt' -> SOURCE Read(ReadFile) OUT -> IN Split(SplitStr)
    Split() OUT -> IN Count(Counter) COUNT -> IN Display(Output)
    Read() ERROR -> IN Display()

I have writted a small wrapper around this lib enabling you to create component like this : 

```coffeescript
Tweet = (data_in, out_url, out_message) ->
  if data_in().entities?.urls?
    for url in data_in().entities.urls
      out_url.send url.expanded_url 
  out.message.send data_in().text 
```

 
## How to code in a meta environnement ##

Usually the more important tool for a developer is the IDE ( distributed vim :) ), for this Javascript / Node.JS is pushing your working environement in the browser with project like :

#### **[Cloud9](http://c9.io)** a Web RAD IDE with Node.js cloud plugged environement ####

#### **[JSFiddle](http://jsfiddle.net/evangenieur/9MfNt/)** a Web IDE for Client code prototyping / testing libs ####

#### **[CoffeeXP](http://evangenieur.com/coffeexp)** a CoffeeScript live coding experiments ####

Hosting is neither a problem with cloud hosting service / techno

#### **[Nodester](http://nodester.com/)** an Open source cloud hosting / Node.js ####

#### **[Nodejitsu](http://nodejitsu.com/)** scalable Node.js clouds ####

So my dream of a Live Stream Social Coding Cloud, has been pushed some steps forward :)


[1]: http://nodejs.org/
[2]: http://coffeescript.org/

