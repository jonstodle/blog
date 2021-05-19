+++
title = "Svelte and CSS Variables is a Match Made in Heaven"
date = 2021-05-19
[taxonomies]
tags = ["svelte"]
+++

CSS variables is a built-in way to define values which can be reused throughout your CSS styles. They also provide a performant way to dynamically set values in CSS from Javascript. This, paired with the reactive nature of Svelte makes for some fantastic oppertunities.

What better way to demonstrate this than by providing a contrived example?

# CSS variables

Let's just take a quick primer on CSS variables.

To define a variable with the name `container-background-color` and set it's value to `black`, you write this:

```css
--container-background-color: black;
```

You can define variables inside any block of CSS rules. The variable is inherited by the descendants of the target elements of the CSS rule:

```css
/* All descendants of <body> will inherit this variable */
body {
    --container-background-color: black;
}

/* All descendents of HTML elements with the class `.info-block`
will have `--container-background-color` set to `yellow` */
.info-block {
    --container-background-color: yellow;
}
```

To define a variable in the global CSS scope, and make defined *everywhere* in CSS, set it inside a special selector called `:root`:

```css
:root {
    --container-background-color: black;
}
```

To use the variable, you must use the "CSS function" `var`:

```css
.info-block > div {
    background-color: var(--container-background-color);
}
```

Lastly, you're also able to set the value of the CSS variable from Javascript. Setting the value from Javascript will automatically update the active styles on your page:

```jsx
document
    .getElementById("site-container")
    .style
    .setProperty("--container-background-color", "green");
```

# CSS Variables and Svelte

CSS variables provides an easy and performant way of dynamically setting CSS values, and Svelte provides an easy and performant way of defining dynamic values. Match made in heaven.

Here's the promised, contrived example.

Set up three sliders for setting individual parts of an RGB color value:

```html
<div class="input-container">
    <label>
        <input type="range" min="0" max="255" bind:value={red} />
        Red
    </label>
    <label>
        <input type="range" min="0" max="255" bind:value={green} />
        Green
    </label>
    <label>
        <input type="range" min="0" max="255" bind:value={blue} />
        Blue
    </label>
</div>
```

Set up variables to bind the input values to, a variable to bind an HTML element to, and a reactive statement which updates a CSS variable:

```jsx
let red = 0;
let green = 0;
let blue = 0;

let rootElement;

$: rootElement && rootElement.style.setProperty('--container-background', `rgb(${red}, ${green}, ${blue})`);
```

And, set up a CSS variable with an initial value (strictly not necessary as it's being set from Javascript, but it's good form), and apply it to a CSS rule:

```css
:root {
    --container-background: inherit;
}

.container {
    background-color: var(--container-background);
}
```

And here it is in action:

<video controls autoplay loop src="/videos/svelte-css-variables.mp4"></video>

You can play with it in the Svelte REPL: [Svelte + CSS Variables](https://svelte.dev/repl/57f03a5268884c8080b286c95e9a7c52?version=3.38.2)
