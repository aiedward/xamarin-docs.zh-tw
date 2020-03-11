---
title: 第6章的摘要。 按一下按鈕
description: 使用 Xamarin 建立 Mobile Apps：第6章的摘要。 按一下按鈕
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334548"
---
# <a name="summary-of-chapter-6-button-clicks"></a>第6章的摘要。 按一下按鈕

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

[`Button`](xref:Xamarin.Forms.Button)是允許使用者起始命令的視圖。 `Button` 是以文字識別（並選擇性地指定影像，如第[13 章、點陣圖](chapter13.md)所示）。 因此，`Button` 定義了許多與 `Label`相同的屬性：

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` 也會定義三個控制其框線外觀的屬性，但這些屬性和其相互獨立性的支援是特定平臺：

- 類型為 [ 的 `BorderColor`](xref:Xamarin.Forms.Button.BorderColor)`Color`
- 類型為 [ 的 `BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth)`Double`
- 類型為 [ 的 `BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)`Double`

`Button` 也會繼承 `VisualElement` 和 `View`的所有屬性，包括 `BackgroundColor`、`HorizontalOptions`和 `VerticalOptions`。

## <a name="processing-the-click"></a>處理點擊

`Button` 類別會定義當使用者按 `Button`時所引發的[`Clicked`](xref:Xamarin.Forms.Button.Clicked)事件。 `Click` 處理常式屬於 `EventHandler`類型。 第一個引數是產生事件的 `Button` 物件;第二個引數是不提供其他資訊的 `EventArgs` 物件。

[**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger)範例示範簡單的 `Clicked` 處理。

## <a name="sharing-button-clicks"></a>共用按鈕按一下

多個 `Button` views 可以共用相同的 `Clicked` 處理常式，但處理常式通常需要判斷哪個 `Button` 負責特定事件。 其中一種方法是將各種 `Button` 物件儲存為欄位，並檢查哪一個會在處理常式中引發事件。

[**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons)範例會示範這項技術。 此程式也會示範如何將 `Button` 的[`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)屬性設定為 `false` 當按下 `Button` 時，將不再有效。 停用的 `Button` 不會產生 `Clicked` 事件。

## <a name="anonymous-event-handlers"></a>匿名事件處理常式

如[**ButtonLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas)範例所示，您可以將 `Clicked` 處理常式定義為匿名 lambda 函式。 不過，如果沒有一些雜亂的反映程式碼，就無法共用匿名處理常式。

## <a name="distinguishing-views-with-ids"></a>區分識別碼的視圖

藉由將[`StyleId`](xref:Xamarin.Forms.Element.StyleId)屬性或[`AutomationId`](xref:Xamarin.Forms.Element.AutomationId)屬性設定為 `string`，也可以區別多個 `Button` 物件。 這個屬性是由 `Element` 所定義，但不會在 Xamarin 中使用。 僅供應用程式使用。

[**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad)範例會針對數位鍵臺上的所有10個數字鍵使用相同的事件處理常式，並使用 `StyleId` 屬性加以區分：

[![最簡單鍵盤的三向螢幕擷取畫面](images/ch06fg04-small.png "計算機")](images/ch06fg04-large.png#lightbox "計算機")

## <a name="saving-transient-data"></a>儲存暫時性資料

許多應用程式都需要在程式終止時儲存資料，並在程式重新開機時重載該資料。 [`Application`](xref:Xamarin.Forms.Application)類別會定義數個可協助您的程式儲存和還原暫時性資料的成員：

- [`Properties`](xref:Xamarin.Forms.Application.Properties)屬性是具有 `string` 索引鍵和 `object` 專案的字典。 字典的內容會在程式終止之前自動儲存在應用程式本機儲存體中，並在程式啟動時重載。
- `Application` 類別會定義程式的標準 `App` 類別所覆寫的三個受保護虛擬方法： [`OnStart`](xref:Xamarin.Forms.Application.OnStart)、 [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep)和[`OnResume`](xref:Xamarin.Forms.Application.OnResume)。 這些是指*應用程式生命週期*事件。
- [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync)方法會儲存字典的內容。

不需要呼叫 `SavePropertiesAsync`。 字典的內容會在程式終止之前自動儲存，並在程式啟動前先行抓取。 在程式測試期間，如果程式損毀就儲存資料，這會很有用。

也適用于：

- [`Application.Current`](xref:Xamarin.Forms.Application.Current)，這是一個靜態屬性，它會傳回目前的 `Application` 物件，讓您可以用來取得 `Properties` 的字典。

第一個步驟是在程式終止時，識別頁面上您想要保存的所有變數。 如果您知道這些變數的變更位置，可以直接將它們加入至 `Properties` 字典。 在頁面的函式中，如果索引鍵存在，您可以設定 `Properties` 字典中的變數。

較大的程式可能需要處理應用程式生命週期事件。 最重要的是 `OnSleep` 方法。 呼叫這個方法會指出程式已離開前景。 可能是使用者已按下裝置上的 [**首頁**] 按鈕，或顯示所有應用程式，或是正在關閉電話。 `OnSleep` 的呼叫是程式在終止之前所收到的唯一通知。 程式應該會有這個機會，以確保 `Properties` 字典是最新的。

對 `OnResume` 的呼叫表示程式在上一次呼叫 `OnSleep` 後並未終止，但現在已在前景中再次執行。 此程式可能會使用此機會來重新整理網際網路連線（例如）。

在程式啟動期間，會呼叫 `OnStart`。 您不需要等到這個方法呼叫來存取 `Properties` 字典，因為在呼叫 `App` 的函式時，內容已經還原。

[**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad)範例與**SimplestKeypad**非常類似，不同之處在于程式會使用 `OnSleep` 覆寫來儲存目前的小數位數專案，以及用來還原該資料的頁面的函式。

> [!NOTE]
> 儲存程式設定的另一種方法是由 Xamarin[喜好](~/essentials/preferences.md)設定類別所提供。

## <a name="related-links"></a>相關連結

- [第6章全文檢索（PDF）](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [第6章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [第 6 F#章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [[Xamarin] 按鈕](~/xamarin-forms/user-interface/button.md)
