---
title: 可重複使用 EventToCommandBehavior
description: 行為可用來使命令無法正確命令與互動的控制項產生關聯。 本文示範如何建立及使用的 Xamarin.Forms 行為，可與事件引發時叫用命令。
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/09/2018
ms.openlocfilehash: 8bf8f86cf708806d1c17b3fe4eda0755f98fd646
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563182"
---
# <a name="reusable-eventtocommandbehavior"></a>可重複使用 EventToCommandBehavior

_行為可用來使命令無法正確命令與互動的控制項產生關聯。本文示範如何建立及使用的 Xamarin.Forms 行為，可與事件引發時叫用命令。_

## <a name="overview"></a>總覽

`EventToCommandBehavior`類別是可重複使用 Xamarin.Forms 的自訂行為執行命令，以回應*任何*事件的引發。 根據預設，事件引數的事件會傳遞至命令，而且可以選擇性地轉換[ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)實作。

下列的行為屬性必須設定為使用行為：

- **EventName** – 事件的名稱來接聽的行為。
- **命令**–`ICommand`執行。 若要尋找的行為必須要有`ICommand`執行個體[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)附加的控制項，可能會從父元素繼承。

也可以設定下列選擇性行為屬性：

- **CommandParameter** –`object`會傳遞至命令。
- **轉換器**– [ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)之間傳遞將會變更的事件引數的資料格式的實作*來源*並*目標*繫結引擎。

> [!NOTE]
> `EventToCommandBehavior`是自訂類別，可以在找到[EventToCommand 行為範例](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)，並不是 Xamarin.Forms 的一部分。

## <a name="creating-the-behavior"></a>建立行為

`EventToCommandBehavior`類別衍生自`BehaviorBase<T>`類別，又衍生自[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)類別。 目的`BehaviorBase<T>`類別是為需要的任何 Xamarin.Forms 行為提供基底類別[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)設為附加的控制項的行為。 這可確保行為可以繫結至並執行`ICommand`所指定`Command`屬性時的行為會取用。

`BehaviorBase<T>`類別會提供可覆寫[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))方法，以設定[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)行為和可覆寫[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法會清除`BindingContext`。 此外，此類別會儲存在附加的控制項的參考`AssociatedObject`屬性。

### <a name="implementing-bindable-properties"></a>實作可繫結屬性

`EventToCommandBehavior`類別會定義四個[ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty)情況下，執行使用者定義的命令，當事件引發時。 這些屬性是以下列程式碼範例所示：

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

當`EventToCommandBehavior`取用類別，`Command`屬性應該是資料繫結至`ICommand`要執行以回應事件的引發中定義`EventName`屬性。 預期的行為能找到`ICommand`上[ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext)的附加的控制項。

根據預設，事件的事件引數會傳遞至命令。 這項資料可以選擇性地轉換，因為之間傳遞*來源*並*目標*繫結引擎，藉由指定[ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)實作`Converter`屬性值。 或者，參數可以傳遞給命令藉由指定`CommandParameter`屬性值。

### <a name="implementing-the-overrides"></a>實作覆寫

`EventToCommandBehavior`類別會覆寫[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))並[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法`BehaviorBase<T>`類別，如下列程式碼範例所示：

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

[ `OnAttachedTo` ](xref:Xamarin.Forms.Behavior`1.OnAttachedTo(Xamarin.Forms.BindableObject))方法會藉由呼叫來執行安裝程式`RegisterEvent`方法，傳入的值`EventName`屬性做為參數。 [ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法會藉由呼叫執行清除`DeregisterEvent`方法，傳入的值`EventName`屬性做為參數。

### <a name="implementing-the-behavior-functionality"></a>實作行為的功能

行為的目的是執行所定義的命令`Command`屬性，以回應事件的引發所定義`EventName`屬性。 核心行為功能是以下列程式碼範例所示：

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

`RegisterEvent`方法執行以回應`EventToCommandBehavior`附加至控制項，以及它收到的值`EventName`屬性做為參數。 然後，這個方法會嘗試找出事件中所定義`EventName`附加控制項上的屬性。 前提是該事件可以位於，`OnEvent`方法登錄為事件處理常式方法。

`OnEvent`方法執行以回應事件的引發中定義`EventName`屬性。 前提`Command`屬性參考的有效`ICommand`，這個方法會嘗試擷取要傳遞給參數`ICommand`，如下所示：

- 如果`CommandParameter`屬性定義的參數，它會擷取。
- 否則，如果`Converter`屬性會定義[ `IValueConverter` ](xref:Xamarin.Forms.IValueConverter)實作轉換子執行，以及將轉換的事件引數資料，如之間傳遞*來源*並*目標*繫結引擎。
- 否則，事件引數會假設為參數。

資料繫結`ICommand`接著執行，將參數傳遞給命令，但前提[ `CanExecute` ](xref:Xamarin.Forms.Command.CanExecute(System.Object))方法會傳回`true`。

雖然未顯示在這裡，`EventToCommandBehavior`也包含`DeregisterEvent`方法，而且執行[ `OnDetachingFrom` ](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom(Xamarin.Forms.BindableObject))方法。 `DeregisterEvent`方法用來找出並取消註冊中所定義的事件`EventName`屬性，以清除任何潛在記憶體流失的問題。

## <a name="consuming-the-behavior"></a>使用行為

`EventToCommandBehavior`類別可以附加至[ `Behaviors` ](xref:Xamarin.Forms.VisualElement.Behaviors)集合的控制項，如下列 XAML 程式碼範例所示：

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

`Command`行為的屬性是資料繫結至`OutputAgeCommand`屬性產生關聯的 ViewModel，雖然`Converter`屬性設定為`SelectedItemConverter`執行個體，就會傳回[ `SelectedItem` ](xref:Xamarin.Forms.ListView.SelectedItem)的[ `ListView` ](xref:Xamarin.Forms.ListView)從[ `SelectedItemChangedEventArgs` ](xref:Xamarin.Forms.SelectedItemChangedEventArgs)。

在執行階段行為會回應與控制項互動。 當選取的項目[ `ListView` ](xref:Xamarin.Forms.ListView)，則[ `ItemSelected` ](xref:Xamarin.Forms.ListView.ItemSelected)會引發事件，會同時執行`OutputAgeCommand`在 ViewModel。 這會接著更新 ViewModel`SelectedItemText`屬性所[ `Label` ](xref:Xamarin.Forms.Label)繫結到，如下列螢幕擷取畫面所示：

[![](event-to-command-behavior-images/screenshots-sml.png "範例應用程式與 EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "EventToCommandBehavior 與應用程式範例")

執行命令，當事件引發時，使用這種行為的好處是，命令可以是未設計成與命令互動的控制項相關聯。 此外，這會從程式碼後置檔案移除必不可少事件處理程式碼。

## <a name="summary"></a>總結

這篇文章示範如何使用 Xamarin.Forms 行為與事件引發時叫用命令。 行為可用來使命令無法正確命令與互動的控制項產生關聯。

## <a name="related-links"></a>相關連結

- [EventToCommand 行為 （範例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [行為](xref:Xamarin.Forms.Behavior)
- [行為&lt;T&gt;](xref:Xamarin.Forms.Behavior`1)
