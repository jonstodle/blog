+++
date = "08/23/2012 10:00:00 +00:00"
tags = ["Windows Phone"]
title = "Dynamic application title on Windows Phone 7.x using recources"
+++
I was developing a stupidly simple application for personal use and came up with
a humorous idea. I wanted the application title on top of the app pages to also
show the version. However I didn’t want to add code to every code-behind file to
make it so. I came up with the idea of adding the string, used as the
application title, to the application resources. Then I used a simple binding to
make it all happen.

<!-- In App.xaml -->  
<Application.Resources>  
    <sys:String x:Key="ApplicationTitle">FUEL CONSUMPTION - v1.0</sys:String>  
</Application.Resources>  
<!-- In MainPage.xaml -->  
<StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">  
    <TextBlock x:Name="ApplicationTitle" Text="{StaticResource ApplicationTitle}" Style="{StaticResource PhoneTextNormalStyle}"/>  
</StackPanel>


This worked excellently, but; I didn’t want to add the version to the resources
everytime i updated the application. Of course this is a bit lazy since it’s not
that much of a hassle, but there’s something a bit more cool about it updating
by itself.

To make this dynamic I needed to do some coding in C#: To get the version number
of the application i fetched the version number of the assembly. The version
number of the assembly is set by right clicking the project in Visual Studio and
click Properties. From there you click on Assembly Information and edit the
version numbering there. The resources are represented by a ResourceDictionary 
and I tried this approach:

// In MainPage.xaml#PhoneApplicationPage_Loaded event handler

// Get the version number from the assembly
var nameHelper = new System.Reflection.AssemblyName(System.Reflection.Assembly.GetExecutingAssembly().FullName);

// Set the new string as the resource string
Application.Current.Resources["ApplicationTitle"] = string.Format("FUEL CONSUMPTION - v{0}.{1}", nameHelper.Version.Major.ToString(), nameHelper.Version.Minor.ToString()));


This didn’t work to well though. Every time I tried doing this, a
NotImplementedException was thrown at me. After some googling around it turned
out that the setter for entries in the ResourceDictionary only does this one
thing; throw an NotImplementedException. I went onto the big, wide interwebs and
asked on Stack Overflow if anyone knew an answer. After some tips from some
brilliant minds, I got inspired and came up with a solution.

Adding new resources to the application isn’t a problem, nor is removing them.
All I had to do was to remove the resource with the key ApplicationTitle  and
add it again with the new string.

// In MainPage.xaml#PhoneApplicationPage_Loaded event handler

// Get the version number from the assembly
var nameHelper = new System.Reflection.AssemblyName(System.Reflection.Assembly.GetExecutingAssembly().FullName);

// Check whether the resource is present and then delete it
if(Application.Current.Resources.Contains("ApplicationTitle"))
 Application.Current.Resources.Remove("ApplicationTitle");

// Add the resource again with the updated string
Application.Current.Resources.Add("ApplicationTitle", string.Format("FUEL CONSUMPTION - v{0}.{1}", nameHelper.Version.Major.ToString(), nameHelper.Version.Minor.ToString()));


This didn’t yield the right result either. It did work if I navigated to another
page in the application. As it turns out, you see; the resources are not
dependency properties, which means they don’t send an event for changes made in
the object. That’s why the application title does not update in MainPage.xaml.
This is however easily remedied by putting the previous code into the
Application_Launching event handler which launches before the
PhoneApplicationPage is even called, as per the Windows Phone execution model.

That’s how I finally managed to get a "dynamic" application title without having
to add code to every PhoneApplicationPage  I make, I only have to bind the title
of the page to the application resource.
