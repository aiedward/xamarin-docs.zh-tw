---
title: 章 23 的摘要。 觸發程序和行為
description: 使用 Xamarin.Forms 建立行動裝置應用程式： 章 23 的摘要。 觸發程序和行為
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 0e5609a3df94914594d6547d9a1887078d282127
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241279"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>章 23 的摘要。 觸發程序和行為

觸發程序和行為的相似處，在於它們會同時適用於在 XAML 檔案以簡化以外使用的項目互動的資料繫結，並且將擴充功能的 XAML 項目。 觸發程序和行為幾乎可搭配 visual 的使用者介面物件。

若要支援觸發程序和行為，同時`VisualElement`和`Style`支援兩個集合屬性：

- [`VisualElement.Triggers`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Triggers/) 和[ `Style.Triggers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Triggers/)的型別 `IList<TriggerBase>`
- [`VisualElement.Behaviors`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) 和[ `Style.Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Behaviors/)的型別 `IList<Behavior>`

## <a name="triggers"></a>觸發程序

觸發程序是導致的回應 （另一個屬性變更或執行一些程式碼） 的條件 （在屬性變更或事件的引發）。 `Triggers`屬性`VisualElement`和`Style`的型別`IList<TriggersBase>`。 [`TriggerBase`](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerBase/) 是四個密封的類別衍生自抽象類別：

- [`Trigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.Trigger/) 屬性變更為基礎的回應
- [`EventTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.EventTrigger/) 根據事件引發的回應
- [`DataTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) 根據資料繫結的回應
- [`MultiTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiTrigger/) 根據多個觸發程序的回應

觸發程序一定會設定其屬性觸發程序正在變更的項目上。

### <a name="the-simplest-trigger"></a>最簡單的觸發程序

[ `Trigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Trigger/)類別會檢查屬性值的變更，並藉由設定另一個屬性，在相同元素的回應。

`Trigger` 會定義三個屬性：

- [`Property`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Property/) 型別 `BindableProperty`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Value/) 型別 `Object`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Setters/) 型別的`IList<SetterBase>`的內容屬性 `Trigger`

此外，`Trigger`需要下列屬性繼承自`TriggerBase`設定：

- [`TargetType`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.TargetType/) 表示在其上的項目類型`Trigger`附加

`Property`和`Value`組成條件，而`Setters`集合是回應。 當指定`Property`具有所指示的值`Value`，然後在`Setter`中的物件`Setters`集合會套用。 當`Property`有不同的值，會移除 setter。 `Setter` 定義的前兩個屬性相同的兩個屬性`Trigger`:

- [`Property`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/) 型別 `BindableProperty`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/) 型別 `Object`

[ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop)範例會示範如何`Trigger`套用至`Entry`可以增加的大小`Entry`透過`Scale`屬性時`IsFocused`屬性`Entry`是`true`。

雖然這並非不常見，`Trigger`可以在程式碼中設定為[ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode)範例將示範如何。

[ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers)範例會示範如何`Trigger`可以設定`Style`要套用至多個`Entry`項目。

### <a name="trigger-actions-and-animations"></a>觸發程序動作和動畫

它也可執行觸發程序為基礎的一些程式碼。 此程式碼可以是以屬性為目標的動畫。 一種常見方式是使用[ `EventTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.EventTrigger/)，而後者可定義兩個屬性：

- [`Event`](https://developer.xamarin.com/api/property/Xamarin.Forms.EventTrigger.Event/) 型別的`string`，為事件的名稱
- [`Actions`](https://developer.xamarin.com/api/property/Xamarin.Forms.EventTrigger.Actions/) 型別的`IList<TriggerAction>`，要在回應中執行的動作清單。

若要使用此功能，您必須撰寫衍生自類別[ `TriggerAction<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerAction%3CT%3E/)、 通常`TriggerAction<VisualElement>`。 您可以在此類別中定義屬性。 這些是單純的 CLR 屬性，而不是可繫結屬性因為`TriggerAction`不是衍生自`BindableObject`。 您必須覆寫[ `Invoke` ](https://developer.xamarin.com/api/member/Xamarin.Forms.TriggerAction%3CT%3E.Invoke/p/T/)叫用動作時所呼叫的方法。 引數是目標項目。

[ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫是一個範例。 它會呼叫`ScaleTo`要製作動畫屬性`Scale`某個項目的屬性。 因為其中一個屬性的類型是`Easing`、 [ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs)類別可讓您使用標準`Easing`XAML 中的靜態欄位。

[ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell)範例示範如何叫用`ScaleAction`從`EventTrigger`物件監視`Focused`和`Unfocused`事件。

[ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell)範例示範如何定義自訂 easing 函式的`ScaleAction`程式碼後置檔案中。

您也可以叫用動作使用`Trigger`(distinguished 在`EventTrigger`)。 這需要您了解，`TriggerBase`定義兩個集合：

- [`EnterActions`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.EnterActions/) 型別 `IList<TriggerAction>`
- [`ExitActions`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.ExitActions/) 型別 `IList<TriggerAction>`

[ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell)範例示範如何使用這些集合。

### <a name="more-event-triggers"></a>多個事件觸發程序

[ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫呼叫`ScaleTo`兩次，以向上和向下調整。 [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth)範例會使用此樣式中`EventTrigger`提供視覺化回應時`Button`按下。 此雙動畫，您也可以使用的型別集合中的兩個動作 [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

[ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs)類別**Xamarin.FormsBook.Toolkit**程式庫會定義可自訂的 shiver 動作。 [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo)範例會示範它。

[ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs)類別**Xamarin.FormsBook.Toolkit**程式庫僅限於`Entry`項目和設定`TextColor`屬性紅色如果`Text`屬性不是`double`。 [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation)範例會示範它。

### <a name="data-triggers"></a>資料觸發程序

[ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/)類似於`Trigger`不同之處在於而不是監視的屬性值變更，它會監視資料繫結。 這可以讓屬性項目會影響另一個項目中的屬性。

`DataTrigger` 會定義三個屬性：

- [`Binding`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Binding/) 型別 `BindingBase`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Value/) 型別 `Object`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Setters/) 型別 `IList<SetterBase>`

[ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors)範例需要[ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫和集合名稱為藍色學生的色彩或粉紅色根據`Sex`屬性：

[![三個螢幕擷取畫面的性別色彩](images/ch23fg04-small.png "性別色彩")](images/ch23fg04-large.png#lightbox "性別色彩")

[ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler)範例集`IsEnabled`屬性`Entry`至`False`如果`Length`屬性`Text`屬性`Entry`等於 0。 請注意，`Text`屬性會初始化為空字串，則依預設它是`null`，而`DataTrigger`不會正確運作。

### <a name="combining-conditions-in-the-multitrigger"></a>MultiTrigger 中結合條件

[ `MultiTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiTrigger/)是條件的集合。 它們是所有`true`，則會套用 setter。 類別會定義兩個屬性：

- [`Conditions`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiTrigger.Conditions/) 型別 `IList<Condition>`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiTrigger.Setters/) 型別 `IList<Setter>`

[`Condition`](https://developer.xamarin.com/api/type/Xamarin.Forms.Condition/) 是抽象類別，並且具有子系的兩個類別：

- [`PropertyCondition`](https://developer.xamarin.com/api/type/Xamarin.Forms.Condition/)其中包含[ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PropertyCondition.Property/)和[ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PropertyCondition.Value/)屬性，例如 `Trigger`
- [`BindingCondition`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingCondition/)其中包含[ `Binding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingCondition.Binding/)和[ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingCondition.Value/)屬性，例如 `DataTrigger`

在[ **AndConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions)範例中，`BoxView`著色不只有當四個`Switch`項目的所有啟動的。

[ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions)範例會示範如何讓`BoxView`色彩時*任何*四個`Switch`元素會開啟。 這需要 De Morgan 法律和反轉所有邏輯的應用程式。

結合 AND 或邏輯並不容易，而且通常需要 看不見`Switch`中繼結果的項目。 [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions)範例會示範如何`Button`，才可以啟用任一兩個`Entry`項目有一些文字輸入，但不是如果兩者都具有一些文字輸入。

## <a name="behaviors"></a>「行為」

您可以使用觸發程序執行的任何項目，您也可以執行的行為，但行為一律需要類別衍生自[ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/)和覆寫下列兩種方法：

- [`OnAttachedTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/T/)
- [`OnDetachingFrom`](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/T/)

引數是行為已附加至的項目。 一般而言，`OnAttachedTo`方法會附加一些事件處理常式，並`OnDetachingFrom`中斷連結它們。 由於這種類別通常會將儲存的某種狀態，它通常無法在共用`Style`。

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation)範例是類似於**TriggerEntryValidation**不同之處在於它會使用行為&mdash; [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。

### <a name="behaviors-with-properties"></a>屬性行為

`Behavior<T>` 衍生自`Behavior`，其衍生自`BindableObject`，因此可繫結屬性可定義在行為上。 這些屬性可以使用資料繫結。

這示範於[ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo)程式，讓使用[ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。 `ValidEmailBehavior` 具有唯讀的可繫結屬性，並做為資料繫結中的來源。

[ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv)範例使用這個相同的行為來顯示其他類型的指標，以表示電子郵件地址無效。

[ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger)範例是改變自先前的範例。 使用 ButtonGlide`DataTrigger`搭配該行為。

### <a name="toggles-and-check-boxes"></a>切換按鈕和核取方塊

可封裝的類別中的切換按鈕的行為，例如[ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫，然後定義所有在 XAML 中切換視覺效果。

[ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel)範例會使用`ToggleBehavior`與`DataTrigger`使用`Label`與切換兩個文字字串。

[ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle)範例會將此概念延伸藉由切換兩個`FormattedString`物件。

[ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs)類別**Xamarin.FormsBook.Toolkit**程式庫是衍生自`ContentView`，定義`IsToggled`屬性，並結合`ToggleBehavior`切換邏輯。 這可讓您更輕鬆地定義在 XAML 中，切換按鈕所示[ **TranditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox)範例。

[ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo)包含[ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs)類別衍生自`ToggleBase`並用[ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)類別以建構類似於 Xamarin.Forms 的切換按鈕`Switch`。

A [ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs)中**Xamarin.FormsBook.Toolkit**提供用來進行中的動畫的桿動畫[ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)範例。

### <a name="responding-to-taps"></a>回應點選

有一項缺點`EventTrigger`是，您無法將其附加至`TapGestureRecognizer`點選回應。 解決該問題的目的是[ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs)中[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

[ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver)範例會使用`TapBehavior`使用舊版`ShiverAction`的點選`BoxView`項目。

[ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews)範例示範如何藉由封裝減少撥打標記`ShiverView`類別。

### <a name="radio-buttons"></a>選項按鈕

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫也有[ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs)建立選項按鈕是依據類別`string`群組名稱。

[ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels)程式使用其選項按鈕的文字字串。 [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle)範例會使用`Style`外觀 checked 與 unchecked 按鈕之間的差異。 [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages)範例會使用其選項按鈕的 boxed 的映像：

[![三個螢幕擷取畫面的電台映像](images/ch23fg17-small.png "選項按鈕影像")](images/ch23fg17-large.png#lightbox "選項按鈕的影像")

[ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios)範例繪製傳統的顯示選項按鈕，以點號的圓圈。

### <a name="fades-and-orientation"></a>淡化和方向

最後一個範例中， [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders)可讓您使用選項按鈕的三個不同的色彩選擇檢視之間切換。 三個檢視淡入和移出使用[ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。

程式也會在縱向或橫向使用之間的方向變更回應[ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs)中**Xamarin.FormsBook.Toolkit**程式庫。



## <a name="related-links"></a>相關連結

- [章 23 全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [章 23 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [使用觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 行為](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
