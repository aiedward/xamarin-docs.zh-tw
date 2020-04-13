---
title: 第23章摘要。 觸發程序和行為
description: 使用 Xamarin.表單創建行動應用程式:第 23 章摘要。 觸發程序和行為
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 8a1274a8447f49ce39f9c92703bbaec9e875b9e9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760586"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>第23章摘要。 觸發程序和行為

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

觸發器和行為是相似的,因為它們都打算用於 XAML 檔中,以簡化元素交互,而超出數據綁定的使用範圍,並擴展 XAML 元素的功能。 觸發器和行為幾乎總是與可視用戶介面物件一起使用。

要支援觸發器和行為,兩`VisualElement``Style`者都支援兩個集合屬性:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)與[`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers)類型`IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)與[`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors)類型`IList<Behavior>`

## <a name="triggers"></a>觸發程序

觸發器是導致回應(另一個屬性更改或運行某些代碼)的條件(屬性更改或事件觸發)。 的屬性`Triggers``VisualElement``Style`是`IList<TriggersBase>`。 [`TriggerBase`](xref:Xamarin.Forms.TriggerBase)是一個抽象類,其中四個密封類派生:

- [`Trigger`](xref:Xamarin.Forms.Trigger)基於屬性變更的回應
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger)基於事件觸發的回應
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)基於資料繫結的回應
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)基於多個觸發器的回應

觸發器始終設置在觸發器更改其屬性的元素上。

### <a name="the-simplest-trigger"></a>最簡單的觸發器

類[`Trigger`](xref:Xamarin.Forms.Trigger)檢查屬性值的更改,並通過設置同一元素的另一個屬性進行回應。

`Trigger`定義三個屬性:

- [`Property`](xref:Xamarin.Forms.Trigger.Property)類型`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value)類型`Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters)的類型`IList<SetterBase>`,內容屬性`Trigger`

此外,`Trigger`要求設定從繼承`TriggerBase`的以下屬性:

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType)指示附加的元素型態`Trigger`

`Property`和`Value`組成條件,`Setters`集合是回應。 當指示`Property`的值由指示`Value`時`Setter`, 將應用`Setters`集合中 的物件。 當`Property`具有不同的值時,將刪除 setter。 `Setter`定義兩個屬性與的前兩個屬性相同`Trigger`:

- [`Property`](xref:Xamarin.Forms.Setter.Property)類型`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value)類型`Object`

[**"EntryPop"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop)範例展示`Trigger`了 應用`Entry`於`Entry`的`Scale``IsFocused``Entry`,`true`當 的屬性 為 時,如何可以通過 屬性增加的大小。

儘管這並不常見,但可以在代碼中`Trigger`設置,如[**EntryPopCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode)範例所示。

[**樣式觸發器**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers)範例展示`Trigger`如何 在`Style`中設置`Entry`以應用於多個 元素。

### <a name="trigger-actions-and-animations"></a>觸發操作和動畫

也可以根據觸發器運行一些代碼。 此代碼可以是以屬性為目標的動畫。 一種常見方法是使用[`EventTrigger`](xref:Xamarin.Forms.EventTrigger)定義兩個屬性的 。

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event)型`string`態 ,事件的名稱
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions)類型`IList<TriggerAction>`,要在回應中運行的操作清單。

要使用此,您需要編寫派生自[`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1)的類`TriggerAction<VisualElement>`。 可以在此類中定義屬性。 這些是普通的 CLR 屬性,而不是可綁`TriggerAction`定屬性 ,因為不`BindableObject`派生自 。 必須重寫調用操作[`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*)時調用的方法。 參數是目標元素。

[`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中的類就是一個示例。 它調用`ScaleTo`屬性以為`Scale`元素 的屬性設置動畫。 因為它的屬性之一是類型`Easing`,[`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs)因此 類允許您在`Easing`XAML 中使用標準靜態欄位。

[**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell)`ScaleAction`範例展示`EventTrigger`如何 從`Focused``Unfocused`監控與 事件的物件呼叫 。

[**CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell)範例展示如何`ScaleAction`在代碼背後的檔中定義自定義緩動函數。

您還可以使用`Trigger`調用 操作`EventTrigger`(與 區分。 這要求您知道定義`TriggerBase`兩個集合:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions)類型`IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions)類型`IList<TriggerAction>`

[**EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell)範例展示如何使用這些集合。

### <a name="more-event-triggers"></a>更多事件觸發器

[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫`ScaleTo`[`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs)中的 類調用兩次以向上和向下擴展。 [**ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth)示例在樣`EventTrigger`式 中使用此功能,在按`Button`下 時 提供可視反饋。 此雙重動畫也可以使用類型集合中的兩個操作[`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

[`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) **Xamarin.FormsBook.Toolkit**庫中的類定義了可自定義的抖動操作。 [**ShiverButtonDemo 示例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo)演示了它。

[`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) **Xamarin.FormsBook.Toolkit**庫中的類僅限`Entry`於 元素`TextColor``Text`,如果 屬性不是 ,則將屬性`double`設置為紅色。 [**觸發器入口驗證**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation)示例演示了它。

### <a name="data-triggers"></a>資料觸發器

[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)與類似,`Trigger`只不過,它監視數據綁定不是監視屬性的值更改。 這允許一個元素中的屬性影響另一個元素中的屬性。

`DataTrigger`定義三個屬性:

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding)類型`BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value)類型`Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters)類型`IList<SetterBase>`

[**性別顏色**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors)示例需要[**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt)庫`Sex`,並根據 屬性將學生姓名的顏色設置為藍色或粉紅色:

[![性別顏色的三重截圖](images/ch23fg04-small.png "性別顏色")](images/ch23fg04-large.png#lightbox "性別顏色")

如果 屬性屬性`False``Length``Text``IsEnabled``Entry`等於 0,`Entry`則[**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler)範例設定到 請注意,該`Text`屬性已初始化為空字串;但是,該屬性將初始化為空字串。預設情況下,它是`null`,`DataTrigger`並且無法正常工作。

### <a name="combining-conditions-in-the-multitrigger"></a>組合多觸發器中的條件

[`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)是條件的集合。 當它們全部`true`時,然後應用設置器。 該類定義兩個屬性:

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions)類型`IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters)類型`IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition)是一個抽象類,有兩個後代類:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition),其[`Property`](xref:Xamarin.Forms.PropertyCondition.Property)[`Value`](xref:Xamarin.Forms.PropertyCondition.Value)屬性 如`Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition),其[`Binding`](xref:Xamarin.Forms.BindingCondition.Binding)[`Value`](xref:Xamarin.Forms.BindingCondition.Value)屬性 如`DataTrigger`

在[**And條件**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions)示例中,`BoxView`僅`Switch`當四個元素全部打開時,才會著色。

[**Or條件**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions)範例展示如何在打開`Switch`四 個元素`BoxView`中的任何*any*一個時 創建顏色。 這需要應用德摩根定律並扭轉所有邏輯。

組合 AND 和 OR 邏輯並非`Switch`易事,通常需要不可見的元素來獲得中間結果。 [**Xor條件**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions)範例展示`Button`如何 啟用,`Entry`如果兩個元素中的任何一個鍵入了某些文字,但如果它們都鍵入了某些文字,則不能啟用 。

## <a name="behaviors"></a>行為

任何可以使用觸發器執行的任何操作都可以對行為執行,但行為始終需要派生並[`Behavior<T>`](xref:Xamarin.Forms.Behavior`1)重寫以下兩種方法的類:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

參數是行為附加到的元素。 通常,`OnAttachedTo`該方法附加一些事件處理程式,`OnDetachingFrom`並分離它們。 由於此類類通常保存某些狀態,因此通常無法在中`Style`共用。

[**行為入口驗證**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation)示例類似於**觸發器入口驗證**,只不過它使用[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中&mdash;[`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs)的類行為。

### <a name="behaviors-with-properties"></a>具有屬性的行為

`Behavior<T>`派生自`Behavior`派生`BindableObject`自 派生的 ,因此可以在行為上定義可綁定屬性。 這些屬性可以在數據綁定中處於活動狀態。

這一點在使用[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)[`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs)庫中的 類[**的電子郵件驗證演示**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo)程式中進行了演示。 `ValidEmailBehavior`具有唯讀可綁定屬性,並在數據綁定中充當源。

[**EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv)範例使用此相同的行為來顯示另一種類型的指示器,以指示電子郵寄地址有效。

[**電子郵件驗證觸發器**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger)示例是上一個範例的變體。 ButtonGlide`DataTrigger`與 該行為結合使用。

### <a name="toggles-and-check-boxes"></a>切換與選取的盒

可以在類(如[`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) [**Xamarin.FormsBook.Toolkit)**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫中封裝切換按鈕的行為,然後在 XAML 中完全定義切換的所有視覺物件。

[**切換標籤「**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel)顯示例」`ToggleBehavior``DataTrigger`的使用`Label`與的切換的具有兩個文字字串的 。

[**格式化文字切換**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle)示例通過在兩個對象之間切換來擴展`FormattedString`這一 概念。

[`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) **Xamarin.FormsBook.Toolkit**庫中的類派生`ContentView`自 ,`IsToggled`定義屬性`ToggleBehavior`,並合併 用於切換邏輯。 這樣可以更輕鬆地在 XAML 中定義切換按鈕,如[**傳統CheckBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox)示例所示。

[**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo)包括派[`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs)生`ToggleBase`的 類[`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs),並使用 類構造類似於 Xamarin.Forms`Switch`的切換按鈕。

[`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) **Xamarin.FormsBook.Toolkit**中的 A 提供了一個動畫,用於在[**『槓桿切換**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)』示例中製作動畫槓桿。

### <a name="responding-to-taps"></a>回應點選

一個`EventTrigger`缺點是,你不能將其附加`TapGestureRecognizer`到 響應點擊。 解決這個問題是[`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs)[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)中的目的

[**BoxViewTapShiver 範例**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver)用於`TapBehavior`使用較`ShiverAction`早的`BoxView`已攻取 元素。

[**ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews)示例演示如何通過封`ShiverView`裝 類來減少標記。

### <a name="radio-buttons"></a>選項按鈕

[**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)庫還有一[`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs)個 類,用於`string`製作按 組名稱分組的單選按鈕。

[**"無線電標籤"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels)程式使用文字字串作為單選按鈕。 [**"RadioStyle"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle)範例`Style`使用 用於檢查和未選中按鈕之間的外觀差異。 [**"RadioImages"**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages)範例使用盒裝影像來按下按鈕:

[![無線電影像的三重截圖](images/ch23fg17-small.png "收音機按鈕影像")](images/ch23fg17-large.png#lightbox "收音機按鈕影像")

[**傳統電臺**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios)示例在圓內繪製帶有點的傳統顯示單選按鈕。

### <a name="fades-and-orientation"></a>淡入淡出與方向

最後一個示例[**「多色滑塊」**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders)允許您使用單選按鈕在三個不同的顏色選擇視圖之間切換。 使用[`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)函式庫中的

該程式還使用[`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs)**Xamarin.FormsBook.Toolkit**庫中的 回應縱向和橫向之間的方向變化。

## <a name="related-links"></a>相關連結

- [第23章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [第23章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [使用觸發程序](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.Forms 行為](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
