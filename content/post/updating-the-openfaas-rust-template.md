+++
date = "2019-06-25T18:20:00.0000000+02:00"
tags = ["Rust", "OpenFaaS"]
title = "Updating the OpenFaaS Rust Template"
+++
Recently we've started looking into using [OpenFaaS](https://www.openfaas.com/) at work. We're still only in the experimenting phase, but I'm really liking what I'm seeing so far.

I've also fallen in love with Rust recently and would love to use it with OpenFaaS, and luckily there's a template contributed by the community which was ready to use. To my dismay though, I soon discovered a small flaw. The resulting Docker container containing my function was just past 1 GB in size. That's way too big for 30 lines of code, even considering the three dependencies I had.

I started looking at what could be included that made the size of the container that large. It soon revealed itself to be the base image. The OpenFaaS template uses the official Rust image to build and run the function, the problem is though, that image is meant for building the application, not necessarily running it. There's a lot of stuff included in the Rust image, not needed when running the application normally. No need for Cargo, rustc or any other parts of the toolchain for example.

The Rust image is based on Debian Stretch, so I changed the Dockerfile to use the Rust image as a build step instead. It then copies the compiled binary to a new slimmer image based on `debian:stretch-slim`.

This puts the image size at approximately 100 MB, only one thenth the size!

Ideally I'd like to put it on an Alpine image, but part of the application I'm building take a dependency on the operating system it's built on. Meaning: if it's built on Debian, it needs Debian to run. I might try to make an Alpine build image to work around this.

After I'd slimmed down the size of the produced image, I also went through the rest of the Docker file and aligned it with how the official Go template was set up. It felt more right making the two images as similar as possible, as they're both "system languages" with no to few external dependencies.

You can find the updated OpenFaaS Rust template on [my Github](https://github.com/jonstodle/openfaas-rust-template) and you can use it in your OpenFaaS projects by running

```bash
faas-cli template pull https://github.com/jonstodle/openfaas-rust-template
faas-cli new oxidize --language rust
```

Happy coding!