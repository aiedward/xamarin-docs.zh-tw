---
title: 第23章的摘要。 觸發程序和行為
description: 建立 Mobile Apps，包含 Xamarin.Forms ：第23章的摘要。 觸發程序和行為
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bdbc795c0669115f822e6908590a3754af31a2d0
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373987"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>第23章的摘要。 觸發程序和行為

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

觸發程式和行為很類似，因為它們都是要在 XAML 檔案中使用，以簡化使用資料系結之外的元素互動，以及擴充 XAML 專案的功能。 觸發程式和行為幾乎一律會與視覺使用者介面物件一起使用。

為了支援觸發程式和行為， `VisualElement` 和兩者都 `Style` 支援兩個集合屬性：

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) 和 [`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers) 類型 `IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) 和 [`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors) 類型 `IList<Behavior>`

## <a name="triggers"></a>觸發程序

觸發程式是一種條件， (屬性變更或引發事件) ，而導致回應 (另一個屬性變更或執行某些程式碼) 。 `Triggers`和的屬性 `VisualElement` `Style` 屬於型別 `IList<TriggersBase>` 。 [`TriggerBase`](xref:Xamarin.Forms.TriggerBase) 是衍生四個密封類別的抽象類別：

- [`Trigger`](xref:Xamarin.Forms.Trigger) 針對根據屬性變更的回應
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) 針對根據事件引發的回應
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) 針對以資料系結為基礎的回應
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) 針對以多個觸發程式為基礎的回應

觸發程式一律會在觸發程式正在變更其屬性的元素上設定。

### <a name="the-simplest-trigger"></a>最簡單的觸發程式

[`Trigger`](xref:Xamarin.Forms.Trigger)類別會檢查屬性值的變更，並藉由設定相同元素的另一個屬性來回應。

`Trigger` 會定義三個屬性：

- [`Property`](xref:Xamarin.Forms.Trigger.Property) 型別為 `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value) 型別為 `Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters) 的類型 `IList<SetterBase>` ，內容屬性 `Trigger`

此外，還 `Trigger` 需要設定下列繼承自的屬性 `TriggerBase` ：

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType)指出附加之元素的類型 `Trigger`

`Property`和 `Value` 包含條件，而 `Setters` 集合是回應。 當指出的 `Property` 值有指定的值時 `Value` ，就 `Setter` 會套用集合中的物件 `Setters` 。 如果 `Property` 有不同的值，則會移除 setter。 `Setter` 定義兩個屬性，這兩個屬性與的前兩個屬性相同 `Trigger` ：

- [`Property`](xref:Xamarin.Forms.Setter.Property) 型別為 `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value) 型別為 `Object`

[**EntryPop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop)範例會示範當的 `Trigger` `Entry` `Entry` `Scale` `IsFocused` 屬性為時 `Entry` `true` ，套用至的如何透過屬性增加的大小。

雖然這並不常見，但 `Trigger` 可以在程式碼中設定，如 [**EntryPopCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) 範例所示。

[**StyledTriggers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers)範例會示範如何 `Trigger` 在中設定，以套用 `Style` 至多個 `Entry` 元素。

### <a name="trigger-actions-and-animations"></a>觸發動作和動畫

您也可以根據觸發程式來執行一些程式碼。 這段程式碼可以是以屬性為目標的動畫。 其中一個常見的方法是使用 [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) ，它會定義兩個屬性：

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event) 型別，也就是 `string` 事件的名稱
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) 的型別 `IList<TriggerAction>` ，這是要在回應中執行的動作清單。

若要使用此程式，您必須撰寫一個衍生自的類別 [`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1) ，通常是 `TriggerAction<VisualElement>` 。 您可以在這個類別中定義屬性。 這些都是簡單的 CLR 屬性，而不是可系結的屬性，因為 `TriggerAction` 並非衍生自 `BindableObject` 。 您必須覆寫 [`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*) 叫用動作時所呼叫的方法。 引數是目標元素。

[`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) [**Xamarin.Forms 書籍**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別是範例。 它會呼叫 `ScaleTo` 屬性，以動畫顯示 `Scale` 元素的屬性。 因為它的其中一個屬性是型別 `Easing` ，所以 [`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) 類別可讓您 `Easing` 在 XAML 中使用標準靜態欄位。

[**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell)範例會示範如何 `ScaleAction` 從 `EventTrigger` 監視和事件的物件叫用 `Focused` `Unfocused` 。

[**CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell)範例會示範如何 `ScaleAction` 在程式碼後端檔案中定義的自訂緩和函數。

您也可以使用 (來叫用動作 `Trigger` ，以區別 `EventTrigger`) 。 這需要您知道 `TriggerBase` 定義兩個集合：

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) 型別為 `IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) 型別為 `IList<TriggerAction>`

[**EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell)範例會示範如何使用這些集合。

### <a name="more-event-triggers"></a>其他事件觸發程式

[`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) [**Xamarin.Forms 書籍**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別會呼叫 `ScaleTo` 兩次以擴大和縮小。 [**ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth)範例會在已按下的樣式中使用此範例 `EventTrigger` ，以提供視覺效果的意見反應 `Button` 。 也可以使用類型集合中的兩個動作，來執行這項雙動畫 [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

[`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) **Xamarin.Forms Book** library 程式庫中的類別定義了可自訂的 shiver 動作。 [**ShiverButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo)範例會示範此範例。

[`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) **Xamarin.Forms Book** 中的類別會限制為 `Entry` 元素，如果屬性不是，則會將 `TextColor` 屬性設定為紅色 `Text` `double` 。 [**TriggerEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation)範例會示範此範例。

### <a name="data-triggers"></a>資料觸發程式

與 [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) `Trigger` 不同之處在于，它會監視資料系結，而不是監視值變更的屬性。 這可讓一個元素中的屬性影響另一個專案中的屬性。

`DataTrigger` 會定義三個屬性：

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding) 型別為 `BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value) 型別為 `Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters) 型別為 `IList<SetterBase>`

[**GenderColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors)範例需要 [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫，並根據屬性將學生名稱的色彩設定為藍色或粉紅色 `Sex` ：

[![性別色彩的三個螢幕擷取畫面](images/ch23fg04-small.png "性別色彩")](images/ch23fg04-large.png#lightbox "性別色彩")

[**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler)範例會將的屬性設定 `IsEnabled` 為，如果的屬性的 `Entry` `False` `Length` 屬性 `Text` `Entry` 等於0。 請注意， `Text` 屬性會初始化為空字串，預設為 `null` ，而且無法 `DataTrigger` 正常運作。

### <a name="combining-conditions-in-the-multitrigger"></a>結合 MultiTrigger 中的條件

[`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)是條件的集合。 當兩者都是時 `true` ，就會套用 setter。 類別會定義兩個屬性：

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions) 型別為 `IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters) 型別為 `IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition) 是抽象類別，且有兩個子代類別：

- [`PropertyCondition`](xref:Xamarin.Forms.Condition)，其具有 [`Property`](xref:Xamarin.Forms.PropertyCondition.Property) 和 [`Value`](xref:Xamarin.Forms.PropertyCondition.Value) 屬性，例如 `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition)，其具有 [`Binding`](xref:Xamarin.Forms.BindingCondition.Binding) 和 [`Value`](xref:Xamarin.Forms.BindingCondition.Value) 屬性，例如 `DataTrigger`

在 [**AndConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) 範例中， `BoxView` 只有當四個 `Switch` 元素都開啟時，才會彩色。

[**OrConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions)範例會示範如何在 `BoxView` 開啟 *任何* 四個專案時建立色彩 `Switch` 。 這需要申請 De Morgan 的法律，並反轉所有邏輯。

結合和和或邏輯並不容易，而且通常需要不可見 `Switch` 的元素來進行中繼結果。 [**XorConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions)範例會示範如何在 `Button` 兩個專案 `Entry` 中有一個輸入文字，而不是在兩者中輸入一些文字的情況下啟用。

## <a name="behaviors"></a>行為

您可以使用觸發程式來執行的任何動作，您也可以執行行為，但行為一律需要衍生自的類別， [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 並覆寫下列兩種方法：

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

引數是將行為附加至其中的元素。 一般而言， `OnAttachedTo` 方法會附加一些事件處理常式，並將 `OnDetachingFrom` 它們卸離。 因為這類類別通常會儲存某些狀態，所以通常無法在中共用 `Style` 。

[**BehaviorEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation)範例類似于 **TriggerEntryValidation** ，不同之處在于它會 &mdash; 在 [`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) [**Xamarin.Forms Book. 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中使用類別的行為。

### <a name="behaviors-with-properties"></a>具有屬性的行為

`Behavior<T>` 衍生自 `Behavior` （衍生自）， `BindableObject` 因此可在行為上定義可系結的屬性。 這些屬性可在資料系結中使用。

這會在 [**EmailValidationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo)程式中示範，而此程式會使用 [`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) [**Xamarin.Forms 書本套件**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別。 `ValidEmailBehavior` 具有唯讀的可系結屬性，可作為資料系結中的來源。

[**EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv)範例會使用相同的行為來顯示另一種類型的指標，以表示電子郵件地址有效。

[**EmailValidationTrigger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger)範例是上一個範例的變化。 ButtonGlide 使用結合 `DataTrigger` 該行為的。

### <a name="toggles-and-check-boxes"></a>切換和核取方塊

您可以在類別中封裝切換按鈕的行為（例如 [`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) ，在 [**Xamarin.Forms Book. 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中），然後在 XAML 中完全定義切換的所有視覺效果。

[**ToggleLabel**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel)範例會使用 `ToggleBehavior` with 來搭配 `DataTrigger` `Label` 兩個文字字串來進行切換。

[**FormattedTextToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle)範例會在兩個物件之間切換，以擴充這個概念 `FormattedString` 。

[`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) **Xamarin.Forms 書籍** 庫中的類別衍生自 `ContentView` 、定義 `IsToggled` 屬性，並併入 `ToggleBehavior` 切換邏輯的。 這可讓您更輕鬆地在 XAML 中定義切換按鈕，如 [**TraditionalCheckBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) 範例所示。

[**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo)包含 [`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) 衍生自的類別， `ToggleBase` 而且會使用 [`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs) 類別來建立類似的切換按鈕 Xamarin.Forms `Switch` 。

[`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) **Xamarin.Forms 。工具** 組提供一個動畫，用來建立 [**LeverToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)範例中的動畫拉杆。

### <a name="responding-to-taps"></a>回應點擊

其中一個缺點 `EventTrigger` 是，您無法將它附加至 `TapGestureRecognizer` 來回應點擊。 解決這個問題是本書的目的 [`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) [ **Xamarin.Forms 。工具** 組](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

[**BoxViewTapShiver**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver)範例會使用 `TapBehavior` 較早的來 `ShiverAction` 進行點擊的 `BoxView` 元素。

[**ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews)範例顯示如何藉由封裝類別來縮減標記 `ShiverView` 。

### <a name="radio-buttons"></a>選項按鈕

[**Xamarin.Forms 書籍**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫也有一個類別，可 [`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) 讓您依組名分組來分組選項按鈕 `string` 。

[**RadioLabels**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels)程式會使用文字字串做為其選項按鈕。 [**RadioStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle)範例會使用做 `Style` 為 checked 和 unchecked 按鈕之間的外觀差異。 [**RadioImages**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages)範例會使用已裝箱的影像作為其選項按鈕：

[![選項按鈕影像的三重螢幕擷取畫面](images/ch23fg17-small.png "選項按鈕影像")](images/ch23fg17-large.png#lightbox "選項按鈕影像")

[**TraditionalRadios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios)範例會使用圓形內的點，繪製傳統出現的選項按鈕。

### <a name="fades-and-orientation"></a>淡化和方向

最後一個範例 [**MultiColorSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) 可讓您使用選項按鈕，在三個不同的色彩選取視圖之間切換。 這三個視圖會使用 [`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) [**Xamarin.Forms 書本套件**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的來淡入和淡出。

此程式也會使用 [`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) **Xamarin.Forms 書籍** 庫中的來回應直向與橫向之間的變更。

## <a name="related-links"></a>相關連結

- [第23章全文 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [第23章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [使用觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 行為](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
