+++
title = "Alpine.js"
date = 2020-12-31
[taxonomies]
tags = ["alpine", "javascript"]

+++

jQuery has been a great way to quite easily add some interaction to your static HTML, but it might start to feel a bit heavy-handed once you start trying to coordinate multiple parts of the page.

Wouldn't it be nice if there was a way to set up this interactivity a bit more declaratively?

# Enter [Alpine.js](https://github.com/alpinejs/alpine/)

Add Alpine to your page like this

```html
<script src="<https://cdn.jsdelivr.net/gh/alpinejs/alpine@v2.x.x/dist/alpine.min.js>" defer></script>
```

You're then able to declare simple components, directly inside your HTML:

```html
<div x-data="{ open: false }">
    <button @click="open = true">Open Dropdown</button>

    <ul
        x-show="open"
        @click.away="open = false"
    >
        Dropdown Body
    </ul>
</div>
```

`x-data` indicates the *root* element, and the initial state of your component. The children of that element are now able to listen to events with the `@<event name>` syntax. Alpine also has some shortcuts, such as `x-show` which hides or shows an element.

Alpine's choice in style is lifted straight from Vue, so if you're somewhat familiar with Vue, you should feel right at home with Alpine's vocabulary.

Speaking of Vue, it's also possible to create reusable components:

```html
<div x-data="dropdown()">
    <button x-on:click="open">Open</button>

    <div x-show="isOpen()" x-on:click.away="close">
        // Dropdown
    </div>
</div>

<script>
    function dropdown() {
        return {
            show: false,
            open() { this.show = true },
            close() { this.show = false },
            isOpen() { return this.show === true },
        }
    }
</script>
```

`dropdown` returns an object which contains all the component code: state is defined using fields and functions are declared in the standard way.

Setting `x-data` to the result of `dropdown` makes the fields and functions available to all child elements. Use this to easily reuse some logic.

# Stimulus

Another framework trying to do much of the same thing is [Stimulus](https://stimulusjs.org/): Use your existing HTML and sprinkle in some JavaScript.

I'm not completely sold on Stimulus. I think it's the right idea, but it's not executed as elegantly. Alpine feels a bit less in your way, but that's just a personal preference.

# Closing thoughts

If you're in need of some interactivity on your so far static page, really give [Alpine](https://github.com/alpinejs/alpine/) a look. I've had success with it so far when all I need is a bit of sprucing up in my static HTML.

Happy coding!

