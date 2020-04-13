---
title: 第6章摘要。 按鈕點擊
description: 使用 Xamarin.表單創建行動應用程式:第 6 章摘要。 按鈕點擊
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334548"
---
# <a name="summary-of-chapter-6-button-clicks"></a>第6章摘要。 按鈕點擊

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

是[`Button`](xref:Xamarin.Forms.Button)允許使用者啟動命令的檢視。 A`Button`由文本識別(並選擇性地標識圖像,如[第13章"位圖](chapter13.md)"所示)。 因此,`Button`定義許多相同的屬性`Label`與 :

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button`還定義了管理其邊框外觀的三個屬性,但這些屬性的支援及其相互獨立性是特定於平臺的:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor)類型`Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth)類型`Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)類型`Double`

`Button`還繼承`VisualElement`與`View`所有屬性,包括`BackgroundColor`與`HorizontalOptions` `VerticalOptions` 。

## <a name="processing-the-click"></a>處理單擊

類`Button`定義[`Clicked`](xref:Xamarin.Forms.Button.Clicked)在用戶點擊`Button`時 觸發的事件。 處理程式`Click`的類型`EventHandler`為 。 第一個參數是`Button`生成事件的物件;第二個參數是生成事件的物件。第二個參數是`EventArgs`一個不提供其他資訊的物件。

[**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger)範例展示`Clicked`了簡單的處理。

## <a name="sharing-button-clicks"></a>共用按鈕按下

多個`Button`檢視可以共用同一`Clicked`處理程式,但處理程式通常需要確定`Button`哪個 檢視負責特定事件。 一種方法是將各種`Button`物件存儲為欄位,並檢查在處理程式中觸發事件的物件。

[**兩個按鈕**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons)示例演示了此技術。 程式還[`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled)演示如何在按`Button``false``Button`時設置為 的 屬性不再有效。 關閉`Button`不會產生事件`Clicked`。

## <a name="anonymous-event-handlers"></a>匿名事件處理常式

正如`Clicked`[**ButtonLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas)範例所示,可以將處理程式定義為匿名 lambda 函數。 但是,如果沒有一些混亂的反射代碼,無法共用匿名處理程式。

## <a name="distinguishing-views-with-ids"></a>以辨識器分割檢視

還可以`Button`通過[`StyleId`](xref:Xamarin.Forms.Element.StyleId)將[`AutomationId`](xref:Xamarin.Forms.Element.AutomationId)屬性或`string`屬性設置為 來區分多個物件。 此屬性由`Element`定義 ,但它不在 Xamarin.Forms 中使用。 它僅供應用程式使用。

[**最簡單的 Keypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad)範例對數位鍵盤上的所有 10 個數字鍵使用相同的事件處理`StyleId`程式,並使用 屬性區分它們:

[![最簡單的鍵盤的三重螢幕截圖](images/ch06fg04-small.png "Calculator")](images/ch06fg04-large.png#lightbox "Calculator")

## <a name="saving-transient-data"></a>儲存暫態資料

許多應用程式需要在程式終止時保存數據,並在程式再次啟動時重新載入該資料。 該[`Application`](xref:Xamarin.Forms.Application)類別定義了幾個成員,這些成員可説明您的程式儲存和復態資料:

- 該[`Properties`](xref:Xamarin.Forms.Application.Properties)屬性是一個帶`string`有`object`鍵和項的字典。 字典的內容在程式終止之前自動儲存在應用程式本地存儲中,並在程式啟動時重新載入。
- 這個`Application`類別`App`定義的標準類別覆蓋的三個受保護的虛擬方法:[`OnStart`](xref:Xamarin.Forms.Application.OnStart)與[`OnSleep`](xref:Xamarin.Forms.Application.OnSleep) [`OnResume`](xref:Xamarin.Forms.Application.OnResume) 。 這些引用*應用程式生命週期*事件。
- 該方法[`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync)保存字典的內容。

沒有必要打電話`SavePropertiesAsync`。 字典的內容在程式終止之前自動保存,並在程式啟動之前檢索。 在程序測試期間,在程序崩潰時保存數據非常有用。

也很有用的是:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current)返回當前`Application`物件的靜態屬性,然後可用於`Properties`獲取 字典。

第一步是標識程序終止時要保留的頁面上的所有變數。 如果您知道這些變數更改的所有位置,則可以在此時將它們添加到`Properties`字典中。 在頁面的構造函數中,如果密鑰存在,則可以從`Properties`字典中設置變數。

較大的程式可能需要處理應用程式生命週期事件。 最重要的是方法`OnSleep`。 對此方法的調用指示程式已離開前臺。 使用者可能按下了設備上的 **「主頁」** 按鈕,或顯示了所有應用程式,或者正在關閉手機。 調用`OnSleep`是程式在終止之前收到的唯一通知。 程式應藉此機會確保`Properties`字典是最新的。

呼叫指示`OnResume`程式未在上次呼叫後終止`OnSleep`, 但現在再次在前臺運行。 程式可能會利用這個機會刷新互聯網連接(例如)。

程序`OnStart`啟動期間發生的調用。 不必等到此方法呼叫存取字典`Properties`, 因為在`App`呼叫式建構函數時內容已還原。

[**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad)範例與 **「最簡單鍵盤」** 非常相似,`OnSleep`只是程式使用 重寫來保存當前鍵盤條目,而頁面構造函數則用於還原該資料。

> [!NOTE]
> 另一種保存程式設定的方法由 Xamarin.Essentials[首選項](~/essentials/preferences.md)類提供。

## <a name="related-links"></a>相關連結

- [第六章 全文(PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [第六章 樣本](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [第六章 F# 樣品](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Xamarin.表單按鈕](~/xamarin-forms/user-interface/button.md)
