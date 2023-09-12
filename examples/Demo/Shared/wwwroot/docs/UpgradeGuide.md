﻿## Breaking changes
 The `FluentDataGrid` component is, as you may know, a `QuickGrid` in disguise. We 
 aligned the underlying code even more to the productized version that will ship with 
 .NET 8. Where we previously aligned parameter names to the `fluent-datagrid` Web 
 Component, we will now align to the `QuickGrid` naming. This should make 
 integrating/copying `QuickGrid` component examples in your own environment easier and 
 will make it easier for us to keep the code up-to-date. Changes that need to be made in parameter names from v2 are:  
* RowsData -> Items 
* RowsDataProvider -> ItemsProvider 
* RowsDataSize -> ItemSize 
* RowsDataKey -> ItemKey	

Another breaking change is found in the `Align` enumeration. Where we previously used the values `Left` and `Right`, these have now been changed to `Start` and `End`. This to make working with those easier/more consistent in an RTL-based application.
 
`FluentBadge` now uses a `Color` \ `BackgroundColor` combination to determine the fill values.
 
`FluentCalendar` no longer wraps the `fluent-calendar` web component. It's functionality was too limited. Not all parameters are supported in the updated version.
 
`StackHorizontalAlignment`/`StackVerticalAlignment` have been renamed to just `HorizontalAlignment`/`VerticalAlignment` as there are now more components using these enumarations.

## FluentComponentBase changes
The `FluentComponentBase` class now has an `Id` parameter. This has been lifted from the `FluentInputBase` class. 
The `Id` parameter is used to set the `id` attribute on the root element of the component. 
Previously, in the `FluentInputBase` class, a value was always created for the `Id`. This is no longer the case and 
a value is now only created for components that need to have an id. If needed, you can assign an value to the `Id` 
parameter yourself which will then be used as the 'id' on the root element.

## When using icons or emoji in version 2
We have changed the way icons and emoji are used in version 3. We published a [Icons](https://www.nuget.org/packages/Microsoft.Fast.Components.FluentUI.Icons) NuGet package
and a [Emoji](https://www.nuget.org/packages/Microsoft.Fast.Components.FluentUI.Emojis) NuGet package.   
The Fluent UI System Icons are a (still growing) collection of familiar, friendly and modern icons from Microsoft.
At the moment there are more than 2200 distinct icons available in both filled and outlined versions and in various sizes. In total the collections consists of well over 11k icons in SVG format.

After the following changes, usage is very simple:
```xml
<FluentIcon Icon="Icons.Regular.Size24.Save" />
```

There are a couple of changes that need to be done to upgrade from v2 to v3 when using icons/emoji:

### 1. Changes in `.csproj` file
For version 2 you needed to add a `<ProjectGroup>` similar to below to your project file:
```xml
<PropertyGroup>
	<PublishFluentIconAssets>true</PublishFluentIconAssets>
	<FluentIconSizes>10,12,16,20,24,28,32,48</FluentIconSizes>
	<FluentIconVariants>Filled,Regular</FluentIconVariants>
	<PublishFluentEmojiAssets>true</PublishFluentEmojiAssets>
	<FluentEmojiGroups>Activities,Animals_Nature,Flags,Food_Drink,Objects,People_Body,Smileys_Emotion,Symbols,Travel_Places</FluentEmojiGroups>
	<FluentEmojiStyles>Color,Flat,HighContrast</FluentEmojiStyles>
</PropertyGroup>
```
This whole group should be **removed** from your project file. See the [Project setup](https://www.fluentui-blazor.net/ProjectSetup)
for instructions on how to work with icons and emoji in version 3.

### 2. Changes in `Program.cs`
The `AddFluentUIComponents()` service collection extension needs to be changed. 
The `options.IconConfiguration` and `options.EmojiConfiguration` lines are no longer necessary and should be removed.

```csharp
builder.Services.AddFluentUIComponents(options =>
{
    options.HostingModel = {see remark below};   
});
```

The `options.HostingModel` setting is used to determine the type of project you are building. Choose a value from the `BlazorHostingModel` enumeration which reflects your type of project.

### 3. Changes in code
**ALL** occurences of `<FluentIcon>` and `<FluentEmoji>` should be checked and changed to work with the new format.

For icons the new format is like:
 
```razor
<FluentIcon Icon="@(Icons.Regular.Size24.Save)" />
```

> Names are structured as follows: `Icons.[IconVariant].[IconSize].[IconName]`.

For emoji the new format is:

```xml
<FluentEmoji Emoji="@(Emojis.PeopleBody.Color.Default.Artist)" />
```

> Names are structured as follows: `Emojis.[EmojiGroup].[EmojiStyle].[EmojiSkintone].[EmojiName]`.

📢 You can automate the changes by using the [Visual Studio Find and Replace functionality](https://learn.microsoft.com/en-us/visualstudio/ide/using-regular-expressions-in-visual-studio).

> Before to use **Find and Replace in Files**, you have to backup your project.
> The following steps are provided as an example and may not work in your case.
> You have to adapt them to your project.

- To search: `<FluentIcon Name="?@?FluentIcons\.(?<name>[^"]+)"? Size="?@?IconSize\.(?<size>[^"]+)"? Variant="?@?IconVariant\.(?<variant>[^"]+)"? Color="?@?Color\.(?<color>[^"]+)"? Slot="?(?<slot>[^"]+)"? />`
- To replace by: `<FluentIcon Icon="Icons.${variant}.${size}.${name}" Color="@Color.${color}" Slot="${slot}" />`

![Find and Replace](./_content/FluentUI.Demo.Shared/images/Icons-FindReplace-RegEx.png)

The preceding expressions are defined to search for attributes in this order: Name, Size, Variant, Color, Slot.
You may need to change or delete some attributes, depending on how you've coded them (and relaunch the process).

## Using the library from a RCL
For version 2 you had to add the following to your project file:
```xml
<Target Name="DisableAnalyzers" BeforeTargets="CoreCompile">
	<ItemGroup>
		<Analyzer Remove="@(Analyzer)" Condition="'%(Filename)' == 'Microsoft.Fast.Components.FluentUI.Configuration'" />
	</ItemGroup>
</Target>
```
This is no longer necessary. The code should be **removed** from your project file