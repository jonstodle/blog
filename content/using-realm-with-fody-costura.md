+++
title = "Using Realm with Fody.Costura"
date = 2017-03-10T11:00:00.0000000+00:00
[taxonomies]
tags = ["Realm"]
+++
I’m a huge fan of Realm and was very excited when they recently announced and released Realm for Win32. This means you can use Realm in WPF and Windows Forms applications.

This means that I can use Realm for efficient storage and caching in the tools I develop for use at work.

When developing these tools I usually package them into a single executable file. This makes them much easier to use in environments where installation of new software is not allowed and it makes them easier to transport. With no external dependencies, you can copy the file to a new environment and it just works.

I did however stumble on to some problems with using my preferred packaging solution [Fody.Costura](https://github.com/Fody/Costura) when trying to use Realm. Realm makes some assumptions when it’s first used about where it’s DLLs are located, and this breaks when Costura packages it all up.

After reaching out to one of the Realm devs and creating a [Github issue](https://github.com/realm/realm-dotnet/issues/1249) I got a tip on how to work around it. While this workaround works perfectly, it would be nice if Realm supported packaging like this out-of-the-box. I’ve created a [seperate Github issue](https://github.com/realm/realm-dotnet/issues/1252) (at one of the devs’ suggestion) and requested this.

To get Realm working with Costura (as of Realm 1.0.4 and Costura 1.3) you can do the following.

* Add a folder named `Costura32` and a folder named `Costura64` to your project.
* Add the `realm-wrappers.dll` from `\bin\Debug\lib\win32\x86` into the folder named Costura32 and the `realm-wrappers.dll` from `\bin\Debug\lib\win32\x64` to the folder named Costura64
* Right click each of the realm-wrappers.dll files and select Properties. Set the `Build Action` to `Embedded Resource`.
* Open `FodyWeavers.xml` and change the `<Costura />` element to `<Costura CreateTemporaryAssemblies="true" Unmanaged32Assemblies="realm-wrappers" Unmanaged64Assemblies="realm-wrappers"/>`.

The `realm-wrappers.dll` files are hidden away in a sub folders and aren’t snapped up by Costura by default. We add them to the specially named folders and tell Costura about them by adding `Unmanaged(32|64)Assemblies` to the Costura element.

`Realm.dll` also makes some assumptions when it’s run, and we have to write it to disk before it can be used. Costura, luckily, has a configuration switch to make this happen. By adding `CreateTemporaryAssemblies="true"` to the the Costura element, we tell it to copy the DLLs to disk before using them.

It’s not too much trouble to get Realm working with Costura, but if you want it to be easier, voice your support on [Github](https://github.com/realm/realm-dotnet/issues/1252).
