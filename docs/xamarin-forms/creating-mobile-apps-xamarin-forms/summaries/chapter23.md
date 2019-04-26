---
title: 第 23 章的摘要。 觸發程序和行為
description: 使用 Xamarin.Forms 建立行動應用程式：第 23 章的摘要。 觸發程序和行為
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: ddbb96836fa1aa9611701bd7d645d9e85ad28027
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61332088"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>第 23 章的摘要。 觸發程序和行為

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

觸發程序和行為的相似處，在於它們兩者都是使用 XAML 檔案中，以簡化之外使用的資料繫結的項目互動，以及擴充功能的 XAML 項目。 觸發程序和行為幾乎都可搭配 visual 的使用者介面物件。

若要支援觸發程序和行為，同時`VisualElement`和`Style`支援兩個集合屬性：

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) 並[ `Style.Triggers` ](xref:Xamarin.Forms.Style.Triggers)的型別 `IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 並[ `Style.Behaviors` ](xref:Xamarin.Forms.Style.Behaviors)的型別 `IList<Behavior>`

## <a name="triggers"></a>觸發程序

觸發程序是導致傳回回應 （另一個屬性變更或執行一些程式碼） 的條件 （屬性變更或事件的引發）。 `Triggers`的屬性`VisualElement`並`Style`別的`IList<TriggersBase>`。 [`TriggerBase`](xref:Xamarin.Forms.TriggerBase) 是四個密封的類別衍生自抽象類別：

- [`Trigger`](xref:Xamarin.Forms.Trigger) 根據屬性變更的回應
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) 根據事件引發的回應
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) 根據資料繫結的回應
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) 根據多個觸發程序的回應

觸發程序一律會在觸發程序正在變更其屬性的項目上設定。

### <a name="the-simplest-trigger"></a>最簡單的觸發程序

[ `Trigger` ](xref:Xamarin.Forms.Trigger)類別會檢查屬性值的變更，並回應相同的項目另一個屬性設定。

`Trigger` 會定義三個屬性：

- [`Property`](xref:Xamarin.Forms.Trigger.Property) 型別 `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value) 型別 `Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters) 型別的`IList<SetterBase>`的內容屬性 `Trigger`

颾魤 ㄛ`Trigger`需要下列屬性繼承自`TriggerBase`設定：

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType) 表示在其上的項目類型`Trigger`附加

`Property`並`Value`組成條件，而`Setters`集合是回應。 當指定`Property`所指定的值`Value`，則`Setter`中的物件`Setters`集合會套用。 當`Property`有不同的值，會移除 setter。 `Setter` 定義的前兩個屬性相同的兩個屬性`Trigger`:

- [`Property`](xref:Xamarin.Forms.Setter.Property) 型別 `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value) 型別 `Object`

[ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop)範例會示範如何`Trigger`套用至`Entry`可以增加的大小`Entry`透過`Scale`屬性時`IsFocused`的屬性`Entry`是`true`。

它並不常見，雖然`Trigger`可以在程式碼，設定為[ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode)範例會示範。

[ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers)範例會示範如何`Trigger`中可設定`Style`套用至多個`Entry`項目。

### <a name="trigger-actions-and-animations"></a>觸發程序動作和動畫

它也可執行觸發程序為基礎的一些程式碼。 此程式碼可以是目標屬性的動畫。 一種常見方式是使用[ `EventTrigger` ](xref:Xamarin.Forms.EventTrigger)，其定義兩個屬性：

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event) 型別的`string`，事件名稱
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) 型別的`IList<TriggerAction>`，要在回應中執行的動作清單。

若要使用這種情況，您要撰寫的類別，衍生自[ `TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1)中，通常`TriggerAction<VisualElement>`。 您可以在這個類別中定義屬性。 這些都是單純的 CLR 屬性，而不是可繫結屬性因為`TriggerAction`不是衍生自`BindableObject`。 您必須覆寫[ `Invoke` ](xref:Xamarin.Forms.TriggerAction`1.Invoke*)叫用動作時，會呼叫的方法。 引數是目標項目。

[ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫是一個範例。 它會呼叫`ScaleTo`要建立動畫屬性`Scale`元素的屬性。 因為其中一個屬性是類型`Easing`，則[ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs)類別可讓您使用標準`Easing`XAML 中的靜態欄位。

[ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell)範例示範如何叫用`ScaleAction`從`EventTrigger`物件監視`Focused`和`Unfocused`事件。

[ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell)範例示範如何定義自訂的 easing 函式，如`ScaleAction`程式碼後置檔案中。

您也可以叫用使用的動作`Trigger`(為 distinguished 從`EventTrigger`)。 這需要您已了解，`TriggerBase`定義兩個集合：

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) 型別 `IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) 型別 `IList<TriggerAction>`

[ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell)範例示範如何使用這些集合。

### <a name="more-event-triggers"></a>更多的事件觸發程序

[ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫呼叫`ScaleTo`兩次，以相應增加和減少。 [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth)範例會使用此樣式中`EventTrigger`提供視覺化回饋時`Button`按下。 此雙動畫，也可以使用的型別集合中的兩個動作 [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

[ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs)類別**Xamarin.FormsBook.Toolkit**程式庫會定義可自訂的背脊動作。 [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo)範例會示範它。

[ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs)類別**Xamarin.FormsBook.Toolkit**程式庫僅限於`Entry`項目和設定`TextColor`屬性為紅色`Text`屬性不是`double`。 [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation)範例會示範它。

### <a name="data-triggers"></a>資料觸發程序

[ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger)類似於`Trigger`不同之處在於而不是監視的屬性值變更，它會監視資料繫結。 這可以讓屬性項目會影響另一個項目中的屬性。

`DataTrigger` 會定義三個屬性：

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding) 型別 `BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value) 型別 `Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters) 型別 `IList<SetterBase>`

[ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors)範例需要[ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫和集合名稱的學生藍色的色彩或粉紅色根據`Sex`屬性：

[![性別色彩的三個螢幕擷取畫面](images/ch23fg04-small.png "性別色彩")](images/ch23fg04-large.png#lightbox "性別色彩")

[ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler)範例集`IsEnabled`屬性`Entry`至`False`如果`Length`屬性`Text`屬性`Entry`等於 0。 請注意，`Text`屬性會初始化為空字串; 預設是`null`，和`DataTrigger`不會正確運作。

### <a name="combining-conditions-in-the-multitrigger"></a>Multitrigger v 中結合條件

[ `MultiTrigger` ](xref:Xamarin.Forms.MultiTrigger)是條件的集合。 時所有`true`，就會套用 setter。 類別會定義兩個屬性：

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions) 型別 `IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters) 型別 `IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition) 是抽象類別，並且具有子系的兩個類別：

- [`PropertyCondition`](xref:Xamarin.Forms.Condition)其中包含[ `Property` ](xref:Xamarin.Forms.PropertyCondition.Property)並[ `Value` ](xref:Xamarin.Forms.PropertyCondition.Value)屬性，例如 `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition)其中包含[ `Binding` ](xref:Xamarin.Forms.BindingCondition.Binding)並[ `Value` ](xref:Xamarin.Forms.BindingCondition.Value)屬性，例如 `DataTrigger`

在  [ **AndConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions)範例中，`BoxView`著色不只有當四個`Switch`項目的所有啟動的。

[ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions)範例會示範如何讓`BoxView`色彩時*任何*四個`Switch`項目會開啟。 這需要 De Morgan 法律和反轉所有邏輯應用程式。

結合 AND 或邏輯並不容易，而且通常需要 不可見`Switch`中繼結果的項目。 [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions)範例會示範如何`Button`如果任一個可以啟用兩個`Entry`項目有中，輸入一些文字，但如果它們都有一些文字輸入。

## <a name="behaviors"></a>「行為」

您可以使用觸發程序執行的任何項目，您也可以執行的行為，但行為一律需要衍生自類別[ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1)並覆寫下列兩種方法：

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

引數是要附加行為的項目。 一般而言，`OnAttachedTo`方法會將附加的一些事件處理常式，和`OnDetachingFrom`中斷連結它們。 這種類別通常會儲存某些狀態，因為它通常無法在共用`Style`。

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation)範例是類似**TriggerEntryValidation**不同之處在於它會使用行為&mdash; [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。

### <a name="behaviors-with-properties"></a>使用屬性的行為

`Behavior<T>` 衍生自`Behavior`，其衍生自`BindableObject`，因此可繫結的屬性可以定義在行為上。 這些屬性可以為作用中資料繫結項目。

這示範於[ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo)程式，讓使用[ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs)類別[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。 `ValidEmailBehavior` 有一個唯讀的可繫結屬性，並做為資料繫結中的來源。

[ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv)範例會使用這個相同的行為來顯示另一種來通知電子郵件地址是有效的指標。

[ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger)範例是改變自先前的範例。 使用 ButtonGlide`DataTrigger`搭配該行為。

### <a name="toggles-and-check-boxes"></a>切換和核取方塊

就可以封裝在類別中的切換按鈕的行為，例如[ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫，然後將所有定義在 XAML 中切換視覺效果。

[ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel)範例會使用`ToggleBehavior`具有`DataTrigger`使用`Label`切換兩個文字字串。

[ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle)範例會將這個概念延伸藉由切換兩個`FormattedString`物件。

[ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs)類別**Xamarin.FormsBook.Toolkit**文件庫衍生自`ContentView`，定義`IsToggled` 屬性，並結合`ToggleBehavior`切換邏輯。 這可讓您更輕鬆地在 XAML 中，定義 [切換] 按鈕如所示[ **TraditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox)範例。

[ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo)包含[ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs)類別衍生自`ToggleBase`，並使用[ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)類別來建構類似於 Xamarin.Forms 的切換按鈕`Switch`。

A [ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs)中**Xamarin.FormsBook.Toolkit**提供用來建立動畫的推桿中的動畫[ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)範例。

### <a name="responding-to-taps"></a>回應的點選

有一項缺點`EventTrigger`是，您無法將其附加至`TapGestureRecognizer`點選回應。 取得解決該問題為目的[ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs)中[ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

[ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver)範例會使用`TapBehavior`若要使用舊版`ShiverAction`如點選`BoxView`項目。

[ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews)範例示範如何減少標記藉由將封裝`ShiverView`類別。

### <a name="radio-buttons"></a>選項按鈕

[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫也有[ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs)建立選項按鈕，依據類別`string`群組名稱。

[ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels)程式會使用其選項按鈕的文字字串。 [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle)範例會使用`Style`外觀 checked 與 unchecked 按鈕之間的相異之處。 [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages)範例會使用其選項按鈕的已封裝的映像：

[![三重的螢幕擷取畫面的電台映像](images/ch23fg17-small.png "選項按鈕圖像")](images/ch23fg17-large.png#lightbox "選項按鈕的影像")

[ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios)範例落在圓內繪製傳統的顯示選項按鈕，以點號。

### <a name="fades-and-orientation"></a>淡出及方向

最後一個範例中， [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders)可讓您使用選項按鈕的三個不同的色彩選取範圍檢視之間切換。 三個檢視淡入和登出使用[ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs)中[ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫。

程式也會以直向和橫向方向變更回應[ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs)中**Xamarin.FormsBook.Toolkit**程式庫。



## <a name="related-links"></a>相關連結

- [第 23 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [第 23 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [使用觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 行為](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
