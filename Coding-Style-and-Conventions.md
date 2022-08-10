In general, the project follows the [C# Coding Convention](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/inside-a-program/coding-conventions) and [Framework Design Guidelines](https://docs.microsoft.com/en-us/dotnet/standard/design-guidelines/). We use tools like StyleCop to help enforce some of the rules mentioned below. 

### Enums
* Always use `Unknown` at index 0 for return types that may have a value that is not known
* Always use `Default` at index 0 for option types that can use the system default option
* Follow naming guidelines for tense... `SensorSpeed` not `SensorSpeeds`
* Assign values (0,1,2,3) for all enums

### Property Names
* Include units only if one of the platforms includes it in their implementation. For instance HeadingMagneticNorth implies degrees on all platforms, but PressureInHectopascals is needed since platforms don't provide a consistent API for this.

### Units
* Use the standard units and most well accepted units when possible. For instance Hectopascals are used on UWP/Android and iOS uses Kilopascals so we have chosen Hectopascals.

### Pattern matching

#### Null checking
* Prefer using `is` when checking for null instead of `==`.

e.g. 

```csharp
// null
if (something is null)
{

}

// or not null
if (something is not null)
{
   
}
```

* Avoid using the `!` [null forgiving operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/null-forgiving) to avoid the unintended introduction of bugs.

#### Type checking

* Prefer `is` when checking for types instead of casting.

e.g.

```csharp
if (something is Bucket bucket)
{
   bucket.Empty();
}
```

### File Scoped Namespaces
* Use [file scoped namespaces](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/proposals/csharp-10.0/file-scoped-namespaces) to help reduce code verbosity.

e.g. 

```csharp
namespace CommunityToolkit.Maui.Converters;

using System;

class BoolToObjectConverter
{
}
```

### Braces

Please use `{ }` after `if`, `for`, `foreach`, `do`, `while`, etc.

e.g.

```csharp
if (something is not null)
{
   ActOnIt();
}
```

### `NotImplementedException`

Please avoid adding new code that throws a `NotImplementedException`. According to the [Microsoft Docs](https://docs.microsoft.com/dotnet/api/system.notimplementedexception), we should only "throw a `NotImplementedException` exception in properties or methods in your own types when that member is still in development and will only later be implemented in production code. In other words, a NotImplementedException exception should be synonymous with 'still in development.'"

In other words, `NotImplementedException` implies that a feature is still in development, indicating that the Pull Request is incomplete.