---
title: 第 6 章的摘要。 按一下按鈕
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: a45d1f1663b141912744e83763e7d618866159d7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-6-button-clicks"></a>第 6 章的摘要。 按一下按鈕

[ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/)是檢視，可讓使用者可以起始命令。 A`Button`文字所識別 (以及選擇性的映像中所示[第 13 章、 點陣圖](chapter13.md))。 因此，`Button`定義許多相同的內容`Label`:

- [`Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Text/)
- [`FontFamily`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontFamily/)
- [`FontSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontSize/)
- [`FontAttributes`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.FontAttributes/)
- [`TextColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.TextColor/)

`Button` 也會定義三個屬性，控制其框線的外觀，而這些屬性和相互獨立的支援平台特定：

- [`BorderColor`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderColor/) 型別 `Color`
- [`BorderWidth`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderWidth/) 型別 `Double`
- [`BorderRadius`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.BorderRadius/) 型別 `Double`

`Button` 也會繼承的所有屬性`VisualElement`和`View`，包括`BackgroundColor`， `HorizontalOptions`，和`VerticalOptions`。

## <a name="processing-the-click"></a>處理按一下

`Button`類別會定義[ `Clicked` ](https://developer.xamarin.com/api/event/Xamarin.Forms.Button.Clicked/)在使用者點選時引發的事件`Button`。 `Click`處理常式的類型是`EventHandler`。 第一個引數是`Button`物件產生的事件; 第二個引數是`EventArgs`會提供任何額外資訊的物件。

[ **ButtonLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger)範例會示範簡單`Clicked`處理。

## <a name="sharing-button-clicks"></a>按一下 [共用] 按鈕

多個`Button`檢視可以共用相同`Clicked`處理常式，但此處理常式通常需要決定哪些`Button`負責特定事件。 其中一個方法是儲存各種`Button`物件做為欄位，並檢查哪一個引發的事件處理常式中。

[ **TwoButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons)範例會示範這項技術。 程式也會示範如何設定[ `IsEnabled` ](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsEnabled/)屬性`Button`至`false`時按下`Button`不再有效。 停用 A`Button`不會產生`Clicked`事件。

## <a name="anonymous-event-handlers"></a>匿名的事件處理常式

您可定義`Clicked`處理常式做為匿名 lambda 函式，為[ **ButtonLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas)範例將示範如何。 不過，沒有某些雜亂反映程式碼不能共用匿名的處理常式。

## <a name="distinguishing-views-with-ids"></a>具有識別碼的特殊檢視

多個`Button`物件也可以透過設定區分[ `StyleId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.StyleId/)屬性或[ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/)屬性`string`。 這個屬性由定義`Element`但不是使用 Xamarin.Forms 中。 它被適用於僅由應用程式。

[ **SimplestKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad)範例使用相同的事件處理常式的數字鍵台上的所有數字 10 個金鑰，並區分它們與`StyleId`屬性：

[![三個螢幕擷取畫面的最簡單的數字鍵台](images/ch06fg04-small.png "計算機")](images/ch06fg04-large.png#lightbox "計算機")

## <a name="saving-transient-data"></a>儲存暫時性資料

許多應用程式需要將資料儲存在程式終止時，以及程式重新啟動時重新載入該資料。 [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/)類別會定義協助程式儲存及還原暫時性資料的數個成員：

- [ `Properties` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Properties/)屬性是使用字典`string`索引鍵和`object`項目。 字典內容會自動在程式結束之前的應用程式本機儲存體中儲存並重新載入程式啟動時。
- `Application`類別會定義三個受保護的虛擬方法，該程式的標準`App`類別覆寫： [ `OnStart` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnStart()/)， [ `OnSleep` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnSleep()/)，和[ `OnResume` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.OnResume()/). 這些是指*應用程式生命週期*事件。
- [ `SavePropertiesAsync` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Application.SavePropertiesAsync()/)方法儲存的字典內容。

不需要呼叫`SavePropertiesAsync`。 自動儲存在程式結束之前並擷取程式啟動前字典內容。 會很有用的程式，如果程式損毀，將資料儲存在測試期間。

也有用是：

- [`Application.Current`](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.Current/)傳回目前的靜態屬性`Application`物件，您可以使用它來取得`Properties`字典。

第一個步驟是識別您想要保存在程式終止時的頁面上的所有變數。 如果您知道所有這些變數在其中變更的地方，您可以直接將它們加入`Properties`此時字典。 在頁面的建構函式，您可以設定從變數`Properties`字典，如果存在機碼。

較大型的程式可能需要處理的應用程式生命週期事件。 最重要的是`OnSleep`方法。 這個方法的呼叫表示程式已離開前景。 使用者已按也許**首頁**按鈕在裝置上，或顯示所有應用程式，或是正在關閉電話。 呼叫`OnSleep`是唯一的通知程式只接收之前它就會終止。 程式應該利用這個機會，以確保`Properties`字典是最新狀態。

呼叫`OnResume`表示程式未終止之後，最後呼叫`OnSleep`但現在在前景中再次執行。 程式可能會利用此機會重新整理網際網路連線 （例如）。

呼叫`OnStart`程式啟動期間發生。 不需要存取呼叫此方法等候`Properties`字典因為內容已經被還原時`App`建構函式呼叫。

[ **PersistentKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad)範例是非常類似於**SimplestKeypad**不同之處在於程式會使用`OnSleep`覆寫可儲存目前的數字鍵台項目，並若要還原的資料頁面建構函式。



## <a name="related-links"></a>相關連結

- [第 6 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [第 6 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [第 6 章 F # 範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
