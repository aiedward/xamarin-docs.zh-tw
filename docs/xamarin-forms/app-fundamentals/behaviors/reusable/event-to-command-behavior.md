---
title: 可重複使用的 EventToCommandBehavior
description: 行為可用來將命令與未設計於與命令互動的控制項建立關聯。 本文示範如何建立和使用 Xamarin.Forms 行為，以便在事件引發時叫用命令。
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 46d1566c89de763a469f30ce8ed2c6ef919f1426
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135794"
---
# <a name="reusable-eventtocommandbehavior"></a>可重複使用的 EventToCommandBehavior

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_行為可以用來將命令與未設計成與命令互動的控制項產生關聯。本文示範如何建立和使用 Xamarin.Forms 行為，以便在事件引發時叫用命令。_

## <a name="overview"></a>概觀

`EventToCommandBehavior`類別是可重複使用 Xamarin.Forms 的自訂行為，會執行命令以回應*任何*事件引發。 根據預設，事件的事件引數將會傳遞至命令，並可選擇性地透過實作為轉換 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 。

您必須將下列行為屬性 (Property) 設定為使用行為：

- **EventName** – 行為所接聽的事件名稱。
- **Command** – 要執行的 `ICommand`。 行為預期會 `ICommand` 在附加控制項的上尋找實例 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) ，這可能是從父元素繼承而來。

您也可以設定下列選用的行為屬性：

- **CommandParameter** – 要傳遞給命令的 `object`。
- **Converter** – [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 實作；當繫結引擎在 *source* 和 *target* 之間傳遞事件引數資料時，其可變更事件引數資料的格式。

> [!NOTE]
> `EventToCommandBehavior`是自訂類別，可以位於[EventToCommand 行為範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)中，而不是的一部分 Xamarin.Forms 。

## <a name="creating-the-behavior"></a>建立行為

`EventToCommandBehavior`類別衍生自類別 `BehaviorBase<T>` ，而後者又衍生自 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別。 類別的目的 `BehaviorBase<T>` 是要提供基類，讓 Xamarin.Forms 要求的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 行為必須設定為附加控制項的任何行為。 這可確保使用行為時，該行為可以繫結至 `Command` 屬性所指定的 `ICommand` 並加以執行。

`BehaviorBase<T>`類別提供可覆寫的 [ `OnAttachedTo` ] （x： Xamarin.Forms 。行為 `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method that sets the [` BindingCoNtext `](xref:Xamarin.Forms.BindableObject.BindingContext) of the behavior and an overridable [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom （ Xamarin.Forms 。BindableObject））方法來清除 `BindingContext` 。 此外，該類別會在 `AssociatedObject` 屬性中儲存對附加控制項的參考。

### <a name="implementing-bindable-properties"></a>實作可繫結屬性

`EventToCommandBehavior`類別會定義四個 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 實例，當事件引發時，會執行使用者定義的命令。 下列程式碼範例顯示這些屬性：

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  public static readonly BindableProperty EventNameProperty =
    BindableProperty.Create ("EventName", typeof(string), typeof(EventToCommandBehavior), null, propertyChanged: OnEventNameChanged);
  public static readonly BindableProperty CommandProperty =
    BindableProperty.Create ("Command", typeof(ICommand), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty CommandParameterProperty =
    BindableProperty.Create ("CommandParameter", typeof(object), typeof(EventToCommandBehavior), null);
  public static readonly BindableProperty InputConverterProperty =
    BindableProperty.Create ("Converter", typeof(IValueConverter), typeof(EventToCommandBehavior), null);

  public string EventName { ... }
  public ICommand Command { ... }
  public object CommandParameter { ... }
  public IValueConverter Converter { ...  }
  ...
}
```

使用 `EventToCommandBehavior` 類別時，`Command` 屬性應該是繫結至 `ICommand` 的資料，其執行目的為回應 `EventName` 屬性中所定義的引發事件。 行為預期會 `ICommand` 在附加控制項的上找到 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 。

預設會將事件的事件引數傳遞給命令。 您可以將 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 實作指定為 `Converter` 屬性值，即可在繫結引擎於 *source* 和 *target* 之間傳遞這項資料時選擇性地轉換。 或者，您可以指定 `CommandParameter` 屬性值，將參數傳遞給命令。

### <a name="implementing-the-overrides"></a>實作覆寫

`EventToCommandBehavior`類別會覆寫 [ `OnAttachedTo` ] （x： Xamarin.Forms 。行為 `1.OnAttachedTo(Xamarin.Forms.BindableObject)) and [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom （ Xamarin.Forms 。BindableObject））類別的方法 `BehaviorBase<T>` ，如下列程式碼範例所示：

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  protected override void OnAttachedTo (View bindable)
  {
    base.OnAttachedTo (bindable);
    RegisterEvent (EventName);
  }

  protected override void OnDetachingFrom (View bindable)
  {
    DeregisterEvent (EventName);
    base.OnDetachingFrom (bindable);
  }
  ...
}
```

[ `OnAttachedTo` ] （X： Xamarin.Forms 。行為 `1.OnAttachedTo(Xamarin.Forms.BindableObject)) method performs setup by calling the ` RegisterEvent ` method, passing in the value of the ` 事件 ` property as a parameter. The [` OnDetachingFrom `](xref:Xamarin.Forms.Behavior` 1. OnDetachingFrom （ Xamarin.Forms 。BindableObject））方法會藉由呼叫方法來執行清除 `DeregisterEvent` ，並傳入屬性的值 `EventName` 做為參數。

### <a name="implementing-the-behavior-functionality"></a>實作行為功能

行為的目的是執行 `Command` 屬性所定義命令，以回應 `EventName` 屬性所定義的引發事件。 下列程式碼範例顯示核心行為功能：

```csharp
public class EventToCommandBehavior : BehaviorBase<View>
{
  ...
  void RegisterEvent (string name)
  {
    if (string.IsNullOrWhiteSpace (name)) {
      return;
    }

    EventInfo eventInfo = AssociatedObject.GetType ().GetRuntimeEvent (name);
    if (eventInfo == null) {
      throw new ArgumentException (string.Format ("EventToCommandBehavior: Can't register the '{0}' event.", EventName));
    }
    MethodInfo methodInfo = typeof(EventToCommandBehavior).GetTypeInfo ().GetDeclaredMethod ("OnEvent");
    eventHandler = methodInfo.CreateDelegate (eventInfo.EventHandlerType, this);
    eventInfo.AddEventHandler (AssociatedObject, eventHandler);
  }

  void OnEvent (object sender, object eventArgs)
  {
    if (Command == null) {
      return;
    }

    object resolvedParameter;
    if (CommandParameter != null) {
      resolvedParameter = CommandParameter;
    } else if (Converter != null) {
      resolvedParameter = Converter.Convert (eventArgs, typeof(object), null, null);
    } else {
      resolvedParameter = eventArgs;
    }        

    if (Command.CanExecute (resolvedParameter)) {
      Command.Execute (resolvedParameter);
    }
  }
  ...
}
```

執行 `RegisterEvent` 方法以回應要附加至控制項的 `EventToCommandBehavior`，其會接受 `EventName` 屬性值作為參數。 此方法即會嘗試在附加控制項上找出 `EventName` 屬性中所定義的事件。 只要找得到該事件，就會將 `OnEvent` 方法註冊為事件的處理常式方法。

執行 `OnEvent` 方法以回應 `EventName` 屬性中定義的引發事件。 只要 `Command` 屬性參考有效的 `ICommand`，此方法就會嘗試擷取要傳遞給 `ICommand` 的參數，如下所示：

- 如果 `CommandParameter` 屬性定義了參數，就會擷取該參數。
- 否則，如果 `Converter` 屬性定義了 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 執行，則會執行轉換器，並在系結引擎于*來源*與*目標*之間傳遞事件引數資料時加以轉換。
- 否則，會假設事件引數為參數。

`ICommand`接著會執行資料系結，並將參數傳遞給命令，前提是該 [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) 方法會傳回 `true` 。

雖然此處未顯示，但 `EventToCommandBehavior` 也包含 `DeregisterEvent` 由 [ `OnDetachingFrom` ] （x：）執行的方法 Xamarin.Forms 。行為 `1.OnDetachingFrom(Xamarin.Forms.BindableObject)) method. The ` DeregisterEvent ` method is used to locate and deregister the event defined in the ` 事件名稱 ' 屬性，用以清除任何可能的記憶體流失。

## <a name="consuming-the-behavior"></a>使用行為

`EventToCommandBehavior`類別可以附加至 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 控制項的集合，如下列 XAML 程式碼範例所示：

```xaml
<ListView ItemsSource="{Binding People}">
    <ListView.ItemTemplate>
        <DataTemplate>
            <TextCell Text="{Binding Name}" />
        </DataTemplate>
    </ListView.ItemTemplate>
    <ListView.Behaviors>
        <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}" Converter="{StaticResource SelectedItemConverter}" />
    </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
var listView = new ListView();
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "People");
listView.ItemTemplate = new DataTemplate(() =>
{
    var textCell = new TextCell();
    textCell.SetBinding(TextCell.TextProperty, "Name");
    return textCell;
});
listView.Behaviors.Add(new EventToCommandBehavior
{
    EventName = "ItemSelected",
    Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
    Converter = new SelectedItemEventArgsToSelectedItemConverter()
});

var selectedItemLabel = new Label();
selectedItemLabel.SetBinding(Label.TextProperty, "SelectedItemText");
```

`Command`行為的屬性會將資料系結至 `OutputAgeCommand` 相關聯 ViewModel 的屬性，而 `Converter` 屬性會設定為 `SelectedItemConverter` 實例，這會 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem) [`ListView`](xref:Xamarin.Forms.ListView) 從 [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) 傳回的。

在執行階段，行為會回應與控制項的互動。 在中選取專案時 [`ListView`](xref:Xamarin.Forms.ListView) ， [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 將會引發事件，這會 `OutputAgeCommand` 在 ViewModel 中執行。 接著，這會更新系結 `SelectedItemText` 至的 ViewModel 屬性 [`Label`](xref:Xamarin.Forms.Label) ，如下列螢幕擷取畫面所示：

[![](event-to-command-behavior-images/screenshots-sml.png "Sample Application with EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "Sample Application with EventToCommandBehavior")

使用此行為在事件引發時執行命令的優點為，可以將命令與未設計於要與命令互動的控制項建立關聯。 此外，這可從程式碼後置檔案中移除以定案效果方式處理的程式碼。

## <a name="summary"></a>摘要

本文示範 Xamarin.Forms 如何使用行為，在事件引發時叫用命令。 行為可用來將命令與未設計於與命令互動的控制項建立關聯。

## <a name="related-links"></a>相關連結

- [Event to Command Behavior (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior) (命令行為事件 (範例))
- [行為](xref:Xamarin.Forms.Behavior)
- [行為 &lt; T&gt;](xref:Xamarin.Forms.Behavior`1)
