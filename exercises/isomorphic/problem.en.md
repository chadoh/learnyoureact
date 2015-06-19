React can be made to be isomorphic. This means that the same code can be run on
both the server and in the browser. This gives us the ability to render content
on the server, so that page loads more quickly for the user.

Let's try it!

Until now, we've been compiling the JSX in the browser. This is slow
for the user. Now, we're going to generate the initial HTML on the server, so
that the initial page load happens more quickly.

FIXME: I don't understand this well enough to know how to rephrase this

In the past excercises, there are a code which raises the front-end event, but
that does not work. Where is that?

The code is the check event of `checkbox` you wrote in `State`. 
At `State`, to tell the truth, you can check the `checkbox` whatever code you write. 
In this excercises, let's confirm which you could write the right code or not. 
There are a bit many code that you have to change. 
Let's make the best of it!

# Challenge 
---

Start by installing the required modules. Run the command below.

```
$ npm install browserify reactify
```

Next, create `app.js` at the same directory as `program.js` and copy the code
below into the file.

``` 
var React = require('react'); 
var TodoBox = require('./views/index.jsx');

var data = JSON.parse(document.getElementById('initial-data').getAttribute('data-json')); 
React.render(<TodoBox data={data} />, document.getElementById("app")); 
```

This loads `TodoBox` from `index.jsx`, and gets data from via the `data-json`
attribute of the element with id of `initial-data`. Then it renders a `TodoBox`
into the element with id of `app`, all on the server.

Next, let's make `program.js` work with all this.

Before you start, you may want to check your current files into source control,
or create new ones for this exercise.

First, let's `require` all the things we'll need:

``` 
var React = require('react'); 
var DOM = React.DOM; 
var body = DOM.body; 
var div = DOM.div; 
var script = DOM.script;

var browserify = require('browserify'); 
```

Next, add a line that reads `index.jsx` under the sentence that `require`s `node-jsx`.

``` 
require('node-jsx').install(); 
var TodoBox = require('./views/index.jsx'); 
```

Finally, add a route for `/bundle.js`, and modify the route for `/`, as shown
below. 

FIXME: I don't understand this well enough to rephrase it
If you have an access to `/bundle.js`, you change `app.js` that can work at front-end and response. 
If you have an access to `/`, you response HTML that consists of readed `index.jsx`, and data from server, and `bundle.js`.

``` 
app.use('/bundle.js', function(req, res) { 
  res.setHeader('content-type', 'application/javascript'); 
  browserify('./app.js') 
    .transform('reactify') 
    .bundle() 
    .pipe(res); 
});

app.use('/', function(req, res) { 
  var initialData = JSON.stringify(data); 
  var markup = React.renderToString(React.createElement(TodoBox, {data: data}));

  res.setHeader('Content-Type', 'text/html'); 
   
  var html = React.renderToStaticMarkup(body(null, 
      div({id: 'app', dangerouslySetInnerHTML: {__html: markup}}), 
      script({id: 'initial-data', 
              type: 'text/plain', 
              'data-json': initialData 
            }), 
      script({src: '/bundle.js'}) 
  )); 
       
  res.end(html); 
}); 
```

Once you're finished, try it out by running `node program.js 3000 Milk 13:00` and visiting `http://localhost:3000`.

Click the checkbox on and off, and confirm whether you can check the checkbox correctly.

Once you're confident, run `learnyoureact verify program.js`.

NOTE: Sometimes the HTML produced with `node program.js` can differ from the HTML produced with `verify`. FIXME: I don't know what the stuff about `data-react-checksum` means.

NOTE: I think some of you notice that the difference of HTML in which you run `verify` and access `http://localhost:3000`. 
`data-react-checksum` or  `data-reactid` that keep the DOM unique make the compare of right answer and the code you write fail in the quality. 
So if you run `verify`, this code compare the code at other part of HTML.

Bonus challenge: try breaking the checkbox by setting `checked` to always be `true` or `false` in the `handleChange` function.
