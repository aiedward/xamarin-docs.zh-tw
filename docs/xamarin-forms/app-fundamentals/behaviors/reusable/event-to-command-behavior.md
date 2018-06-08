---
title: 可重複使用 EventToCommandBehavior
description: 行為可用來將命令與過去並非設計來與命令互動的控制項產生關聯。 本文示範如何使用 Xamarin.Forms 行為與事件引發時叫用命令。
ms.prod: xamarin
ms.assetid: EC7F6556-9776-40B8-9424-A8094482A2F3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: e89400c74c3d1afbf8954d0f88387c5967ebd534
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848214"
---
# <a name="reusable-eventtocommandbehavior"></a>可重複使用 EventToCommandBehavior

_行為可用來將命令與過去並非設計來與命令互動的控制項產生關聯。本文示範如何使用 Xamarin.Forms 行為與事件引發時叫用命令。_

## <a name="overview"></a>總覽

`EventToCommandBehavior`類別是可重複使用 Xamarin.Forms 自訂行為執行命令，以回應*任何*事件引發。 根據預設，事件引數的事件將會傳遞至命令，而且可以選擇性地由轉換[ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/)實作。

下列行為屬性必須設定為使用行為：

- **EventName** – 事件的名稱來接聽的行為。
- **命令**– **ICommand**執行。 若要尋找的行為必須要有`ICommand`執行個體上[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)附加的控制項，可能會從父元素繼承而來。

也可以設定下列選擇性行為屬性：

- **CommandParameter** –`object`會傳遞至命令。
- **轉換器**– [ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/)之間傳遞將變更的事件引數資料格式的實作*來源*和*目標*繫結引擎。

## <a name="creating-the-behavior"></a>建立行為

`EventToCommandBehavior`類別衍生自`BehaviorBase<T>`類別，又衍生自[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)類別。 目的`BehaviorBase<T>`類別是基底類別提供需要的任何 Xamarin.Forms 行為[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)設為附加控制項的行為。 這樣可以確保行為可以繫結至執行`ICommand`所指定`Command`取用行為時的屬性。

`BehaviorBase<T>`類別提供可覆寫[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)方法，以設定[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)的行為和可覆寫[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法清除`BindingContext`。 此外，此類別會儲存在附加的控制項的參考`AssociatedObject`屬性。

### <a name="implementing-bindable-properties"></a>實作可繫結屬性

`EventToCommandBehavior`類別會定義四個[ `BindableProperty` ](https://developer.xamarin.com/api/type/Xamarin.Forms.BindableProperty/)與事件引發時，執行個體，執行使用者定義命令。 這些屬性是以下列程式碼範例所示：

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

當`EventToCommandBehavior`取用類別`Command`屬性應該是資料繫結到`ICommand`中回應中所定義的事件引發所要執行`EventName`屬性。 預期的行為能找到`ICommand`上[ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/)的附加的控制項。

根據預設，此事件的事件引數會傳遞給命令。 此資料可以選擇性地轉換為之間傳遞*來源*和*目標*繫結引擎，藉由指定[ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/)實作做為`Converter`屬性值。 或者，參數可以傳遞給命令藉由指定`CommandParameter`屬性值。

### <a name="implementing-the-overrides"></a>實作覆寫

`EventToCommandBehavior`類別覆寫[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)和[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法`BehaviorBase<T>`類別，如下列程式碼範例所示：

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

[ `OnAttachedTo` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/Xamarin.Forms.BindableObject/)方法會藉由呼叫來執行安裝程式`RegisterEvent`方法，傳入的值`EventName`屬性做為參數。 [ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法呼叫會執行清除`DeregisterEvent`方法，傳入的值`EventName`屬性做為參數。

### <a name="implementing-the-behavior-functionality"></a>實作的行為的功能

行為的目的是要執行命令所定義`Command`回應事件引發所定義的屬性`EventName`屬性。 核心行為的功能是以下列程式碼範例所示：

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

`RegisterEvent`方法執行以回應`EventToCommandBehavior`附加至控制項，以及它收到的值`EventName`屬性做為參數。 方法接著會嘗試找出中定義的事件`EventName`附加控制項上的屬性。 前提是該事件可以位於，`OnEvent`方法登錄為事件處理常式方法。

`OnEvent`方法會在回應中所定義的事件引發中執行`EventName`屬性。 前提是`Command`屬性參考是有效`ICommand`，該方法會嘗試擷取要傳遞給參數`ICommand`，如下所示：

- 如果`CommandParameter`屬性定義參數、 擷取。
- 否則，如果`Converter`屬性會定義[ `IValueConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.IValueConverter/)轉換器實作會執行，並且將事件引數資料間傳送*來源*和*目標*繫結引擎。
- 否則，事件引數會假設為參數。

資料繫結`ICommand`就執行，將參數中傳遞至命令，但前提是[ `CanExecute` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Command.CanExecute/p/System.Object/)方法會傳回`true`。

雖然未顯示在這裡，`EventToCommandBehavior`也包含`DeregisterEvent`方法由執行[ `OnDetachingFrom` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/Xamarin.Forms.BindableObject/)方法。 `DeregisterEvent`方法用來找出並取消登錄中定義事件`EventName`屬性，若要清除任何潛在的記憶體流失的問題。

## <a name="consuming-the-behavior"></a>使用行為

`EventToCommandBehavior`類別可以附加至[ `Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/)集合的控制項，如下列 XAML 程式碼範例所示：

```xaml
<ListView ItemsSource="{Binding People}">
  <ListView.Behaviors>
    <local:EventToCommandBehavior EventName="ItemSelected" Command="{Binding OutputAgeCommand}"
                                  Converter="{StaticResource SelectedItemConverter}" />
  </ListView.Behaviors>
</ListView>
<Label Text="{Binding SelectedItemText}" />
```

對等的 C# 程式碼會顯示在以下程式碼範例中：

```csharp
var listView = new ListView ();
listView.SetBinding (ItemsView<Cell>.ItemsSourceProperty, "People");
listView.Behaviors.Add (new EventToCommandBehavior {
  EventName = "ItemSelected",
  Command = ((HomePageViewModel)BindingContext).OutputAgeCommand,
  Converter = new SelectedItemEventArgsToSelectedItemConverter ()
});

var selectedItemLabel = new Label ();
selectedItemLabel.SetBinding (Label.TextProperty, "SelectedItemText");
```

`Command`行為的屬性是資料繫結至`OutputAgeCommand`屬性關聯的 ViewModel，雖然`Converter`屬性設定為`SelectedItemConverter`執行個體，它會傳回[ `SelectedItem` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.SelectedItem/)的[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)從[ `SelectedItemChangedEventArgs` ](https://developer.xamarin.com/api/type/Xamarin.Forms.SelectedItemChangedEventArgs/)。

在執行階段，行為會回應與控制項互動。 當選取的項目[ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/)、 [ `ItemSelected` ](https://developer.xamarin.com/api/event/Xamarin.Forms.ListView.ItemSelected/)會引發事件，會同時執行`OutputAgeCommand`ViewModel 中。 這會依次更新 ViewModel`SelectedItemText`屬性， [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/)繫結到，如下列螢幕擷取畫面所示：

[![](event-to-command-behavior-images/screenshots-sml.png "範例應用程式與 EventToCommandBehavior")](event-to-command-behavior-images/screenshots.png#lightbox "範例 EventToCommandBehavior 與應用程式")

執行命令，當事件引發時，使用這種行為的優點是，命令可能會與未設計成與命令互動控制項相關聯。 此外，這會移除鍋爐調色盤事件處理程式碼程式碼後置檔案。

## <a name="summary"></a>總結

本文示範使用 Xamarin.Forms 行為與事件引發時叫用命令。 行為可用來將命令與過去並非設計來與命令互動的控制項產生關聯。


## <a name="related-links"></a>相關連結

- [EventToCommand 行為 （範例）](https://developer.xamarin.com/samples/xamarin-forms/behaviors/eventtocommandbehavior/)
- [行為](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior/)
- [行為<T>](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)
