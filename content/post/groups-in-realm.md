+++
date = "2017-06-08T10:00:00.0000000+00:00"
tags = ["Realm"]
title = "Groups in Realm"
+++
In most databases you can group records by a field. That is not possible in Realm, however. Not at the moment at least. I'm guessing this is something they'll look to include in the future.

In the meantime however, I've made myself a workaround, as we computer nerds most often do. This solution has some limitations (as all workarounds do), but it might work for you too, so here goes.

A group is, in very simple terms, just a list of items and a key. So what we need is a class that can represent this. But first we need something to group:

```
public class JournalEntry : RealmObject
{
    [PrimaryKey]
    public string Id { get; set; } = Guid.NewGuid().ToString();
    public string Text { get; set; }
    public DateTimeOffset Date { get; set; }
    public DateGroup DateGroup { get; set; }
}
```

This is a simplified class in one of my projects. I need to group this type of class by the property `Date`. To do this I've created a special class to represent a group of `JournalEntry`s, called `DateGroup`. Let me talk you through the class:

```
public class DateGroup : RealmObject, INotifyCollectionChanged, IReadOnlyList<JournalEntry>
```

It inherits `RealmObject` to make it possible to refer to inside other `RealmObject`s. It implements `INotifyCollectionChanged` to make it possible to update the UI when the members of the group changes, and it implements `IReadOnlyList<JournalEntry>` to make sure you can use it as a list. This also makes it eligible for use in a `ListView` in either Xamarin.Forms, UWP or WPF.

```
public DateGroup() { }

public DateGroup(DateTimeOffset dateTimeOffset)
{
    Date = dateTimeOffset;
    Key = Date.Date.ToString();
}

[PrimaryKey]
public string Key { get; set; }
public DateTimeOffset Date { get; set; }
```

There's an empty constructor for convenience, but the important one is the second one. It takes a `DateTimeOffset` which is the basis of the group. The date is saved to make it easy to sort by later.

The date is also used as the key, representing the group. I'm using `DateTimeOffset.Date` as the basis of the key, because it allways returns the same time of day for a specific date.

```
[Backlink(nameof(JournalEntry.DateGroup))]
public IQueryable<JournalEntry> Items { get; }
```

A have a query which gets all the `JournalEntry`s which reference this group. This is the basis of the items in the group.

```
[Ignored]
public IRealmCollection<JournalEntry> Collection
{
    get
    {
        if (_collection == null)
        {
            _collection = Items.OrderBy(journalEntry => journalEntry.Date).AsRealmCollection();
            _collection.CollectionChanged += (s, e) => CollectionChanged?.Invoke(s, e);
        }
        return _collection;
    }
}

private IRealmCollection<JournalEntry> _collection;
```

This property is the magic glue that makes it work. `Collection` returns an `IRealmCollection<JournalEntry>` which is based on the `Items` query property.

It's lazily generated to ensure that the `RealmObject` is properly instantiated before we try to run the `Items` query.

It also makes sure that all `CollectionChanged` notifications are passed on from `_collection` to `DateGroup`. This ensures that UIs will be updated correctly.

```
public JournalEntry this[int index] { get { return Collection[index]; } }

public int Count => Collection.Count;

public event NotifyCollectionChangedEventHandler CollectionChanged;

public IEnumerator<JournalEntry> GetEnumerator() => Collection.GetEnumerator();

IEnumerator IEnumerable.GetEnumerator() => Collection.GetEnumerator();
```

The last part of the puzzle is the implementations of the Interfaces. The required parts from `IReadOnlyList<JournalEntry>` just references the `Collection` property, preventing me from doing any heavy work.

The `CollectionChanged` event is taken care of as explained above.

<details>
  <summary>See the complete `DateGroup` class</summary>
```
public class DateGroup : RealmObject, INotifyCollectionChanged, IReadOnlyList<JournalEntry>
{
    public DateGroup() { }

    public DateGroup(DateTimeOffset dateTimeOffset)
    {
        Date = dateTimeOffset;
        Key = Date.Date.ToString();
    }



    [PrimaryKey]
    public string Key { get; set; }
    public DateTimeOffset Date { get; set; }
    [Backlink(nameof(JournalEntry.DateGroup))]
    public IQueryable<JournalEntry> Items { get; }
[Ignored]
public IRealmCollection<JournalEntry> Collection
{
    get
    {
        if (_collection == null)
        {
            _collection = Items.OrderBy(journalEntry => journalEntry.Date).AsRealmCollection();
            _collection.CollectionChanged += (s, e) => CollectionChanged?.Invoke(s, e);
        }
        return _collection;
    }
}



    public JournalEntry this[int index] { get { return Collection[index]; } }

    public int Count => Collection.Count;

    public event NotifyCollectionChangedEventHandler CollectionChanged;

    public IEnumerator<JournalEntry> GetEnumerator() => Collection.GetEnumerator();

    IEnumerator IEnumerable.GetEnumerator() => Collection.GetEnumerator();



    private IRealmCollection<JournalEntry> _collection;
}
```
</details>

To add a `JournalEntry` to a `DateGroup` I have to set the `DateGroup` property on the `JournalEntry`. This has to be manually maintained, meaning it has to be updated every time the `Date` property is changed.

To get the correct `DateGroup`, I have an extension method for the `Realm` type:

```
public static DateGroup GetGroup(this Realm realm, DateTimeOffset dto)
{
    var key = dto.Date.ToString();
    return realm.Find<DateGroup>(key) ?? new DateGroup(dto);
}
```

First get the key for the given `DateTimeOffset`. Again, by using the `DateTimeOffset.Date` property I ensure that I'm always getting the same time of day for a specific date.

Then try to find a `DateGroup` with that key (this works since the `Key` property is also the `PrimaryKey` of the `RealmObject`). 

If `Find()` doesn't find a `DateGroup` with that key, it will return `null`. In which case I return a new `DateGroup` based on the passed `DateTimeOffset`.

To use it you query the realm for the `DateGroup`s:

```
var groupedList = realm.All<DateGroup>().OrderBy(group => group.Date).AsRealmCollection();
```

This will give you a list of lists. You set this as the `ItemsSource` of a `ListView` and set `ListView.IsGrouped = true` and your groups will work.

The best part is that the `ListView` will update when new items are added to any of the groups.

The main drawback of this technique is that you may end up with empty groups. If a date is set and later changed, you might have a group that has no items. How you solve this problem may vary. You could, for example, run a cleanup when setting a group. I ended up using a [derived collection from the ReactiveUI](https://docs.reactiveui.net/en/user-guide/lists/derived-lists.html) library.