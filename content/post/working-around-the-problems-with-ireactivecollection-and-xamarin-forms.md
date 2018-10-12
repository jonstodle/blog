+++
date = "2017-02-28T11:00:00.0000000+00:00"
tags = ["Xamarin","Xamarin Forms"]
title = "Working around the problems with IReactiveCollection and Xamarin.Forms"
+++
I love ReactiveUI [http://reactiveui.net/]. Ever since I first stumbled across
it after learning some rudamentary ReactiveExtensions [http://reactivex.io/] 
I’ve used it for all my projects. And as so many users of ReactiveUI says: I
don’t how to make an app without it™.

I’ve also grown somewhat fond of Xamarin.Forms [https://www.xamarin.com/forms]:
It makes it possible to develop apps for iOS, Android, Windows Phone and UWP in
one go. You write code in C# and lay out the UI in XAML and it compiles
automagically to native UI on each platform.

The next logical step is of course to unite the two. This works incredibly well.
ReactiveUI has excellent support for almost all .Net platforms, and that
includes the Xamarin family.

Among all these successes, there is one hurdle that a lot of Xamarin.Forms users
stumble across when using ReactiveUI: ReactiveUI’s ReactiveList, which is the
framework’s version of ObservableCollection  with some Rx goodness, has some
strange bugs when using it as a source for a ListView. The same problem can be
observed when using IReactiveDerivedList, the awesome sibling of ReactiveList.

Because of the way ReactiveList  and Xamarin.Forms is written, the change
notification for changes in the list isn’t handled by the Xamarin.Forms ListView
. This manifests itself by new items not appearing, removed items don’t
disappear and sometimes app crashes.

There is an open issue [https://github.com/reactiveui/ReactiveUI/issues/806]  on
the subject on Github, but it’s been 1.5 years and there’s not yet a proper
solution. There is also an open issue
[https://bugzilla.xamarin.com/show_bug.cgi?id=31415]  with Xamarin to help fix
the problem.

But, as programmers, we persevere and find an alternate solution. A member of
the community posted a workaround  he was using to get around the problem. I
looked at that example and tried doing my own version. After trying a couple of
times, I ended up with what he had done (apart from coding style differences, I
think the solutions differ by two lines of code).

The solution is basically to wrap the IReactiveCollection  (which is one of the
fundamental interfaces of ReactiveList) in a class which republishes change
notfications on behalf of the source collection.

I also added an extension method to easily convert IReactiveLists  and 
IReactiveDerivedLists  to the new wrapper class: ReactiveObservableList. My
implementation is, as I said, heavily inspired by this:

using System;
using System.Collections;
using System.Collections.Generic;
using System.Collections.Specialized;
using System.Reactive.Disposables;

namespace ReactiveUI
{
    public static class ReactiveUIExtensions
    {
        public static IEnumerable ToReactiveObservableList(this IReactiveCollection source) => new ReactiveObservableList(source);
    }

    public class ReactiveObservableList : INotifyCollectionChanged, IEnumerable, IDisposable
    {
        public ReactiveObservableList(IReactiveCollection sourceCollection)
        {
            _collection = sourceCollection ?? new ReactiveList(); // In some binding scenarios the sourceCollection passed might be null, set an empty list instead

            _collection.Changed.Subscribe(args => CollectionChanged?.Invoke(this, args)).DisposeWith(_disposables);
        }



        public event NotifyCollectionChangedEventHandler CollectionChanged;



        public IEnumerator GetEnumerator() => _collection.GetEnumerator();

        IEnumerator IEnumerable.GetEnumerator() => _collection.GetEnumerator();

        public void Dispose() => _disposables.Dispose();



        private IReactiveCollection _collection;
        private CompositeDisposable _disposables = new CompositeDisposable();
    }
}
