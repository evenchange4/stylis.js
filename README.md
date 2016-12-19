# Stylis

[![npm](https://img.shields.io/npm/v/stylis.svg?style=flat)](https://www.npmjs.com/package/stylis) [![licence](https://img.shields.io/badge/licence-MIT-blue.svg?style=flat)](https://github.com/thysultan/stylis.js/blob/master/LICENSE.md) 
 ![dependencies](https://img.shields.io/badge/dependencies-none-green.svg?style=flat)

- ~1Kb minified+gzipped
- ~3kb minified

Stylis is a small css compiler that turns this

```javascript
stylis('#user', styles);
```

Where `styles` is the following css

```scss

font-size: 2em;
font-family: sans-serif;

:host {
    color: red;
}

:host(.fancy) {
    color: red;
}

:host-context(body) {
    color: red;
}

// removes line comment

.name {
    transform: rotate(30deg);
}

@global {
    body {
        background: yellow;
    }
}

span, h1, :global(h2) {
	color:red;

	/**
	 * removes block comments
	 */
}

&{
	animation: slidein 3s ease infinite;
    display: flex;
    flex: 1;
    user-select: none;
}

&:before {
	animation: slidein 3s ease infinite;	
}

@keyframes slidein {
	from { transform: translate(10px); }
	to { transform: translate(200px); }
}

@media (max-width: 600px) {
    display: block;

	&, h1 { 
        appearance: none; 
    }
}

// nested
h1 {
    color: red;

    h2 {
        display: block;

        h3, &:hover {
            color: blue;
        }
    }

    font-size: 12px;
}

```

into this (minus the whitespace)

```css
#user {
	font-size: 2em;
	font-family: sans-serif;
}
#user {
    color: red;
}
#user.fancy {
    color: red;
}
body #user {
    color: red;
}
#user .name {
    -webkit-transform: rotate(30deg);
    -ms-transform: rotate(30deg);
    transform: rotate(30deg);
}
body {
    background: yellow;
}
#user span,
#user h1,
h2 {
    color: red;
}
#user {
    display: -webkit-flex;
    display: flex;

    -webkit-flex: 1;
    -moz-flex: 1;
    flex: 1;

    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;

    -webkit-animation: userslidein 3s ease infinite;
    animation: userslidein 3s ease infinite;
}
#user:before {
    -webkit-animation: userslidein 3s ease infinite;
    animation: userslidein 3s ease infinite;
}
@-webkit-keyframes userslidein {
    from {
        -webkit-transform: translate(10px);
        -ms-transform: translate(10px);
        transform: translate(10px);
    }
    to {
        -webkit-transform: translate(200px);
        -ms-transform: translate(200px);
        transform: translate(200px);
    }
}
@keyframes userslidein {
    from {
        -webkit-transform: translate(10px);
        -ms-transform: translate(10px);
        transform: translate(10px);
    }
    to {
        -webkit-transform: translate(200px);
        -ms-transform: translate(200px);
        transform: translate(200px);
    }
}
@media (max-width: 600px) {
    #user, #user h1 {
        -webkit-appearance: none;
        -moz-appearance: none;
        appearance: none;
    }

    #user {
        display: block;
    }
}

h1 {
    color: red;
    font-size: 12px;
}

h1 h2 {
    display: block;
}

h1 h2 h3,
h1 h2 h2:hover {
    color: blue;
}
```

## Supports

* Edge
* IE 9+
* Chrome
* Firefox
* Safari
* Node.js

---

## Installation

#### direct download

```html
<script src=stylis.min.js></script>
```

#### CDN


```html
<script src=https://unpkg.com/stylis@0.8.0/stylis.min.js></script>
```

#### npm

```
npm install stylis --save
```

## API

```javascript
stylis(
    selector: {string}, 
    cssString: {string}, 
    namespaceAnimations, {boolean}
    namespaceKeyframes {boolean}
);

// namespaceAnimations and namespaceKeyframes allow 
// you to prevent keyframes and animations
// from being namespaced
```

## Intergration

You can use stylis to build an abstraction ontop of, for example imagine we want to build an abstract that makes the following React Component possible

```javascript
class Heading extends React.Component {
    stylesheet(){
        return `
            &{
                color: blue
            }
        `;
    }
    render() {
        return (
            React.createElement('h1', 'Hello World')
        );
    }
}
```

We could simply extend the Component class as follows

```javascript
React.Component.prototype.stylis = function (self) {
    var namespace = this.displayName;

    return function () {
        stylis(namespace, self.stylesheet(), document.head);
        mounted = true;
        this.setAttribute(namespace);
    }
}
```

Then use it in the following way

```javascript
class Heading extends React.Component {
    stylesheet(){
        return `
            &{
                color: blue
            }
        `;
    }
    render() {
        return (
            React.createElement('h1', {ref: this.stylis(self)}, 'Hello World')
        );
    }
}
```

When the first instance of the component is mounted the function assigned to the ref will get executed adding a style element with the compiled output of `stylesheet()`
where as only the namespace attribute is added to any subsequent instances.

You can of course do this another way

```javascript
class Heading extends React.Component {
    constructor (props) {
        super(props);
        // or you can even inline this
        this.style = React.createElement('style', {id: this.displayName}, this.stylesheet());
    }
    stylesheet(){
        return `
            &{
                color: blue
            }
        `;
    }
    render() {
        return (
            React.createElement('h1', null, 'Hello World', this.style)
        );
    }
}
```

One will add it to the head another will render it in place with the component.

If you want a better picture into what can be done, there is an abstraction i created
for [dio.js](https://github.com/thysultan/dio.js) that does away with the above boilerplate entirely [http://jsbin.com/mozefe/1/edit?js,output](http://jsbin.com/mozefe/1/edit?js,output)