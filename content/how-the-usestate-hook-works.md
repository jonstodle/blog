+++
title = "How the useState Hook Works"
date = 2021-01-12
[taxonomies]
tags = ["react", "programming"]

+++

I recently tried to explain to a friend how the `useState` hook works in React. I thought my analogy was quite a good one, so here goes!

Imagine React has a large warehouse where it can store information you don't want to reset every time React updates the view. We'll call this warehouse _Central Storage_.

To be able to store information in Central Storage, we'll have to reserve shelf space. We reserve shelf space by calling `useState`. `useState` takes a value which will be the default information stored in your shelf space.

`useState` returns an array with two items:

1. A fresh _copy_ of the information stored in your shelf space. We'll receive a fresh copy every time React updates the view.
2. A function which let's us overwrite the information stored in your shelf space. To permanently change the information, which we receive a copy of in _1_, we have to call this function with the new information.

Our code will look something like this:

```javascript
const shelfSpace = useState("Hello World!");
//       ^            ^           ^
//       |            |           |
//       ∟ Array containing a copy of the information stored in your shelf space, and a function to overwrite
//         the information stored in your shelf space
//                    |           |
//                    ∟ "Reserve shelf space"
//                                |
//                                ∟ Store "Hello World!" in the shelf space when reserving the shelf space

const message = shelfSpace[0];
//      ^                  ^
//      |                  |
//      ∟ Store the copy of the information in your shelf space in a more conveniently named variable. It has a
//        name which better describe the information.
//                         |
//                         ∟ The copy of the information will always be the first item in the array returned by
//                           useState. At index 0, in other words

const overwriteMessage = shelfSpace[1];
//            ^                     ^
//            |                     |
//            ∟ Store the function for overwriting the information in your shelf space in a more conveniently
//              named variable. It better describes what the function does.
//                                  |
//                                  ∟ The function for overwriting the information in your shelf space will always
//                                    be the second item in the array returned by useState.
//                                    At index 1, in other words
```

We don't have to use three lines of code for every reservation i Central Storage. We can shorten it to this:

```javascript
const [message, overwriteMessage] = useState("Hello World!");
//        ^            ^
//        |            |
//        ∟ "Unpack" the list returned by useState and store the first item in the array (index 0)
//          in a variable named `message`
//                     |
//                     ∟ Store the second item in the array (index 1) in a variable named `overwriteMessage`
```

When making changes to our copy of the information we've received from Central Storage, those changes will not show up in your shelf space in Central Storage automatically. We have to explicitly ask for the information in your shelf space in Central Storage to be overwritten.

Even if we update the variable `message` in your code...

```javascript
const [message, overwriteMessage] = useState("Hello World!");
message = "Hello Programming!";
```

... it won't change the information in your shelf space in Central Storage. We've just modified our copy of the information. The next time React updates the view, it will fetch a fresh copy of the information in Central Storage - not containing the changes you just made.

To store your changes, we'll have to explicitly overwrite the changes in your shelf space in Central Storage:

```javascript
const [message, overwriteMessage] = useState("Hello World!");
message = "Hello Programming!";
overwriteMessage(message)
//                  ^
//                  |
//                  ∟ Overwrite the information in your shelf space in Central Storage with our changed copy
```

---

Hope this helps someone better understand the concept of the `useState` hook.

Happy coding!