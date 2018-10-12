+++
date = "09/16/2017 10:00:00 +00:00"
tags = ["ReactiveUI","C#"]
title = "Watch out for multiple subscriptions with BindCommand to event in RxUI"
+++
When using the handy binding methods in ReactiveUI, you might find yourself
using the very handy overload of BindCommand()  which let's you bind a command
to an event:

this.BindCommand(ViewModel, vm => vm.MyCommand, v => v.TextBox, nameof(TextBox.GotFocus));


You might even have it inside a WhenActivated()  in your view:

this.WhenActivated((CompositeDisposable d) =>
{
    this.BindCommand(ViewModel, vm => vm.MyCommand, v => v.TextBox, nameof(TextBox.GotFocus));
});


What you have  to remember is to dispose that BindCommand()  with the disposable
you're given inside WhenActivated().

Unlike regular command bindings, which just binds the command to the command
property of the control...

this.BindCommand(ViewModel, vm => vm.MyCommand, v => v.MyButton);


Command to event bindings adds an event listener to the event and executes the
command when the event fires. If you don't dispose this binding, BindCommand() 
will add a new event listener when navigating back to that view. This means
you'll have to event listeners, both executing the command, resulting in two
executions of the command.

This will of course repeat every time you navigate back to a view, and
reactivate it. The solution is to follow best practice when using 
WhenActivated()  and dispose the binding:

this.BindCommand(ViewModel, vm => vm.MyCommand, v => v.MyButton)
    .DisposeWith(d);


Happy coding!
