+++
date = "2017-05-09T10:00:00.0000000+00:00"
tags = ["Realm"]
title = "Clever add and edit logic with Realm"
+++
I fell in love with Realm [https://realm.io/]  about half a year back. It’s an
alternative to SQLite based databases. It’s easy to use, fast to set up, fast
and cross platform. Unlike SQLite it’s not a relational database, but rather an
object database. You store your plain old objects “directly” in the database
instead of going through an ORM
[https://en.wikipedia.org/wiki/Object-relational_mapping]  layer or some sort of
serialization.

Because Realm works differently than Core Data, Couchbase or something similar,
you have to think differently about how to do certain things. While watching a
presentation of Realm on the Xamarin Show
[https://channel9.msdn.com/Shows/XamarinShow/Episode-20-Realm-Mobile-Databases-with-Adam-Fish] 
 I spotted quite a nice trick when it comes to editing objects in the database.

The problem with editing objects in Realm
Usually you’ll load the record from the database, change it and then write the
updated record to the database. If the changes are to be discarded, you skip the
step storing the updated record in the database.

This is a bit more difficult in Realm. When you load a record from the Realm
database, you’re actually loading an object. If you want to make changes to that
object, you’ll have to put those changes in a transaction. Usually you’ll do
updates like this:

myRealm.Write(() =>
{
    // Make changes to my object here
});


This becomes very unpractical when doing data binding. You’d have to make custom
setter logic for the properties backed by a Realm object, or do some sort of
shallow copy of the object to have a version you can edit directly.

Editing objects the Realm way
There is an alternative way of changing an object: you can call 
myRealm.BeginWrite()  which will return a Transaction. You can now do changes to
objects in the Realm database. When you’re done you can commit the changes
you’ve done by calling myTransaction.Commit(). If you want to discard (or roll
back) the changes, you call myTransaction.Dispose().

Here comes the clever bit: If you create a transaction when navigating to the
page, you can do binding directly on the object, save changes by calling 
Commit()  or discard the by calling Dispose().

Here’s a simple example. Given the following class:

public class Data : RealmObject
{
    [PrimaryKey] public string Id { get; set; } = Guid.NewGuid().ToString();
    public string Text { get; set; }
}


The Data  class is used to store a simple string. The Id  property will hold a
unique identfier with which we can easily find the record. Realm will make the
class implement INotifyPropertyChanged  during compilation, making the Text 
property bindable.

We then have a simple XAML page:

<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
    x:Class="App1.EditPage">
 
    <StackLayout>
        <Entry Text="{Binding Model.Text}"/>
 
        <Button Text="Save" Clicked="Button_Clicked"/>
    </StackLayout>
 
</ContentPage>


It contains an Entry  field and a Button  to save the entered text. In the
code-behind of the page we have the following code:

public partial class EditPage : ContentPage
{
    public EditPage (string id = "") // Make it possible to pass in the ID of an existing object
    {
        InitializeComponent ();
        BindingContext = this; // Make binding to itself possible
        
        _realm = Realm.GetInstance(); // Get an instance of the Realm database
        _transaction = _realm.BeginWrite(); // Create a transaction
        
        Model = _realm.Find<Data>(id) ?? _realm.Add(new Data()); // "Find" returns null if it can't find the object with the given ID
        
        this.Disappearing += EditPage_Disappearing;
    }
    
    public Data Model { get; set; } // The property to bind to
    
    private async void Button_Clicked(object sender, EventArgs e)
    {
        _transaction.Commit(); // Commit/save the changes when button is clicked
        await Navigation.PopAsync();
    }
    
    private void EditPage_Disappearing(object sender, EventArgs e)
    {
        _transaction.Dispose(); // Close the transaction when navigating away
    }
    
    private Realm _realm;
    private Transaction _transaction;
}


To start from the top: You can pass in the ID of an existing record to the
constructor. This will load the record with the given ID. If an invalid ID is
given (an empty string for example) _realm.Find()  will return null. In that
case we’ll create a new Data object and add it to Realm. _realm.Add()  returns
the newly added object.

We also keep track of the Transaction  returned by _realm.BeginWrite(). We use
this at two places later:

The first place is in Button_Clicked  where we handle clicks on the save button.
We call _transaction.Commit()  which will save all the changes we’ve done to the
Realm database. This includes added or removed objects, and changes to existing
objects.

The second place is in the EditPage_Disappearing  method where we call 
_transaction.Dispose(). This makes sure that we close the transaction. If you
don’t close the transaction, Realm will throw an exception when you try to
create new transactions later.

If you call _transaction.Dispose()  without calling _transaction.Commit() 
first, all the changes you’ve made will be discarded.

Using this technique you’re able to leverage both data binding and do changes
directly on the objec, while still maintaining the ability to discard all the
changes.

Happy coding!
