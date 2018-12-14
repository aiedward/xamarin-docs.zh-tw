---
title: 可重複使用的 EventToCommandBehavior
description: 針對設計用意並非與命令互動的控制項，您可以使用行為來建立其與命令的關聯。 本文示範如何建立及使用 Xamarin.Forms 行為，在事件引發時叫用命令。
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: 8bf8f86cf708806d1c17b3fe4eda0755f98fd646
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563182"
---
# <a name="reusable-eventtocommandbehavior"></a>可重複使用的 EventToCommandBehavior

_針對設計用意並非與命令互動的控制項，您可以使用行為來建立其與命令的關聯。本文示範如何建立及使用 Xamarin.Forms 行為，在事件引發時叫用命令。_

## <a name="overview"></a>總覽

`EventToCommandBehavior` 類別是可重複使用的 Xamarin.Forms 自訂行為，其可執行命令以回應「任何」事件的引發。 預設會將事件的事件引數傳遞給命令，並可由 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 實作選擇性地轉換。

您必須將下列行為屬性設定為使用行為：

- **EventName** – 行為所接聽的事件名稱。
- **Command** – 要執行的 `ICommand`。 行為預期會在附加控制項的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 上找到 `ICommand` 執行個體，其可能會從父項目繼承。

您也可以設定下列選用的行為屬性：

- **CommandParameter** – 要傳遞給命令的 `object`。
- **Converter** – [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 實作；當繫結引擎在 *source* 和 *target* 之間傳遞事件引數資料時，其可變更事件引數資料的格式。

> [!NOTE]
> `EventToCommandBehavior` 是自訂類別，並不屬於 Xamarin.Forms 的一部分；您可以在 [EventToCommand 行為範例](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)中找到。

## <a name="creating-the-behavior"></a>建立行為

`EventToCommandBehavior` 類別衍生自 `BehaviorBase<T>` 類別，而後者又會衍生自 [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 類別。 `BehaviorBase<T>` 類別的目的是為任何 Xamarin.Forms 行為提供基底類別，而這些 Xamarin.Forms 行為需要將行為的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 設為附加控制項。 這可確保使用行為時，該行為可以繫結至 `Command` 屬性所指定的 `ICommand` 並加以執行。

`BehaviorBase<T>` 類別會提供可覆寫的 [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 方法以設定行為的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext)，和可覆寫的 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法以清除 `BindingContext`。 此外，該類別會在 `AssociatedObject` 屬性中儲存對附加控制項的參考。

### <a name="implementing-bindable-properties"></a>實作可繫結屬性

`EventToCommandBehavior` 類別會定義四個 [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) 執行個體，以在事件引發時執行使用者定義的命令。 下列程式碼範例顯示這些屬性：

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

使用 `EventToCommandBehavior` 類別時，`Command` 屬性應該是繫結至 `ICommand` 的資料，其執行目的為回應 `EventName` 屬性中所定義的引發事件。 行為預期會在附加控制項的 [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) 上找到 `ICommand`。

預設會將事件的事件引數傳遞給命令。 您可以將 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 實作指定為 `Converter` 屬性值，即可在繫結引擎於 *source* 和 *target* 之間傳遞這項資料時選擇性地轉換。 或者，您可以指定 `CommandParameter` 屬性值，將參數傳遞給命令。

### <a name="implementing-the-overrides"></a>實作覆寫

`EventToCommandBehavior` 類別可覆寫 `BehaviorBase<T>` 類別的 [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 和 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法，如下列程式碼範例所示：

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

[`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject)) 方法會呼叫 `RegisterEvent` 方法，並將 `EventName` 屬性作為參數值傳入，以執行設定作業。 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法會呼叫 `DeregisterEvent` 方法，並將 `EventName` 屬性作為參數值傳入，以執行清除作業。

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
- 否則，如果 `Converter` 屬性定義了 [`IValueConverter`](xref:Xamarin.Forms.IValueConverter) 實作，即會執行轉換器，並當繫結引擎在 *source* 和 *target* 之間傳遞事件引數資料時加以轉換。
- 否則，會假設事件引數為參數。

接著，只要 [`CanExecute`](xref:Xamarin.Forms.Command.CanExecute(System.Object)) 方法傳回 `true`，即會執行資料與 `ICommand` 的繫結，並將參數傳遞給命令。

雖然此處未顯示，但 `EventToCommandBehavior` 也包含由 [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject)) 方法執行的 `DeregisterEvent` 方法。 `DeregisterEvent` 方法可用來找出並取消註冊 `EventName` 屬性中所定義的事件，以清除任何潛在的記憶體流失問題。

## <a name="consuming-the-behavior"></a>使用行為

您可以將 `EventToCommandBehavior` 類別附加至控制項的 [`Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 集合，如下列 XAML 程式碼範例所示：

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

行為的 `Command` 屬性是繫結至相關聯 ViewModel 之 `OutputAgeCommand` 屬性的資料，而 `Converter` 屬性設定為 `SelectedItemConverter` 執行個體，其會從 [`SelectedItemChangedEventArgs`](xref:Xamarin.Forms.SelectedItemChangedEventArgs) 傳回 [`ListView`](xref:Xamarin.Forms.ListView) 的 [`SelectedItem`](xref:Xamarin.Forms.ListView.SelectedItem)。

在執行階段，行為會回應與控制項的互動。 在 [`ListView`](xref:Xamarin.Forms.ListView) 中選取項目時，會引發 [`ItemSelected`](xref:Xamarin.Forms.ListView.ItemSelected) 事件，其會在 ViewModel 中執行 `OutputAgeCommand`。 接著，這會更新 [`Label`](xref:Xamarin.Forms.Label) 繫結到的 ViewModel `SelectedItemText` 屬性，如下列螢幕擷取畫面所示：

[![](event-to-command-behavior-images/screenshots-sml.png "使用 EventToCommandBehavior 的範例應用程式")](event-to-command-behavior-images/screenshots.png#lightbox "使用 EventToCommandBehavior 的範例應用程式")

使用此行為在事件引發時執行命令的優點為，可以將命令與未設計於要與命令互動的控制項建立關聯。 此外，這可從程式碼後置檔案中移除以定案效果方式處理的程式碼。

## <a name="summary"></a>總結

本文示範如何使用 Xamarin.Forms 行為，在事件引發時叫用命令。 針對設計用意並非與命令互動的控制項，您可以使用行為來建立其與命令的關聯。

## <a name="related-links"></a>相關連結

- [Event to Command Behavior (Samples)](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/) (命令行為事件 (範例))
- [Behavior](xref:Xamarin.Forms.Behavior)
- [Behavior&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
