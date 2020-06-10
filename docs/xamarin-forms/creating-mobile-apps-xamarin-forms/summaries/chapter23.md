---
title： "第23章的摘要。 觸發程式和行為「描述：」使用下列方式建立 Mobile Apps Xamarin.Forms ：第23章的摘要。 觸發程式和行為 "ms-chap： xamarin ms. 技術： assetid： 19E84B5D-46B4-4B6D-A255-87BEFB011261 author： davidbritch ms-chap： dabritch ms. date： 11/07/2017 no loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>第23章的摘要。 觸發程序和行為

[![下載範例 ](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

觸發程式和行為很相似，因為它們都是要在 XAML 檔案中使用，以簡化除了使用資料系結以外的元素互動，以及擴充 XAML 專案的功能。 觸發程式和行為幾乎一律會與視覺使用者介面物件搭配使用。

為了支援觸發程式和行為， `VisualElement` 和都 `Style` 支援兩個集合屬性：

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)和 [`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers) 屬於類型`IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)和 [`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors) 屬於類型`IList<Behavior>`

## <a name="triggers"></a>觸發程序

觸發程式是導致回應（另一個屬性變更或執行一些程式碼）的條件（屬性變更或引發事件）。 `Triggers`和的屬性屬於型別 `VisualElement` `Style` `IList<TriggersBase>` 。 [`TriggerBase`](xref:Xamarin.Forms.TriggerBase)是四個密封類別所衍生的抽象類別：

- [`Trigger`](xref:Xamarin.Forms.Trigger)針對以屬性變更為基礎的回應
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger)根據事件引發的回應
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)針對以資料系結為基礎的回應
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)針對以多個觸發程式為基礎的回應

觸發程式一律會在其屬性正由觸發程式變更的元素上設定。

### <a name="the-simplest-trigger"></a>最簡單的觸發程式

[`Trigger`](xref:Xamarin.Forms.Trigger)類別會檢查屬性值的變更，並藉由設定相同元素的另一個屬性來進行回應。

`Trigger`定義三個屬性：

- [`Property`](xref:Xamarin.Forms.Trigger.Property)類型為`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value)類型為`Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters)屬於類型的 `IList<SetterBase>` 、的 content 屬性`Trigger`

此外， `Trigger` 也需要設定下列繼承自的屬性 `TriggerBase` ：

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType)表示附加之元素的類型 `Trigger`

`Property`和 `Value` 會組成條件，而 `Setters` 集合則是回應。 當指定的 `Property` 具有所指示的值時 `Value` ，就 `Setter` 會套用集合中的物件 `Setters` 。 當 `Property` 具有不同的值時，就會移除 setter。 `Setter`定義兩個屬性，與的前兩個屬性相同 `Trigger` ：

- [`Property`](xref:Xamarin.Forms.Setter.Property)類型為`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value)類型為`Object`

[**EntryPop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop)範例會示範當的 `Trigger` `Entry` `Entry` `Scale` `IsFocused` 屬性 `Entry` 為時 `true` ，套用至的如何透過屬性來增加的大小。

雖然這並不常見，但 `Trigger` 可在程式碼中設定，如[**EntryPopCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode)範例所示。

[**StyledTriggers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers)範例會示範如何 `Trigger` 在中設定，以套用 `Style` 至多個 `Entry` 元素。

### <a name="trigger-actions-and-animations"></a>觸發程式動作和動畫

您也可以執行以觸發程式為基礎的一些程式碼。 這個程式碼可以是以屬性為目標的動畫。 其中一個常見的方式是使用 [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) ，它會定義兩個屬性：

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event)類型的 `string` ，事件的名稱
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions)類型的 `IList<TriggerAction>` ，要在回應中執行的動作清單。

若要使用此程式，您必須撰寫一個衍生自的類別 [`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1) ，通常是 `TriggerAction<VisualElement>` 。 您可以在此類別中定義屬性。 這些是一般的 CLR 屬性，而不是可系結的屬性，因為 `TriggerAction` 不是衍生自 `BindableObject` 。 您必須覆寫 [`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*) 叫用動作時所呼叫的方法。 引數是目標元素。

[`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別是一個範例。 它會呼叫 `ScaleTo` 屬性來建立專案 `Scale` 屬性的動畫。 因為它的其中一個屬性屬於類型 `Easing` ，所以 [`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) 類別可讓您 `Easing` 在 XAML 中使用標準靜態欄位。

[**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell)範例會示範如何 `ScaleAction` 從 `EventTrigger` 監視和事件的物件叫用 `Focused` `Unfocused` 。

[**CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell)範例示範如何 `ScaleAction` 在程式碼後置檔案中定義的自訂緩動函數。

您也可以使用 `Trigger` （與的區別）來叫用動作 `EventTrigger` 。 這需要您注意 `TriggerBase` 定義兩個集合：

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions)類型為`IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions)類型為`IList<TriggerAction>`

[**EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell)範例會示範如何使用這些集合。

### <a name="more-event-triggers"></a>其他事件觸發程式

[`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類別會呼叫 `ScaleTo` 兩次，以相應增加和減少。 [**ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth)範例會在樣式化中使用此 `EventTrigger` 功能，以在按下時提供視覺化的意見反應 `Button` 。 這種雙重動畫也可以在類型的集合中使用兩個動作[`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

[`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) **FormsBook 工具**庫中的類別會定義可自訂的 shiver 動作。 [**ShiverButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo)範例會示範它。

[`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) **FormsBook 工具**庫中的類別僅限於專案 `Entry` ，如果屬性不是，則將 `TextColor` 屬性設定為紅色 `Text` `double` 。 [**TriggerEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation)範例會示範它。

### <a name="data-triggers"></a>資料觸發程式

類似于， [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) `Trigger` 不同之處在于它會監視資料系結，而不是監視屬性的值變更。 這可讓某個專案中的屬性影響另一個元素中的屬性。

`DataTrigger`定義三個屬性：

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding)類型為`BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value)類型為`Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters)類型為`IList<SetterBase>`

[**GenderColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors)範例需要[**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)程式庫，並根據屬性將學生名稱的色彩設為藍色或粉紅色 `Sex` ：

[![性別色彩的三重螢幕擷取畫面](images/ch23fg04-small.png "性別色彩")](images/ch23fg04-large.png#lightbox "性別色彩")

如果[**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler)的屬性（property）的 `IsEnabled` `Entry` `False` `Length` 屬性 `Text` `Entry` 等於0，則 ButtonEnabler 範例會將的屬性設定為。 請注意， `Text` 屬性會初始化為空字串; 依預設，它是 `null` ，而且不會 `DataTrigger` 正確運作。

### <a name="combining-conditions-in-the-multitrigger"></a>在 MultiTrigger 中合併條件

[`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)是條件的集合。 當它們全部都是時 `true` ，就會套用 setter。 類別會定義兩個屬性：

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions)類型為`IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters)類型為`IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition)是抽象類別，而且有兩個子代類別：

- [`PropertyCondition`](xref:Xamarin.Forms.Condition)，其具有 [`Property`](xref:Xamarin.Forms.PropertyCondition.Property) 和 [`Value`](xref:Xamarin.Forms.PropertyCondition.Value) 屬性，例如`Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition)，其具有 [`Binding`](xref:Xamarin.Forms.BindingCondition.Binding) 和 [`Value`](xref:Xamarin.Forms.BindingCondition.Value) 屬性，例如`DataTrigger`

在[**AndConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions)範例中， `BoxView` 只有在全部開啟四個元素時，才會著色 `Switch` 。

[**OrConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions)範例會示範如何在 `BoxView` 開啟四個元素的*任*一個時，建立色彩 `Switch` 。 這需要應用 Morgan 的法律，並反轉所有邏輯。

結合 AND 和 OR 邏輯並不容易，而且通常需要不可見 `Switch` 的元素來進行中繼結果。 [**XorConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions)範例會示範如何在 `Button` 兩個專案 `Entry` 中都有輸入的文字時啟用，但如果兩者都有輸入的文字，則為。

## <a name="behaviors"></a>「行為」

您可以使用觸發程式執行的任何作業，也可以使用行為來進行，但行為一律需要衍生自的類別， [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) 並覆寫下列兩個方法：

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

引數是附加行為的元素。 一般來說， `OnAttachedTo` 方法會附加一些事件處理常式，並將 `OnDetachingFrom` 它們卸離。 因為這類類別通常會儲存某種狀態，所以通常無法在中共用 `Style` 。

[**BehaviorEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation)範例類似**TriggerEntryValidation** ，不同之處在于它會在 &mdash; [`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中使用類別的行為。

### <a name="behaviors-with-properties"></a>具有屬性的行為

`Behavior<T>`衍生自 `Behavior` ，其衍生自 `BindableObject` ，因此可以在行為上定義可系結的屬性。 這些屬性可以在資料系結中使用。

這是在[**EmailValidationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo)程式中示範，它會使用 [`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)程式庫中的類別。 `ValidEmailBehavior`具有唯讀可系結屬性，並可作為資料系結中的來源。

[**EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv)範例會使用這個相同的行為來顯示另一種類型的指標，表示電子郵件地址是有效的。

[**EmailValidationTrigger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger)範例是上一個範例的變化。 ButtonGlide 會將 `DataTrigger` 與該行為搭配使用。

### <a name="toggles-and-check-boxes"></a>切換和核取方塊

您可以在類別中封裝切換按鈕的行為（例如 [`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) [**FormsBook**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) ），然後在 XAML 中完全定義切換的所有視覺效果。

[**ToggleLabel**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel)範例會使用搭配 `ToggleBehavior` ，將 `DataTrigger` `Label` 與兩個文字字串用於切換。

[**FormattedTextToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle)範例會藉由在兩個物件之間切換來延伸此概念 `FormattedString` 。

[`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) **FormsBook**中的類別衍生自 `ContentView` 、定義 `IsToggled` 屬性，並 `ToggleBehavior` 為切換邏輯併入。 這可讓您更輕鬆地在 XAML 中定義切換按鈕，如[**TraditionalCheckBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox)範例所示。

[**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo)包含 [`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) 衍生自的類別， `ToggleBase` 並使用 [`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs) 類別來建立類似的切換按鈕 Xamarin.Forms `Switch` 。

[`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) **FormsBook**中的會提供一個動畫，用來在[**LeverToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)範例中建立動畫杆。

### <a name="responding-to-taps"></a>回應點擊

的一個缺點 `EventTrigger` 是，您無法將它附加至 `TapGestureRecognizer` 來回應點擊。 解決這個問題是 FormsBook 中的用途 [`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) [ **。**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

[**BoxViewTapShiver**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver)範例會使用 `TapBehavior` ，將先前的 `ShiverAction` 用於螺紋 `BoxView` 元素。

[**ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews)範例示範如何藉由封裝類別來減少標記 `ShiverView` 。

### <a name="radio-buttons"></a>選項按鈕

[**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫也有一個類別，可 [`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) 讓您按組名分組的選項按鈕 `string` 。

[**RadioLabels**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels)程式會使用文字字串作為其選項按鈕。 [**RadioStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle)範例會使用 `Style` ，來表示 checked 和 unchecked 按鈕之間的外觀差異。 [**RadioImages**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages)範例會使用已裝箱的影像作為其選項按鈕：

[![收音機影像的三向螢幕擷取畫面](images/ch23fg17-small.png "選項按鈕影像")](images/ch23fg17-large.png#lightbox "選項按鈕影像")

[**TraditionalRadios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios)範例會以圓形內的點繪製傳統的顯示選項按鈕。

### <a name="fades-and-orientation"></a>淡出和方向

最後一個範例是[**MultiColorSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) ，可讓您使用選項按鈕在三個不同的色彩選取視圖之間切換。 這三個視圖會使用 [`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) [**FormsBook 工具**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的來淡入和縮小。

此程式也會使用 [`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) **FormsBook 工具**庫中的來回應直向和橫向之間的變化。

## <a name="related-links"></a>相關連結

- [第23章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [第23章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [使用觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms問題](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
