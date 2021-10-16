+++
title = "Tiny Container Images With Distroless Containers"
date = 2021-10-16
[taxonomies]
tags = ["Docker", "Rust", "Go", "node.js"]
+++

When deploying applications using containers, it's usually a goal to minimize the size of the container image.

Achieving a small container size varies a lot between different languages and technologies. There is no *single* solution that will work for everyone, as usual, but there’s a pretty interesting alternative if you’re deploying Rust, D, Go, Java or node.js applications.

Google maintains a repo of what they call *distroless containers*. These are container base images that contain as little as possible. They’ve stripped away pretty much everything until you’re basically left with *libc* and not much more. There isn’t even a shell included!

Another nice benefit of the distroless images, apart from the size consideration, is the security. With so few tools and executables inside the container it increases the difficulty of being able to do much if you manage to get access to it. Less code, means less bugs and vulnerabilities.

To start using one is pretty simple. Build your application and copy the final binary into the image based on one of the distroless images:

```docker
FROM golang:1.13-buster as build

WORKDIR /src

COPY . /src

RUN go get -d -v ./...

RUN go build -o /go/bin/app

FROM gcr.io/distroless/base-debian10

COPY --from=build /go/bin/app /

CMD ["/app"]
```

It’s important to note that you have to use the for `[“/app”]` when defining a `CMD` or `ENTRYPOINT` command in the docker file. If you use the “bare” form, e.g. `CMD “/app”` , Docker will prepend the command with a shell which will not work, as there’s not shell inside the distroless container.

And that’s it!

Check out their [repo on Github](https://github.com/GoogleContainerTools/distroless) for more information.

---

Happy coding!
