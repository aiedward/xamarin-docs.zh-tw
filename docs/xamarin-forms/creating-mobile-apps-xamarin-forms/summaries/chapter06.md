---
title: 第6章的摘要。 按鈕點擊
description: 建立 Mobile Apps Xamarin.Forms ：第6章的摘要。 按鈕點擊
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8a95f4a88ea0adff78475dab8699308fbe49aa46
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370204"
---
# <a name="summary-of-chapter-6-button-clicks"></a>第6章的摘要。 按鈕點擊

[![下載範例](~/media/shared/download.png) 下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

> [!NOTE]
> 這本書是在2016的春季發行，而且自那時起尚未更新。 本書中有很多工具價值，但部分資料已過期，有些主題則不再是完全正確或完整。

[`Button`](xref:Xamarin.Forms.Button)是可讓使用者起始命令的視圖。 `Button`是由文字 (以及選擇性的影像（如第[13 章的點陣圖](chapter13.md)) 所示）來識別。 因此，會 `Button` 定義許多相同的屬性，如下所示 `Label` ：

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` 也會定義管理其框線外觀的三個屬性，但是這些屬性和其相互獨立性的支援是平臺專用的：

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 型別為 `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 型別為 `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) 型別為 `Double`

`Button` 也會繼承和的所有 `VisualElement` 屬性 `View` ，包括 `BackgroundColor` 、 `HorizontalOptions` 和 `VerticalOptions` 。

## <a name="processing-the-click"></a>處理按一下

`Button`類別 [`Clicked`](xref:Xamarin.Forms.Button.Clicked) 會定義使用者點擊時所引發的事件 `Button` 。 `Click`處理常式的類型為 `EventHandler` 。 第一個引數是 `Button` 產生事件的物件; 第二個引數是不 `EventArgs` 提供其他資訊的物件。

[**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger)範例示範簡單 `Clicked` 的處理。

## <a name="sharing-button-clicks"></a>共用按鈕點擊

多個 `Button` 視圖可以共用相同的 `Clicked` 處理常式，但處理常式通常需要決定哪 `Button` 一個負責特定的事件。 其中一個方法是將各種 `Button` 物件儲存為欄位，並檢查哪一個物件正在引發處理常式中的事件。

[**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons)範例會示範這項技巧。 此程式也會示範如何 [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) `Button` `false` 在按下不再有效時，將的屬性設定為 `Button` 。 Disabled 不 `Button` 會產生 `Clicked` 事件。

## <a name="anonymous-event-handlers"></a>匿名事件處理常式

如 ButtonLambdas 範例所示，您可以將 `Clicked` 處理常式定義為 [**ButtonLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas)匿名 lambda 函數。 但是，如果沒有一些雜亂的反映程式碼，就無法共用匿名處理常式。

## <a name="distinguishing-views-with-ids"></a>區分識別碼的視圖

您 `Button` 也可以透過將 [`StyleId`](xref:Xamarin.Forms.Element.StyleId) 屬性或屬性設定為，來區別多個物件 [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) `string` 。 這個屬性是由定義， `Element` 但不會在中使用 Xamarin.Forms 。 它僅供應用程式使用。

[**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad)範例會在數位鍵臺上使用相同的事件處理常式來處理所有10個數字鍵，並以屬性區分它們 `StyleId` ：

[![最簡單鍵盤的三重螢幕擷取畫面](images/ch06fg04-small.png "計算機")](images/ch06fg04-large.png#lightbox "計算機")

## <a name="saving-transient-data"></a>儲存暫時性資料

許多應用程式都需要在程式終止時儲存資料，並在程式重新開機時重載該資料。 [`Application`](xref:Xamarin.Forms.Application)類別會定義數個可協助程式儲存和還原暫時性資料的成員：

- [`Properties`](xref:Xamarin.Forms.Application.Properties)屬性是含有索引 `string` 鍵和專案的字典 `object` 。 字典的內容會在程式終止之前自動儲存在應用程式本機儲存體中，並在程式啟動時重載。
- `Application`類別會定義程式的標準類別所覆寫的三個受保護虛擬方法 `App` ： [`OnStart`](xref:Xamarin.Forms.Application.OnStart) 、 [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep) 和 [`OnResume`](xref:Xamarin.Forms.Application.OnResume) 。 這些是指 *應用程式生命週期* 事件。
- 方法會儲存 [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) 字典的內容。

不需要呼叫 `SavePropertiesAsync` 。 在程式終止之前，會自動儲存字典的內容，並在程式啟動前先行取出。 這在程式測試期間很有用，可在程式進行測試時儲存資料。

也很有用：

- [`Application.Current`](xref:Xamarin.Forms.Application.Current)，此為靜態屬性，會傳回 `Application` 您可以用來取得字典的目前物件 `Properties` 。

第一個步驟是識別頁面上您想要在程式終止時保存的所有變數。 如果您知道這些變數的變更，您可以直接將它們加入至 `Properties` 字典。 在頁面的函式中，如果索引鍵存在，您可以從字典設定變數 `Properties` 。

較大的程式可能需要處理應用程式生命週期事件。 最重要的是 `OnSleep` 方法。 呼叫這個方法表示程式已離開前景。 可能是使用者已按下裝置上的 [ **首頁** ] 按鈕，或顯示所有應用程式或正在關閉電話。 的呼叫 `OnSleep` 是程式在終止之前所收到的唯一通知。 程式應該會有這個機會，以確保 `Properties` 字典是最新的。

的呼叫 `OnResume` 表示程式未在最後一次呼叫之後終止， `OnSleep` 但現在會再次在前景中執行。 程式可能會使用這個機會來重新整理網際網路連線 (例如) 。

`OnStart`在程式啟動期間發生的呼叫。 由於呼叫函式時已經還原內容，因此不需要等到這個方法呼叫存取 `Properties` 字典 `App` 。

[**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad)範例與 **SimplestKeypad** 非常類似，不同之處在于程式會使用覆 `OnSleep` 寫來儲存目前的鍵盤專案，以及使用頁面的函式來還原該資料。

> [!NOTE]
> 儲存程式設定的另一種方法是由 Xamarin.Essentials [喜好](~/essentials/preferences.md) 設定類別所提供。

## <a name="related-links"></a>相關連結

- [第6章完整文字 (PDF) ](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [第6章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [第6章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Xamarin.Forms 按鈕](~/xamarin-forms/user-interface/button.md)
