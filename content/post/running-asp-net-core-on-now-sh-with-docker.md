+++
date = "09/10/2018 21:26:51 +00:00"
tags = ["ASP.NET Core","Docker","now.sh"]
title = "Running ASP.NET Core on now.sh with Docker"
+++
I recently fell in love with now.sh [https://now.sh]. It's a super nice hosting
service which let's deploy static websites, node powered websites or Docker
containers using i single command in your terminal: now. You literally write now 
 in your terminal and your site is live in seconds:



The original video clip was less than 1:45 minutes!

My biggest interest in now  was their Docker support. That meant I would be able
to run ASP.NET Core applications easily and, most importantly, cheaply.

I started out very hopeful and happy. I had a Dockerfile  which had a build step
and a final container only containing the final, compiled bits. I packaged the
compiled bits in the official ASP.NET Core Docker image:

FROM microsoft/dotnet:2.1-sdk AS build

WORKDIR /source

COPY . .

RUN dotnet restore

RUN dotnet publish -c Release -o /output


FROM microsoft/dotnet:2.1-aspnetcore-runtime

EXPOSE 80

WORKDIR /app

COPY --from=build /output .

CMD ["dotnet", "NowShDemo.dll"]


But this made me hit an unforeseen obstacle. The size of the final Docker image
can't be larger than 100 megabytes. Ouch!



Luckily, now.sh  does some "compression" (the Assembling image  step) on their
end, meaning that the Docker image they're looking at is actually a bit smaller,
than the "raw" image produced when building from the Dockerfile  locally. They
report the image to be 116.7 megabytes, while it's 258 megabytes on my disk.

The code is just the content of the default ASP.NET Core MVC template and I'm
already over the size limit. What to do?

The first trick is to use a smaller Linux base image. By scrolling down a bit on
the microsoft/dotnet  page in the official Docker registry
[https://store.docker.com/community/images/microsoft/dotnet], you'll find there
are runtime (and SDK) images based on Alpine. Alpine is known for it's
fantastically small image size and is popular to use with Docker. Alpine's image
is 4  megabytes! That's the whole operating system!

I change my final Docker image to use the Alpine variant:

FROM microsoft/dotnet:2.1-sdk AS build

WORKDIR /source

COPY . .

RUN dotnet restore

RUN dotnet publish -c Release -o /output


FROM microsoft/dotnet:2.1-aspnetcore-runtime-alpine

EXPOSE 80

WORKDIR /app

COPY --from=build /output .

CMD ["dotnet", "NowShDemo.dll"]


This changed the image size on my disk to 167 megabytes. That's 91 megabytes
shaved off. When I deploy this image to now.sh, it's deployed without problem. 
now.sh  reports the size of the image to be 76.8 megabytes, which is well below
the limit.

Happy times!

But. Can I do better though? Could I manage to make the image even smaller? I
bet I can!

As of version 2.1 of .NET Core, Alpine is a fully supported runtime target. That
means you'll be able to compile your application to a self-contained deployment,
which gives you a native executable for Alpine, your code and the .NET Core
runtime.

By adding <RuntimeIdentifier>alpine-x64</RuntimeIdentifier>  right below the 
TargetFramework  tag in my csproj  file, I'm able to compile my code to run on
64-bit Alpine. I also need to specify that I'm building for alpine when I'm
running dotnet publish.

.NET Core needs some tools and/or applications to be installed on Alpine to be
able to run properly. You could set up the image to install these dependencies,
but thankfully, Microsoft provides a Docker image with the dependencies
installed. This Docker image is used when building the Alpine runtime image I
used earlier.

My Dockerfile now looks like this:

FROM microsoft/dotnet:2.1-sdk AS build

WORKDIR /source

COPY . .

RUN dotnet restore

RUN dotnet publish -c Release -o /output -r alpine-x64


FROM microsoft/dotnet:2.1-runtime-deps-alpine

EXPOSE 80

WORKDIR /app

COPY --from=build /output .

CMD ["./NowShDemo"]


You'll also notice that the CMD  command at the bottom is changed to calling an
executable file in the current directory, instead of going through dotnet.

This new image is 115 megabytes on my disk. Another 52 megabytes saved! now.sh 
reports it's size as 51.5 megabytes. This is good!

Can I take it even farther? Sure I can!

When you create a ASP.NET Core MVC project from the default template it includes
a package called Microsoft.AspNetCore.All. This is a so-called metapackage 
which only contains references to other packages. In this case, those other
packages are all  the packages that make up ASP.NET Core.

If I remove that all encompassing package and add only the bits I use, I might
be able to shave off a few extra megabytes.

After some trial and error I'm able to change my csproj  from this:

<ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App"/>
</ItemGroup>


To this:

<ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore" Version="2.1.3" />
    <PackageReference Include="Microsoft.AspNetCore.Hosting" Version="2.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.HttpsPolicy" Version="2.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.1.1" />
    <PackageReference Include="Microsoft.AspNetCore.Mvc" Version="2.1.2" />
</ItemGroup>


Instead of pulling in all  of ASP.NET Core, I'm only using the parts I
explicitly need for my code to run. You could probably save even more by
splitting up the Microsoft.AspNetCore.Mvc  package, but I'm quite happy with
this result.

The new Docker image is only 105 megabytes on disk, that's another 10 megabytes
off. After deploying that image to now.sh, it reports the size to be 47.4
megabytes. A new record!

During the course of this experiment I was able to go from a Docker image of 258
megabytes to an image of 105 megabytes. That's less than half the original size,
without removing any of my code! The image that is deployed to now.sh  has
shrunken from 116.7 megabytes to 47.4 megabytes!

Mind you, this is not a real site I'm running; it's just the code from the
default ASP.NET Core MVC template. But it might give you some inspiration on how
to shrink your Docker images.

Feel free to ping me on Twitter [https://twitter.com/jonstodle]  if you have any
questions or comments.
