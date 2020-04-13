---
title: 可重複使用的 EventToCommandBehavior
description: 行為可用來將命令與未設計於與命令互動的控制項建立關聯。 本文示範如何建立及使用 Xamarin.Forms 行為，在事件引發時叫用命令。
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: 292a6aaaea4fb0f84138e04c88f001c72ddd096d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "68650904"
---
# <a name="reusable-eventtocommandbehavior"></a>可重複使用的 EventToCommandBehavior

[![下載範例](~/media/shared/download.png)下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)

_行為可用於將命令與未設計為與命令交互的控制項相關聯。本文演示了創建和使用 Xamarin.Forms 行為,以在事件觸發時調用命令。_

## <a name="overview"></a>概觀

`EventToCommandBehavior` 類別是可重複使用的 Xamarin.Forms 自訂行為，其可執行命令以回應「任何」** 事件的引發。 默認情況下,事件的事件參數將傳遞給命令,並且可以[`IValueConverter`](xref:Xamarin.Forms.IValueConverter)通過 實現進行可選轉換。

您必須將下列行為屬性 (Property) 設定為使用行為：

- **EventName** – 行為所接聽的事件名稱。
- **Command** – 要執行的 `ICommand`。 該行為期望在`ICommand`附加控制項的上[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)找到實例,該實例可能從父元素繼承。

您也可以設定下列選用的行為屬性：

- **CommandParameter** – 要傳遞給命令的 `object`。
- **Converter** – [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 實作；當繫結引擎在 *source* 和 *target* 之間傳遞事件引數資料時，其可變更事件引數資料的格式。

> [!NOTE]
> `EventToCommandBehavior` 是自訂類別，並不屬於 Xamarin.Forms 的一部分；您可以在 [EventToCommand 行為範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior)中找到。

## <a name="creating-the-behavior"></a>建立行為

類`EventToCommandBehavior`派生自類`BehaviorBase<T>`, 而類又派生自[`Behavior<T>`](xref:Xamarin.Forms.Behavior`1)類。 `BehaviorBase<T>`類的目的是為任何 Xamarin.Forms 行為提供基類,這些[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)行為需要將行為設置為附加的控制項。 這可確保使用行為時，該行為可以繫結至 `Command` 屬性所指定的 `ICommand` 並加以執行。

類`BehaviorBase<T>`提供了[`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))一個可重寫的方法,用於設置行為[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)和清理[`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))`BindingContext`的重寫方法。 此外，該類別會在 `AssociatedObject` 屬性中儲存對附加控制項的參考。

### <a name="implementing-bindable-properties"></a>實作可繫結屬性

該`EventToCommandBehavior`類定義[`BindableProperty`](xref:Xamarin.Forms.BindableProperty)四 個實例,在事件觸發時執行使用者定義的命令。 下列程式碼範例顯示這些屬性：

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

使用 `EventToCommandBehavior` 類別時，`Command` 屬性應該是繫結至 `ICommand` 的資料，其執行目的為回應 `EventName` 屬性中所定義的引發事件。 該行為將期望找到附加控制項`ICommand`的[`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)上。

預設會將事件的事件引數傳遞給命令。 您可以將 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 實作指定為 `Converter` 屬性值，即可在繫結引擎於 *source* 和 *target* 之間傳遞這項資料時選擇性地轉換。 或者，您可以指定 `CommandParameter` 屬性值，將參數傳遞給命令。

### <a name="implementing-the-overrides"></a>實作覆寫

類別`EventToCommandBehavior`重寫[`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))`BehaviorBase<T>`類別[`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))的和方法,如以下代碼範例所示:

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

該方法[`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))通過調`RegisterEvent`用 方法執行設置`EventName`,將 屬性的值作為參數傳入。 該方法[`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))通過調用 方法`DeregisterEvent`執行清理`EventName`,將 屬性的值作為參數傳入。

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
- 否則,`Converter`如果屬性定義[`IValueConverter`](xref:Xamarin.Forms.IValueConverter)實現 ,則轉換器將執行,並在綁定引擎在*源**和目標*之間傳遞事件參數數據時轉換該資料。
- 否則，會假設事件引數為參數。

然後執行資料`ICommand`繫結,將參數傳遞給指令,前提[`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object))是方法`true`傳回 。

雖然此處未顯示,`EventToCommandBehavior`也`DeregisterEvent`包括[`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))由方法執行的方法。 `DeregisterEvent` 方法可用來找出並取消註冊 `EventName` 屬性中所定義的事件，以清除任何潛在的記憶體流失問題。

## <a name="consuming-the-behavior"></a>使用行為

類別`EventToCommandBehavior`可以附加到控制[`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)檔的集合,例如 XAML 程式碼範例的範例 :

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

行為`Command`的屬性是綁定到關聯`OutputAgeCommand`ViewModel 屬性`Converter``SelectedItemConverter`的資料,而該 屬性設定[`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)[`ListView`](xref:Xamarin.Forms.ListView)為實例[`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs),實體傳回的從傳回 。

在執行階段，行為會回應與控制項的互動。 在 中[`ListView`](xref:Xamarin.Forms.ListView)選擇 項目[`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected)時, 事件會觸發,這將在`OutputAgeCommand`ViewModel 中執行 。 反過來,這將更新綁定到的`SelectedItemText`[`Label`](xref:Xamarin.Forms.Label)ViewModel 屬性,如以下螢幕截圖所示:

[![](event-to-command-behavior-images/screenshots-sml.png "Sample Application with EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "Sample Application with EventToCommandBehavior")

使用此行為在事件引發時執行命令的優點為，可以將命令與未設計於要與命令互動的控制項建立關聯。 此外，這可從程式碼後置檔案中移除以定案效果方式處理的程式碼。

## <a name="summary"></a>總結

本文示範如何使用 Xamarin.Forms 行為，在事件引發時叫用命令。 行為可用來將命令與未設計於與命令互動的控制項建立關聯。

## <a name="related-links"></a>相關連結

- [Event to Command Behavior (Samples)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-eventtocommandbehavior) (命令行為事件 (範例))
- [行為](xref:Xamarin.Forms.Behavior)
- [行為&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
