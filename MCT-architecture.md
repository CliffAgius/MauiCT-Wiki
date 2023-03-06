### Overview

Today we've two projects on our repo, they are the `Core` and `Maui` projects, which will be called `Controls` in this article.

This architecture was idealized to follow the .NET MAUI architecture, having a better separation and allowing the code to be used by other flavors of Toolkits. Our toolkit is based on the MVVM flavor, but we can have toolkits around Blazor, Comet, and any other flavor of .NET MAUI that can be created in the future. To understand this idea better let's take a look into each project and its responsibilities.

If you want to have more context around the conversion of this structure you can see [#213](https://github.com/CommunityToolkit/Maui/issues/213). You can see the general architecture in the image below.

![image](https://user-images.githubusercontent.com/20712372/223001050-b6b08c64-0aa4-4e9c-b675-58095b03a08a.png)


### Core

The `Core` project has all the agnostic code in our lib, it shouldn't be opinionated, so any other project that can take reference on it can implement its own style and opinions. To make it clear see this _wrong_ example around a  `Button` control, that's a universal concept.

```csharp
public interface IButton
{
	ICommand ClickCommand;
	EventHandler Clicked;
}
```

That's wrong because `ICommand` is a pattern followed by MVVM and Reactive, for example, doesn't have that concept. In the second we have an `EventHandler` for the Clicked action, again we're forcing the implementor to use an `EventHandler` and that can break the desired pattern. So what will be the solution? Well pretty simple, we will use something that's common for all C# vanilla approaches.

```csharp
public interface IButton
{
	void OnClick();
}
```

So we just use a `void method` to represent the Click action, with that the implementor can abstract it on their desired pattern, doesn't matter if it's a `Command`, an `event`, or any other pattern. So keep that in mind if you need to implement something on the `Core` project.

Also, on `Core` we add the primitive values, like `structs`, `EventArgs`, `Handlers`, and Platform controls that will be used by `Handlers`.

In order to keep the `Core` project even more agnostic we **don't** use the `Microsoft.Maui.Controls` namespace. The explanation is very simple, everything on this namespace is MVVM opinionated so doesn't make sense to use it on our `Core` project. So, if you need to depend on something that lives on the `Microsoft.Maui.Controls` namespace it shouldn't be on `Core`.

To finish on this subject, the `Core` project is our layer that is intended to be used by other libraries.

### Controls

The `Controls` project is where all opinionated implementation lives, talking about this toolkit that's MVVM opinionated, all the code will have that in mind, and, of course, we can use the `Microsoft.Maui.Controls` namespace.

This project implements the `Core` abstractions, so we create the shared controls and connect them to the respective handler, and we can extend the handler by adding features that will not suit the `Core` layer, for example, something that's platform-specific or something that we feel that isn't needed for all flavors of .NET MAUI.

On the `Controls` project we implement also some concepts that also just exist in the `Microsoft.Maui.Controls` namespace, like `Behaviors`, `PlatformBehaviors`, `Converters` etc. Those shouldn't be on `Core` since they can't exist in other .NET MAUI flavors.

In the snippet below you can see an example of an implementation of the `IButton` interface defined on `Core`.

```csharp
public class Button : View, IButton
{
	public static readonly BindableProperty ClickCommandProperty = 
		BindableProperty.Create(nameof(ClickCommand), typeof(ICommand), typeof(Button));

	public ICommand ClickCommand
	{
		get => (ICommand)GetValue(ClickCommandProperty);
		set => SetValue(ClickCommandProperty, value);
	}

	IButton.OnClick()
	{
		ClickCommand?.Invoke();
	}
}
```

So as you can see, here we take advantage of the `Bindings` structure and the `ICommand`  concept that's very MVVM opinionated.