+++
title = "Realm 101"
date = 2017-06-18T10:00:00.0000000+00:00
[taxonomies]
tags = ["Realm"]
+++
This is meant as a comprehensive introduction to Realm for use with Xamarin. While I am going to do this in Xamarin.Forms, it is very applicable to Xamarin.iOS, Xamarin.Android, UWP and WPF too. With a little imagination you could probably use this as a 101 for Swift, Java or JavaScript too.

In this tutorial were going to be building a simple Todo app, as is the custom these days. We are going to start from the very beginning by creating a Xamarin.Forms project, add the necessary nugets and then build the model classes, the UI and logic.

Along the way I will also explain why things are done a certain way. Some of the practices when working with Realm is a bit different than what you might be used to if you have used SQL-esque solutions before.

# The app

The app we are creating is just a simple todo application. The user will be able to create lists which contains multiple todos. The todos can also contain tasks, in case the user wants to separate a todo into smaller tasks.

## Creating the project

In this tutorial I am going to use Visual Studio for Mac. Some of the UI and wizards are a bit different than Visual Studio on Windows, but you should be able to find your way.

First we create a new project and choose Xamarin Forms application. I am going to be showing this on Android, so I have deselected iOS. We are going to call it *Realm101* and choose to use the shared project template.

This will generate some projects for us and make things ready.

## Adding Realm

Next, we need to add Realm to the mix. Open Nuget for the Realm101.Droid project by double clicking on *Packages* (or right clicking the project name and clicking on *Manage Nuget packages...* on Windows).

You will get multiple hits, but what you are interested in is the package named *Realm.Database*. This package only contains Realm for use locally on the device, which is what we are interested in. The *Realm* package also contains the features for automatic syncing with a server, but that is a tutorial for another time.

Realm will now be added to your project and is ready to use. In addition to the package there is also a new file in your project called *FodyWeavers.xml*. This is because Realm uses [Fody](https://github.com/Fody/Fody) to generate extra code inside your model classes. The generated code makes your models implement `INotifyPropertyChanged` among other things.

# Data in Realm 

A model is a class that represents a piece of information. Usually these classes are used to represent something that is stored in a database. A model represents a single record in the database.

In Realm the object instance of the model class is the actual thing that is stored in the database. There is no *conversion* or [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) behind the scenes.

All model classes that are to be used with Realm needs to inherit from `RealmObject` directly. Classes that inherit from a class that inherits from `RealmObject` is not officially supported.

The data in the model classes needs to be automatic properties, eg. not have custom getters and setters. I use the `prop` snippet in Visual Studio to generate the properties:

![prop snippet](/content/images/2017/06/prop-snippet-1.gif)

At the moment of writing the types supported by Realm are `bool`, `char`, `byte`, `short`, `int`, `long`, `float`, `double`, `string`, and `DateTimeOffset`. In addition it can also store any classes you create that inherit from `RealmObject`. The up-to-date list can be found in the [documentation](https://realm.io/docs/xamarin/latest/#supported-types).

## Model metadata / schema

In SQL databases you have to create a schema to describe how the data should be stored. In Realm the class itself is the schema. You can however give some extra information in you class to help Realm understand it.

### Primary key

In database terms, a primary key is a piece of a record (or multiple pieces) that can uniquely identify that record. It is, in essence, an id for the record.

In Realm the term primary key is also used to signify the id of a record. You specify that a property in you class is the primary key by using the `[PrimaryKey]` attribute. The type of the primary key can either be a `string` or a `long`.

I prefer to use a GUID as a string as an id. GUIDs are easy to create and convert to strings, and they are ensured to be unique inside our app.

```
[PrimaryKey]
public string Id { get; set; } = Guid.NewGuid().ToString();
```

By giving a class a primary key you can use that key to find a specific record. This is done by using the `Find()` method:

```
_todo = _realm.Find<Todo>("d4a48986-1a99-4652-ac3f-3289d0878e85");
```

Using `Find()` is the fastest and most efficient way to get a record in Realm.

### Indexed properties

Sometimes you will want to search the database (or query it, as it is often called). While you can do this with no modification to you model classes, you can make searches faster by indexing properties.

When you tell Realm to index a property, Realm will take special note of that property and make it faster when you search for something inside that property. You use the `[Indexed]` attribute:

```
[Indexed]
public string Title { get; set; }
```

While this sounds like a reason to index all the properties, it is not generally advised. When you index a property in the class, it takes a little bit longer when you add an object of that class to the database and the database uses a bit more storage on disk.

You will have to way the pros and cons and see what works best for your app, but for our todo app we are not going to use indexing at all. We are not going to use searching, so it is not needed.

### Ignored properties

Sometimes you do not want to store a property inside a class in the database. By marking it with the `[Ignored]` attribute you tell Realm to ignore it when storing the data in the database, but you will be able to use the property as normal.

```
[Ignored]
public string Details { get; set; }
```

## Referring to other objects

It is possible to reference other objects inside an object. This is done by simply creating a property of the type of the other object, as one would expect:

```
public Todo Parent { get; set; } // Where Todo is a RealmObject
```

### Lists

You can also keep a list of objects. You do this by declaring a getter only automatic property. The list will be created when you first use it. You can then use as you would normally.

```
public IList<Task> Tasks { get; }

// Somewhere else
var task = new Task();
myObject.Tasks.Add(task);
myObject.Tasks.Remove(task);
// etc...
```

It is also possible to keep a list by defining a *back link*. A back link is a special query maintained by Realm which will find all the objects that refer to this via one of their properties.

One object has a reference to another object like this:

```
// In Task.cs
public Todo Parent { get; set; }
```

```
// In Todo.cs
[Backlink(nameof(Task.Parent))]
public IQueryable<Task> Tasks { get; }
```

We set a `Todo` as the parent of a `Task`. We can then find all the `Task`s that have that `Todo` as their parent by using the `[BackLink()]` attribute.

If we add new `Task`s with the same `Todo` as their parent, the back link query will automatically update with the new results.

# The model classes in our app

In our app we are going to use three model classes:

## TodoList

This class will have a name and a list of `Todo`s. This way the user of the app can keep different categories or groups of `Todo`s.

<details>
 <summary>Expand to see code</summary>

```
public class TodoList : RealmObject
{
    [PrimaryKey]
    public string Id { get; set; } = Guid.NewGuid().ToString();
    public string Name { get; set; }
    [Backlink(nameof(Todo.Parent))]
    public IQueryable<Todo> Todos { get; }
}
```

</details>

## Todo

This class just has a title, some details and a due date. It will also have a property to tell which list it belongs to.

<details>
 <summary>Expand to see code</summary>

```
public class Todo : RealmObject
{
    [PrimaryKey]
    public string Id { get; set; } = Guid.NewGuid().ToString();
    public string Title { get; set; }
    public string Details { get; set; }
    public DateTimeOffset DueDate { get; set; }
    public TodoList Parent { get; set; }
    [Backlink(nameof(Task.Parent))]
    public IQueryable<Task> Tasks { get; }
}
```

</details>

## Task

The `Task` class represent a small unit of work that is part of a `Todo`. It also has a title, details, due date and a reference to the `Todo` it belongs to.

<details>
 <summary>Expand to see code</summary>

```
public class Task : RealmObject
{
    [PrimaryKey]
    public string Id { get; set; } = Guid.NewGuid().ToString();
    public string Title { get; set; }
    public string Details { get; set; }
    public DateTimeOffset DueDate { get; set; }
    public Todo Parent { get; set; }
}
```

</details>

# The UI
We are going to need a few pages to edit and display the lists, todos and tasks.

## TodoListsPage

This page will be the first screen in our app. It contains a list that shows all the `TodoList`s the user has created. It also allows the user to add a new list. The user can remove lists by pressing and holding on an item.

<details>
 <summary>Expand to see XAML</summary>

```
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Realm101.Pages.TodoListsPage">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Add" Clicked="AddToolbarItems_Clicked"/>
    </ContentPage.ToolbarItems>
	
    <ContentPage.Content>
        <Grid>
            <ListView x:Name="TodoListsListView" ItemTapped="TodoListsListView_ItemTapped">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Name}">
                            <TextCell.ContextActions>
                                <MenuItem Text="Remove" IsDestructive="true" CommandParameter="{Binding .}" Clicked="RemoveMenuItem_Clicked"/>
                            </TextCell.ContextActions>
                        </TextCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
            
            <Grid x:Name="AddTodoListGrid" IsVisible="false" VerticalOptions="Center" BackgroundColor="#eeeeee">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <Grid.RowDefinitions>
                    <RowDefinition/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                
                <Entry x:Name="AddTodoListEntry" Placeholder="Name" Grid.ColumnSpan="2"/>
                
                <Button Text="Add" Grid.Row="1" Clicked="AddTodoListAddButton_Clicked"/>
                
                <Button Text="Cancel" Clicked="AddTodoListCancelButton_Clicked" Grid.Row="1" Grid.Column="1"/>
            </Grid>
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

</details>

In the code-behind we have to fetch a reference to a realm. We do this by calling `Realm.GetInstance()`. This will give us the default instance a Realm. If the realm does not exist, it will be created automatically. This will create a file called `default.realm` on disk which contains all the data stored in the realm.

It is possible to pass a `RealmConfiguration` to `GetInstance()` to specify where on disk the realm file should be saved. You can also enable encryption of the realm file. This will slow realm down a little, but they claim it should be below 10%.

If you change model class after you have started using Realm, Realm has to update the internal schema of your classes. You can set how these changes should be handled by providing a method that will be run to migrate the data from the old format to the new.

During development you might end up changing the model classes a lot. You could just delete the realm file on disk or uninstall the app to force Realm to create a new realm file. The easier way however is to set `ShouldDeleteIfMigrationNeeded = true`. Realm will then automatically delete the realm file and create a new one if the model classes change.

We keep a reference to the realm as a private field in the class to easily access it later.

We then query the realm to get some records from it. To start a query we call `All()` on the realm. You specify the type of record you want in the angle brackets: `_realm.All<TodoList>()`. You can then filter and order the results by using `Where()` and `OrderBy()`/`OrderByDescending()`, respectively.

At the end of the query we call `AsRealmCollection()`. This converts it to a `IRealmCollection<TodoList>` which implements `INotifyCollectionChanged` which makes it behave like `ObservableCollection`. When we use the collection as the `ItemsSource` of a `ListView`, the `ListView` will automatically update when the collection updates.

The queries in Realm are live. This means that if you add a new `TodoList` to the realm, the query will automatically update to include the new `TodoList` as a result. This will in turn make the `IRealmCollection<TodoList>` update and notify the `ListView` that a new item has been added. The `ListView` then updates to reflect the change.

<details>
 <summary>Expand to see code-behind</summary>

```
public partial class TodoListsPage : ContentPage
{
    public TodoListsPage()
    {
        InitializeComponent();

        Title = "Realm101";
        TodoListsListView.ItemsSource = _realm.All<TodoList>().OrderBy(list => list.Name).AsRealmCollection();
    }

    void AddToolbarItems_Clicked(object sender, System.EventArgs e) => AddTodoListGrid.IsVisible = true;

    async void TodoListsListView_ItemTapped(object sender, Xamarin.Forms.ItemTappedEventArgs e)
    {
        await Navigation.PushAsync(new TodoListPage((e.Item as TodoList).Id));
    }

    void DoneMenuItem_Clicked(object sender, System.EventArgs e)
    {
        var todoList = (sender as MenuItem)?.CommandParameter as TodoList;
        if (todoList != null)
        {
            _realm.Write(() =>
            {
                foreach (var todo in todoList.Todos) _realm.Remove(todo);
                _realm.Remove(todoList);
            });
        }
    }

    void AddTodoListAddButton_Clicked(object sender, System.EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(AddTodoListEntry.Text))
        {
            _realm.Write(() => _realm.Add(new TodoList { Name = AddTodoListEntry.Text }));
            AddTodoListEntry.Text = "";
            AddTodoListGrid.IsVisible = false;
        }
    }

    void AddTodoListCancelButton_Clicked(object sender, System.EventArgs e)
    {
        AddTodoListEntry.Text = "";
        AddTodoListGrid.IsVisible = false;
    }

    Realm _realm = Realm.GetInstance();
}
```

</details>

## TodoListPage

This page will show all the `Todo`s in the selected `TodoList`. The user can also add new `Todo`s to the list. To mark a `Todo` as done, the user can press and hold on an item in the list.

<details>
 <summary>Expand to see XAML</summary>

```
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Realm101.Pages.TodoListPage">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Add" Clicked="AddToolbarItem_Clicked"/>
    </ContentPage.ToolbarItems>
    
	<ContentPage.Content>
        <Grid>
            <ListView x:Name="TodosListView" ItemTapped="TodosListView_ItemTapped">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Title}" Detail="{Binding DueDate, StringFormat='{0:d}'}">
                            <TextCell.ContextActions>
                                <MenuItem Text="Remove" CommandParameter="{Binding .}" Clicked="RemoveMenuitem_Clicked"/>
                            </TextCell.ContextActions>
                        </TextCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
            
            <Grid x:Name="AddTodoGrid" IsVisible="false" VerticalOptions="Center" BackgroundColor="#eeeeee">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <Grid.RowDefinitions>
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition Height="100"/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                
                <Entry x:Name="AddTodoEntry" Placeholder="Title" Grid.ColumnSpan="2"/>
                
                <DatePicker x:Name="AddTodoDatePicker" Grid.Row="1" Grid.ColumnSpan="2"/>
                
                <Editor x:Name="AddTodoEditor" Grid.Row="2" Grid.ColumnSpan="2"/>
                
                <Button Text="Add" Grid.Row="3" Clicked="AddTodoAddButton_Clicked"/>
                
                <Button Text="Cancel" Clicked="AddTodoCancelButton_Clicked" Grid.Row="3" Grid.Column="1"/>
            </Grid>
        </Grid>
	</ContentPage.Content>
</ContentPage>
```

</details>

We get passed the `Id` of the selected `TodoList` in the constructor. We then use this id to get the `TodoList` from the realm.

We get the `Todo`s from the `TodoList`, sort them by `DueDate` and convert it to a `IRealmCollection` to more easily use it.

Whenever we want to make changes to any object in the realm, it has to be done inside a *transaction*. This can most easily be done by using the `Write()` method. You pass an `Action` within which you can do changes to the realm.

Even if you want to make changes to a property on one of the objects that are stored in the realm, you will have to do it inside of a `Write()`. This is because the object writes directly to the database and multiple writes can not happen at the same time.

If you are going to make multiple changes in the realm, updating objects, adding objects or removing objects, etc., it is best to do it all inside a single transaction:

```
// Do not do this
foreach(var todo in todoList.Todos)
{
    _realm.Write(() => todo.Details = "Updated");
}

// DO THIS
_realm.Write(() =>
{
    foreach(var todo in todoList.Todos) todo.Details = "Updated";
});
```

<details>
 <summary>Expand to see code-behind</summary>

```
public partial class TodoListPage : ContentPage
{
    public TodoListPage(string todoListId)
    {
        InitializeComponent();

        _todoList = _realm.Find<TodoList>(todoListId);
        Title = _todoList.Name;
        TodosListView.ItemsSource = _todoList.Todos.OrderBy(todo => todo.DueDate).AsRealmCollection();
    }

    void AddToolbarItem_Clicked(object sender, System.EventArgs e) => AddTodoGrid.IsVisible = true;

    async void TodosListView_ItemTapped(object sender, Xamarin.Forms.ItemTappedEventArgs e)
    {
        await Navigation.PushAsync(new TodoPage((e.Item as Todo).Id));
    }

    void DoneMenuitem_Clicked(object sender, System.EventArgs e)
    {
        var todo = (sender as MenuItem)?.CommandParameter as Todo;
        if (todo != null)
        {
            _realm.Write(() =>
            {
                foreach (var task in todo.Tasks) _realm.Remove(task);
                _realm.Remove(todo);
            });
        }
    }

    void AddTodoAddButton_Clicked(object sender, System.EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(AddTodoEntry.Text))
        {
            _realm.Write(() => _realm.Add(new Todo { Title = AddTodoEntry.Text, DueDate = AddTodoDatePicker.Date, Details = AddTodoEditor.Text, Parent = _todoList }));
            AddTodoEntry.Text = "";
            AddTodoEditor.Text = "";
            AddTodoDatePicker.Date = DateTimeOffset.Now.Date;
            AddTodoGrid.IsVisible = false;
        }
    }

    void AddTodoCancelButton_Clicked(object sender, System.EventArgs e)
    {
        AddTodoEntry.Text = "";
        AddTodoEditor.Text = "";
        AddTodoDatePicker.Date = DateTimeOffset.Now.Date;
        AddTodoGrid.IsVisible = false;
    }

    Realm _realm = Realm.GetInstance();
    TodoList _todoList;
}
```

</details>

## TodoPage

This page shows the details of a `Todo`. It also lets the user add `Task`s to the `Todo`.

<details>
 <summary>Expand to see XAML</summary>

```
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="Realm101.Pages.TodoPage">
	<ContentPage.Content>
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="100"/>
                <RowDefinition Height="Auto"/>
                <RowDefinition/>
            </Grid.RowDefinitions>
            
            <Label Text="{Binding DueDate, StringFormat='{0:d}'}"/>
            
            <Label Text="{Binding Details}" Grid.Row="1"/>
            
            <Button Text="Add task" HorizontalOptions="End" Grid.Row="2" Clicked="AddTaskButton_Clicked"/>
            
            <ListView x:Name="TasksListView" Grid.Row="3">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <TextCell Text="{Binding Title}" Detail="{Binding DueDate, StringFormat='{0:d}'}">
                            <TextCell.ContextActions>
                                <MenuItem Text="Remove" IsDestructive="true" CommandParameter="{Binding .}" Clicked="RemoveMenuItem_Clicked"/>
                            </TextCell.ContextActions>
                        </TextCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
            
            <Grid x:Name="AddTaskGrid" IsVisible="false" VerticalOptions="Center" BackgroundColor="#eeeeee" Grid.RowSpan="4">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <Grid.RowDefinitions>
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition Height="100"/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                
                <Entry x:Name="AddTaskEntry" Placeholder="Title" Grid.ColumnSpan="2"/>
                
                <DatePicker x:Name="AddTaskDatePicker" Grid.Row="1" Grid.ColumnSpan="2"/>
                
                <Editor x:Name="AddTaskEditor" Grid.Row="2" Grid.ColumnSpan="2"/>
                
                <Button Text="Add" Grid.Row="3" Clicked="AddTaskAddButton_Clicked"/>
                
                <Button Text="Cancel" Clicked="AddTaskCancelButton_Clicked" Grid.Row="3" Grid.Column="1"/>
            </Grid>
        </Grid>
	</ContentPage.Content>
</ContentPage>
```

</details>

Once again we are passed the `Id` of an object in the realm, which we get by using `Find()`. At the same time we also set the `Todo` as the `BindingContext` to make it easy to bind to it in the XAML.

The reason I pass the `Id` of the object, instead of the object itself, is because objects from a realm can only be used on the thread the realm was opened. It is not necessary worry about that in our app, because everything is happening on the UI thread, but it might be something to be aware of in larger apps.

You can always open a realm (by calling `Realm.GetInstance()`) on a different thread and do all the things you want to do there. Calling `GetInstance()` costs next to nothing and can be done without worrying about performance costs.

<details>
 <summary>Expand to see code-behind</summary>

```
public partial class TodoPage : ContentPage
{
    public TodoPage(string todoId)
    {
        InitializeComponent();

        BindingContext = _todo = _realm.Find<Todo>(todoId);
        Title = _todo.Title;
        TasksListView.ItemsSource = _realm.All<Task>().OrderBy(task => task.DueDate).AsRealmCollection();
    }

    void AddTaskButton_Clicked(object sender, System.EventArgs e) => AddTaskGrid.IsVisible = true;

    void DoneMenuItem_Clicked(object sender, System.EventArgs e)
    {
        var task = (sender as MenuItem)?.CommandParameter as Task;
        if (task != null)
        {
            _realm.Write(() => _realm.Remove(task));
        }
    }

    void AddTaskAddButton_Clicked(object sender, System.EventArgs e)
    {
        if (!string.IsNullOrWhiteSpace(AddTaskEntry.Text))
        {
            _realm.Write(() => _realm.Add(new Task { Title = AddTaskEntry.Text, DueDate = AddTaskDatePicker.Date, Details = AddTaskEditor.Text, Parent = _todo }));
            AddTaskEntry.Text = "";
            AddTaskEditor.Text = "";
            AddTaskDatePicker.Date = DateTimeOffset.Now.Date;
            AddTaskGrid.IsVisible = false;
        }
    }

    void AddTaskCancelButton_Clicked(object sender, System.EventArgs e)
    {
        AddTaskEntry.Text = "";
        AddTaskEditor.Text = "";
        AddTaskDatePicker.Date = DateTimeOffset.Now.Date;
        AddTaskGrid.IsVisible = false;
    }

    Realm _realm = Realm.GetInstance();
    Todo _todo;
}
```

</details>

# In summary

Realm is a fast, mobile-friendly and easy-to-use database designed for the modern era. I think it is a superior alternative to most SQLite based databases. I am not alone in thinking this; big companies like Netflix, Google and Intel have chosen to use Realm in some of their apps. Netflix even wrote a [nice blog post](https://medium.com/netflix-techblog/netflix-downloads-on-android-d79db40f1732) about it.

If you have any questions feel free to contact me on [Twitter](https://twitter.com/jonstodle) or, even better, join the [Xamarin Chat Slack team](https://xamarinchat.herokuapp.com/) where there is a dedicated channel to Realm. The manager of Realm Xamarin also hangs out there.

The whole app is up on [my Github](https://github.com/jonstodle/realm101) if you want to play around.

Happy coding!
