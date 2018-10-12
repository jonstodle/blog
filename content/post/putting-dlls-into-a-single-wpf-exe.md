+++
date = "11/28/2016 11:00:00 +00:00"
tags = ["WPF"]
title = "Putting DLLs Into a Single WPF Exe"
+++
I’ve been working on a tool for use at my job. It’s written in WPF and using all
my favorite libraries like ReactiveUI [http://reactiveui.net/], Reactive
Extensions [http://reactivex.io/]  and Json.NET [http://www.newtonsoft.com/json]
.

Using all these external libraries has landed me with a lot of DLLs that I need
to include in the folder where my WPF application runs. This isn’t really a
viable solution in my case, as I want the tool to be portable. It’s also not an
alternative to create some sort of installer; it’s not certain me or any other
user have proper privileges to install software were the tool might be used.

After searching around the web for a little while I found the perfect solution,
written up by Daniel Chambers
[http://www.digitallycreated.net/Blog/61/combining-multiple-assemblies-into-a-single-exe-for-a-wpf-application]
. Using his technique worked wonders. This is a technique credited to Jeffrey
Richter
[https://blogs.msdn.microsoft.com/microsoft_press/2010/02/03/jeffrey-richter-excerpt-2-from-clr-via-c-third-edition/]
. It basically adds the referenced assemblies (DLLs) to the exe created by
Visual Studio, and adds a handler to help the application find them.

This stopped working, however. For some bizarre reason, it stopped including one
of the assemblies and I couldn’t find a way to fix it. After a lot of debugging
ang googling I suddenly stumbled upon a simpler solution: Fody Costura
[https://github.com/Fody/Costura].

Costura is a plugin for Fody and both can easily be downloaded from NuGet.
Costura fixes this automatically for you. No setup needed!

Nothing lasts forever, and I did of course come across more troubles only a
little farther down the road: Suddenly I was getting launch crashes when testing
the exe after copying it to a different machine. I was lucky enough to find the
solution after only some minor google-fu:

It turns out that Fody Costura only manages to include assemblies in the top
folder of the build output. This is usually the bin/debug  or bin/release
folders of your project folder. SQLitePCL.raw
[https://github.com/ericsink/SQLitePCL.raw]  apparently puts the architecture
specific assemblies (x86/x64) in seperate sub folders of the output folder. This
makes them invisible to Costura.

The solution, however, is simple:

Add the two assemblies to your project inside folders named Costura32  and 
Costura64…



…set their build action to Embedded Resource…



…then tell Costura to add the assemblies in the FodyWeavers.xml  file:

<?xml version="1.0" encoding="utf-8"?>
<Weavers>
     <Costura>
            <Unmanaged32Assemblies>
                  e_sqlite3
           </Unmanaged32Assemblies>
           <Unmanaged64Assemblies>
                  e_sqlite3
           </Unmanaged64Assemblies>
     </Costura>
</Weavers>


Then there were much rejoice.

In summary: I’d recommend Fody Costura for bundling assemblies in your exe file.
It’s the simplest solution and it’s been thoroughly tested.
