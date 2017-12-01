# JQuery Template

## Introduction

jQuery Template is a simple yet robust front-end templating plugin for jQuery,
supporting any JavaScript Syntax, function calling, list rendering, template
nesting and lot more.

## Download & Installation

Download `/dist/jquery-template.js` to your website `/js` directory, and
import it in your html file by

```html
<script src="/js/jquery-template.js"></script>
```

It will be ready to use!

## Getting Started

Prior to the use of this plugin, you need:

1. A HTML5 supported Template with name `hello`:

    ``` html
    <template name="hello">
        <div data-id="data.id">
            <p>
                Hello, <span data-text="data.name"></span>
            </p>
        </div>
    </template>
    ```

2. A JSON Format Data:

    ``` js
    var data = {
        id: "a93e1cff0da",
        name: "John Doe"
    }
    ```

Then in the front-end, rendering is as easy as:

``` js
$("#container").render("hello", data);
```

The result will then be

``` html
<div id="container">
    <div id="a93e1cff0da">
        <p>
            Hello, <span>John Doe</span>
        </p>
    </div>
</div>
```

## API

#### `$(selector).render("<NAME>", <DATA>)`

- `<NAME>`: String

    Template name is the name of the template in html (e.g.
    `<template name='TEMPLATE_NAME'>`). Note that the template must be in
    `template` tag with a `name` attribution so that we can find the template
    by its name.
    
- `<DATA>`: Array *OR* Any other object
    
    If the data is an array, say `arr`, then the plugin will render `arr.length`
    instances of the same template `<NAME>` each serving an element of `arr`.
    
    If the data is any other object, you can reference, in Template elements'
    attributions, using `data`. We will explain more in the next section
    
#### Template data fields

1. To put the data as texts to the template, we use attribution `data-text`:

    ``` html
    <template name="student">
        <span>
            <span data-text="data.firstName"></span>
            <span data-text="data.lastName"></span>
        </span>
    </template>
    ```
    
    ``` js
    $("#container").render("student", {
        firstName: "John",
        lastName: "Doe"
    });
    ```
    
    Will result in
    
    ``` html
    <div id="container">
        <span>
            <span>John</span>
            <span>Doe</span>
        </span>
    </div>
    ```

2. To put extra html into an element, we use attribution `data-html`:
    
    ``` html
    <template name="student">
        <span data-html="data.html"></span>
    </template>
    ```
    
    ``` js
    $("#container").render("student", {
        html: "<a href=\"http://example.com\">click here</a>"
    });
    ```
    
    Result:
    
    ``` html
    <div id="container">
        <span>
            <a href="http://example.com">click here</a>
        </span>
    </div>
    ```

3. You can also prepend or append html elements using `data-prepend` and
    `data-append`:
    
    ``` html
    <template name="student">
        <ul data-prepend="data.before" data-append="data.after">
            <li>Hobby 1</li>
            <li>Hobby 2</li>
        </ul>
    </template>
    ```
    
    ``` js
    $("#container").render("student", {
        before: "<li>Hobby 0</li>",
        after: "<li>Hobby 3</li>"
    });
    ```
    
    Result:
    
    ``` html
    <div id="container">
        <li>Hobby 0</li>
        <li>Hobby 1</li>
        <li>Hobby 2</li>
        <li>Hobby 3</li>
    </div>
    ```

4. You can add class to an element by using `data-class`.

    ``` html
    <template name="student">
        <span data-class="data.hidden ? 'hidden' : ''">This will be hidden</span>
    </template>
    ```
    
    > Note that here we used a js syntax expression
    > `data.hidden ? 'hidden' : ''`. This is very convenient for most of the
    > users since you will not need to finish processing all the information
    > prior to rendering. If sometimes simple expression are not working
    > as expected, you can even *call globally accessible functions*!
    
    ``` js
    $("#container").render("student", {
        hidden: true
    });
    ```

    Result:

    ``` html
    <div id="container">
        <span class="hidden">This will be hidden</span>
    </div>
    ```

5. You can add css to an element by using `data-css`.

    ``` html
    <template name="student">
        <span data-css="data.css">This is red</span>
    </template>
    ```

    ``` js
    $("#container").render("student", {
        css: {
            color: "red"
        }
    });
    ```

    Result:

    ``` html
    <div id="container">
        <span style="color: red;">This is red</span>
    </div>
    ```

6. For the following keywords, using `data-<KEY>` will directly add `<KEY>`
    attribute to the element. For example, setting `data-id` to `'asdf'` will
    result in `id="asdf"`:
    
    ``` html
    <template name="student">
        <div data-id="data.pid" data-text="data.name"></div>
    </template>
    ```
    
    ``` js
    $("#container").render("student", {
        pid: "as0dfja0g",
        name: "John Doe"
    });
    ```
    
    Will result in
    
    ``` html
    <div id="container">
        <div id="as0dfja0g">John Doe</div>
    </div>
    ```

> Note: if possible, please do not use multiple tags that might result in
> conflicts in a single element. Because that might cause unexpected behavior.
> For Example, do not use `data-html` and `data-text` on a single element
> at the same time.

#### Rendering List

Putting a list into the data will resulting in rendering the specified template
multiple times.

``` html
<ul id="container"></ul>
<template name="hobby">
    <li data-id="data.id" data-text="data.hobby"></li>
</template>
```

``` js
$("#container").render("hobby", [
    { id: "1", hobby: "play basketball" },
    { id: "2", hobby: "watch movie" },
    { id: "3", hobby: "go to restaurant" },
    { id: "4", hobby: "drive" },
    { id: "5", hobby: "ski" }
])
```

Result:

``` html
<ul id="container">
    <li id="1">play basketball</li>
    <li id="2">watch movie</li>
    <li id="3">go to restaurant</li>
    <li id="4">drive</li>
    <li id="5">ski</li>
</ul>
```

#### Nesting Template

In this example, we want to render multiple students, each has their own list
of hobbies. So within a single student template, there will be multiple hobbies
being rendered. Let's take a look at how we are going to achieve this.

``` html

<!-- The Container -->
<ul id="container"></ul>

<!-- The Student -->
<template name="student">
    <p>
        <span data-text="data.firstName"></span>
        <span data-text="data.lastName"></span>
    </p>
    <p>My Hobbies:</p>
    <ul template-name="hobby" template-data="data.hobbies"></ul>
</template>

<!-- The Hobby -->
<template name="hobby">
    <li data-text="data"></li>
</template>
```

Note that in the `ul` tag in `student` template, we use `template-name` attr to
specify which template we want to use to nest the list, and `template-data` attr
to tell the renderer which data to use to render the template. Note that here,
the `template-data` does not need to be an array. It can be anything as long
as it suits the sub-template.

Also note that in `hobby` template, the `data-text` attr only has `data`. This
is because the hobby is just a string (but not object or other stuff). So you
can just use data to put the hobby `string` inside the `li` tag.

``` js
$("#container").render("student", [
    {
        firstName: "John",
        lastName: "Doe",
        hobbies: [ "hobby 0", "hobby 1", "hobby 2" ]
    },
    {
        firstName: "Liby",
        lastName: "99",
        hobbies: [ "programming", "jquery" ]
    },
    {
        firstName: "Kizuna",
        lastName: "Ai",
        hobbies: [ "play games", "dance" ]
    }
]);
```

Voila! The result is just as simple as expected:

``` html
<ul id="container">
    <li>
        <p>
            <span>John</span>
            <span>Doe</span>
        </p>
        <ul>
            <li>Hobby 0</li>
            <li>Hobby 1</li>
            <li>Hobby 2</li>
        </ul>
    </li>
    <li>
        <p>
            <span>Liby</span>
            <span>99</span>
        </p>
        <ul>
            <li>programming</li>
            <li>jquery</li>
        </ul>
    </li>
    <li>
        <p>
            <span>Kizuna</span>
            <span>Ai</span>
        </p>
        <ul>
            <li>play games</li>
            <li>dance</li>
        </ul>
    </li>
</ul>
```

## Examples

There are much more examples inside `/example` folder. Please pay there a visit
if there's anything unclear after this README.

## Contribution

Super open for contribution! Ping me for any question or advice and welcome
to submit a pull request!
