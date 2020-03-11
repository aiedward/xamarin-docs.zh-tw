---
title: 第23章的摘要。 觸發程式和行為
description: 使用 Xamarin 建立 Mobile Apps：第23章的摘要。 觸發程式和行為
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 8a1274a8447f49ce39f9c92703bbaec9e875b9e9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760586"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>第23章的摘要。 觸發程式和行為

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

觸發程式和行為很相似，因為它們都是要在 XAML 檔案中使用，以簡化除了使用資料系結以外的元素互動，以及擴充 XAML 專案的功能。 觸發程式和行為幾乎一律會與視覺使用者介面物件搭配使用。

為了支援觸發程式和行為，`VisualElement` 和 `Style` 都支援兩個集合屬性：

- 類型的[`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)和[`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers) `IList<TriggerBase>`
- 類型的[`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)和[`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors) `IList<Behavior>`

## <a name="triggers"></a>觸發程序

觸發程式是導致回應（另一個屬性變更或執行一些程式碼）的條件（屬性變更或引發事件）。 `VisualElement` 和 `Style` 的 `Triggers` 屬性是 `IList<TriggersBase>`類型。 [`TriggerBase`](xref:Xamarin.Forms.TriggerBase)是抽象類別，其中四個密封的類別會衍生：

- 根據屬性變更[`Trigger`](xref:Xamarin.Forms.Trigger)回應
- 根據事件引發的回應[`EventTrigger`](xref:Xamarin.Forms.EventTrigger)
- 根據資料系結的回應[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)
- 根據多個觸發程式的回應[`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)

觸發程式一律會在其屬性正由觸發程式變更的元素上設定。

### <a name="the-simplest-trigger"></a>最簡單的觸發程式

[`Trigger`](xref:Xamarin.Forms.Trigger)類別會檢查屬性值的變更，並藉由設定相同元素的另一個屬性來進行回應。

`Trigger` 定義三個屬性：

- 類型為 [ 的 `Property`](xref:Xamarin.Forms.Trigger.Property)`BindableProperty`
- 類型為 [ 的 `Value`](xref:Xamarin.Forms.Trigger.Value)`Object`
- 類型 `IList<SetterBase>`的[`Setters`](xref:Xamarin.Forms.Trigger.Setters) ，`Trigger` 的 content 屬性

此外，`Trigger` 需要設定繼承自 `TriggerBase` 的下列屬性：

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType) ，表示附加 `Trigger` 的元素類型

`Property` 和 `Value` 組成條件，而 `Setters` 集合則是回應。 當指示的 `Property` 具有 `Value`所指示的值時，就會套用 `Setters` 集合中的 `Setter` 物件。 當 `Property` 的值不同時，會移除 setter。 `Setter` 會定義兩個屬性，與 `Trigger`的前兩個屬性相同：

- 類型為 [ 的 `Property`](xref:Xamarin.Forms.Setter.Property)`BindableProperty`
- 類型為 [ 的 `Value`](xref:Xamarin.Forms.Setter.Value)`Object`

[**EntryPop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop)範例會示範當 `IsFocused` 的 `Entry` 屬性 `true`時，套用至 `Entry` 的 `Trigger` 如何透過 `Scale` 屬性增加 `Entry` 的大小。

雖然這並不常見，但 `Trigger` 可以在程式碼中設定，如[**EntryPopCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode)範例所示。

[**StyledTriggers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers)範例會示範如何在 `Style` 中設定 `Trigger`，以套用至多個 `Entry` 元素。

### <a name="trigger-actions-and-animations"></a>觸發程式動作和動畫

您也可以執行以觸發程式為基礎的一些程式碼。 這個程式碼可以是以屬性為目標的動畫。 其中一個常見的方式是使用[`EventTrigger`](xref:Xamarin.Forms.EventTrigger)，它會定義兩個屬性：

- 類型 `string`的[`Event`](xref:Xamarin.Forms.EventTrigger.Event) ，事件的名稱
- `IList<TriggerAction>`類型的[`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) ，這是要在回應中執行的動作清單。

若要使用此程式，您必須撰寫衍生自[`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1)的類別，通常是 `TriggerAction<VisualElement>`。 您可以在此類別中定義屬性。 這些是一般的 CLR 屬性，而不是可系結的屬性，因為 `TriggerAction` 不是衍生自 `BindableObject`。 您必須覆寫叫用動作時所呼叫的[`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*)方法。 引數是目標元素。

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的[`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs)類別是一個範例。 它會呼叫 `ScaleTo` 屬性，以建立專案之 `Scale` 屬性的動畫。 因為其中一個屬性的類型是 `Easing`，所以[`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs)類別可讓您在 XAML 中使用標準 `Easing` 靜態欄位。

[**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell)範例會示範如何從監視 `Focused` 和 `Unfocused` 事件的 `EventTrigger` 物件叫用 `ScaleAction`。

[**CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell)範例會示範如何在程式碼後置檔案中定義 `ScaleAction` 的自訂緩動函數。

您也可以使用 `Trigger` 叫用動作（與 `EventTrigger`的區別）。 這需要您知道 `TriggerBase` 定義了兩個集合：

- 類型為 [ 的 `EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions)`IList<TriggerAction>`
- 類型為 [ 的 `ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions)`IList<TriggerAction>`

[**EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell)範例會示範如何使用這些集合。

### <a name="more-event-triggers"></a>其他事件觸發程式

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的[`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs)類別會呼叫 `ScaleTo` 兩次，以相應增加和減少。 [**ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth)範例會在樣式的 `EventTrigger` 中使用此功能，以在按下 `Button` 時提供視覺效果的意見反應。 這個雙重動畫也可以使用類型集合中的兩個動作[`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

**FormsBook 工具**庫中的[`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs)類別會定義可自訂的 shiver 動作。 [**ShiverButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo)範例會示範它。

**FormsBook 工具**庫中的[`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs)類別僅限於 `Entry` 元素，並在 `Text` 屬性不是 `double`時，將 `TextColor` 屬性設定為紅色。 [**TriggerEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation)範例會示範它。

### <a name="data-triggers"></a>資料觸發程式

[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)類似 `Trigger`，不同之處在于它會監視資料系結，而不是監視屬性的值變更。 這可讓某個專案中的屬性影響另一個元素中的屬性。

`DataTrigger` 定義三個屬性：

- 類型為 [ 的 `Binding`](xref:Xamarin.Forms.DataTrigger.Binding)`BindingBase`
- 類型為 [ 的 `Value`](xref:Xamarin.Forms.DataTrigger.Value)`Object`
- 類型為 [ 的 `Setters`](xref:Xamarin.Forms.DataTrigger.Setters)`IList<SetterBase>`

[**GenderColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors)範例需要[**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫，並根據 `Sex` 屬性，將學生名稱的色彩設為藍色或粉紅色：

[![性別色彩的三重螢幕擷取畫面](images/ch23fg04-small.png "性別色彩")](images/ch23fg04-large.png#lightbox "性別色彩")

如果 `Text` 之 `Entry` 屬性的 `Length` 屬性等於0，則[**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler)範例會將 `Entry` 的 `IsEnabled` 屬性設定為 `False`。 請注意，`Text` 屬性會初始化為空字串;預設為 `null`，且 `DataTrigger` 無法正常運作。

### <a name="combining-conditions-in-the-multitrigger"></a>在 MultiTrigger 中合併條件

[`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)是條件的集合。 當它們全部 `true`時，就會套用 setter。 類別會定義兩個屬性：

- 類型為 [ 的 `Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions)`IList<Condition>`
- 類型為 [ 的 `Setters`](xref:Xamarin.Forms.MultiTrigger.Setters)`IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition)是抽象類別，而且有兩個子代類別：

- [`PropertyCondition`](xref:Xamarin.Forms.Condition)，其具有[`Property`](xref:Xamarin.Forms.PropertyCondition.Property)和[`Value`](xref:Xamarin.Forms.PropertyCondition.Value)屬性，例如 `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition)，其具有[`Binding`](xref:Xamarin.Forms.BindingCondition.Binding)和[`Value`](xref:Xamarin.Forms.BindingCondition.Value)屬性，例如 `DataTrigger`

在[**AndConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions)範例中，只有在全部開啟四個 `Switch` 元素時，才會著色 `BoxView`。

[**OrConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions)範例會示範如何在開啟四個 `Switch` 元素的*任一*項時，讓 `BoxView` 色彩。 這需要應用 Morgan 的法律，並反轉所有邏輯。

結合 AND 和 OR 邏輯並不容易，而且通常需要不可見 `Switch` 中繼結果的元素。 [**XorConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions)範例會示範如何在兩個 `Entry` 專案中有一個輸入的文字時，啟用 `Button`，但如果兩者都有輸入的文字，則不能啟用。

## <a name="behaviors"></a>行為

您可以使用觸發程式執行的任何作業，也可以使用行為來進行，但行為一律需要衍生自[`Behavior<T>`](xref:Xamarin.Forms.Behavior`1)的類別，並覆寫下列兩個方法：

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

引數是附加行為的元素。 一般來說，`OnAttachedTo` 方法會附加一些事件處理常式，並 `OnDetachingFrom` 將它們卸離。 因為這類類別通常會儲存某種狀態，所以通常無法在 `Style`中共用。

[**BehaviorEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation)範例類似**TriggerEntryValidation** ，不同之處在于它會使用 &mdash; [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中[`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs)類別的行為。

### <a name="behaviors-with-properties"></a>具有屬性的行為

`Behavior<T>` 衍生自 `BindableObject`衍生的 `Behavior`，因此可以在行為上定義可系結的屬性。 這些屬性可以在資料系結中使用。

這是在[**EmailValidationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo)程式中示範，它會使用[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫中的[`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs)類別。 `ValidEmailBehavior` 具有唯讀可系結屬性，並可作為資料系結中的來源。

[**EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv)範例會使用這個相同的行為來顯示另一種類型的指標，表示電子郵件地址是有效的。

[**EmailValidationTrigger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger)範例是上一個範例的變化。 ButtonGlide 會使用與該行為結合的 `DataTrigger`。

### <a name="toggles-and-check-boxes"></a>切換和核取方塊

您可以在類別中封裝切換按鈕的行為，例如[**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)中的[`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) ，然後在 XAML 中完全定義切換的所有視覺效果。

[**ToggleLabel**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel)範例會使用具有 `DataTrigger` 的 `ToggleBehavior`，將具有兩個文字字串的 `Label` 用於切換。

[**FormattedTextToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle)範例會藉由在兩個 `FormattedString` 物件之間切換來擴充此概念。

**FormsBook 工具**庫中的[`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs)類別衍生自 `ContentView`、定義 `IsToggled` 屬性，以及合併切換邏輯的 `ToggleBehavior`。 這可讓您更輕鬆地在 XAML 中定義切換按鈕，如[**TraditionalCheckBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox)範例所示。

[**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo)包含衍生自 `ToggleBase` 的[`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs)類別，並使用[`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)類別來建立類似于 Xamarin `Switch`的切換按鈕。

**FormsBook**中的[`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs)會提供一個動畫，用來在[**LeverToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)範例中建立動畫的拉杆。

### <a name="responding-to-taps"></a>回應點擊

`EventTrigger` 的一個缺點是，您無法將它附加至 `TapGestureRecognizer` 以回應點擊。 解決這個問題是[`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs)在 FormsBook 中的目的[ **。** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

[**BoxViewTapShiver**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver)範例會使用 `TapBehavior`，將先前的 `ShiverAction` 用於攻絲的 `BoxView` 元素。

[**ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews)範例示範如何藉由封裝 `ShiverView` 類別，在標記上減少。

### <a name="radio-buttons"></a>選項按鈕

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫也有一個[`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs)類別，可讓按 `string` 組名分組的選項按鈕。

[**RadioLabels**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels)程式會使用文字字串作為其選項按鈕。 [**RadioStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle)範例會使用 `Style`，以取得 [已檢查] 和 [未核取] 按鈕之間的外觀差異。 [**RadioImages**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages)範例會使用已裝箱的影像作為其選項按鈕：

[![收音機影像的三向螢幕擷取畫面](images/ch23fg17-small.png "選項按鈕影像")](images/ch23fg17-large.png#lightbox "選項按鈕影像")

[**TraditionalRadios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios)範例會以圓形內的點繪製傳統的顯示選項按鈕。

### <a name="fades-and-orientation"></a>淡出和方向

最後一個範例是[**MultiColorSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) ，可讓您使用選項按鈕在三個不同的色彩選取視圖之間切換。 這三個視圖會使用[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的[`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs)淡入和放大。

此程式也會使用**FormsBook 工具**庫中的[`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) ，來回應直向和橫向之間的變化。

## <a name="related-links"></a>相關連結

- [第23章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [第23章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [使用觸發程式](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 行為](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
