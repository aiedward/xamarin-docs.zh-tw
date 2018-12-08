---
title: 第 6 章的摘要。 按下按鈕
description: 使用 Xamarin.Forms 建立行動應用程式： 摘要的第 6 章。 按下按鈕
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2018
ms.locfileid: "53057590"
---
# <a name="summary-of-chapter-6-button-clicks"></a>第 6 章的摘要。 按下按鈕

[![下載範例](~/media/shared/download.png)下載範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

[ `Button` ](xref:Xamarin.Forms.Button)是可讓使用者起始的命令的檢視。 A`Button`由文字 (以及選擇性的映像所示[第 13 章、 點陣圖](chapter13.md))。 因此，`Button`許多相同的屬性會定義`Label`:

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` 也會定義三個屬性用來管理其框線，外觀，但這些屬性和相互獨立的支援是特定的平台：

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) 型別 `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) 型別 `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) 型別 `Double`

`Button` 也會繼承的所有屬性`VisualElement`並`View`，包括`BackgroundColor`， `HorizontalOptions`，和`VerticalOptions`。

## <a name="processing-the-click"></a>處理按一下

`Button`類別會定義[ `Clicked` ](xref:Xamarin.Forms.Button.Clicked)在使用者點選時引發的事件`Button`。 `Click`處理常式的類型是`EventHandler`。 第一個引數`Button`物件，產生事件; 第二個引數為`EventArgs`會提供任何額外資訊的物件。

[ **ButtonLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger)範例會示範簡單`Clicked`處理。

## <a name="sharing-button-clicks"></a>按一下 [共用] 按鈕

多個`Button`檢視可以共用相同`Clicked`處理常式，但此處理常式通常需要來判斷哪一個`Button`負責特定的事件。 其中一個方法是儲存各種`Button`欄位以及哪一個引發的事件處理常式中的核取的物件。

[ **TwoButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons)範例會示範這項技術。 程式也會示範如何設定[ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled)屬性`Button`來`false`時按下`Button`已不再有效。 已停用`Button`不會產生`Clicked`事件。

## <a name="anonymous-event-handlers"></a>匿名事件處理常式

可定義`Clicked`處理常式為匿名的 lambda 函式，作為[ **ButtonLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas)範例會示範。 不過，不需要某些凌亂的反映程式碼不能共用匿名的處理常式。

## <a name="distinguishing-views-with-ids"></a>具有識別碼的特殊檢視

多個`Button`物件也可以藉由設定區別[ `StyleId` ](xref:Xamarin.Forms.Element.StyleId)屬性或[ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId)屬性設`string`。 這個屬性會定義由`Element`但不是使用 Xamarin.Forms 中。 它被專門用於只能由應用程式。

[ **SimplestKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad)範例的數字鍵台上的所有數字 10 個金鑰會使用相同的事件處理常式，並區分它們與`StyleId`屬性：

[![最簡單的數字鍵台的三個螢幕擷取畫面](images/ch06fg04-small.png "計算機")](images/ch06fg04-large.png#lightbox "計算機")

## <a name="saving-transient-data"></a>正在儲存暫時性資料

許多應用程式需要將資料儲存在程式終止時，程式一次啟動時重新載入該資料。 [ `Application` ](xref:Xamarin.Forms.Application)類別會定義數個協助程式儲存和還原的暫時性資料的成員：

- [ `Properties` ](xref:Xamarin.Forms.Application.Properties)屬性是使用字典`string`金鑰和`object`項目。 字典的內容會自動儲存在應用程式在程式結束之前的本機儲存體，並重新載入程式啟動時。
- `Application`類別會定義三個受保護的虛擬方法，該程式的標準`App`類別會覆寫： [ `OnStart` ](xref:Xamarin.Forms.Application.OnStart)， [ `OnSleep` ](xref:Xamarin.Forms.Application.OnSleep)，以及[ `OnResume` ](xref:Xamarin.Forms.Application.OnResume). 這些是指*應用程式生命週期*事件。
- [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync)方法會將儲存字典的內容。

您不需要呼叫`SavePropertiesAsync`。 字典的內容會自動儲存在程式結束之前，並擷取程式啟動前。 最好在程式測試，以儲存資料，如果程式損毀期間。

也很有用是：

- [`Application.Current`](xref:Xamarin.Forms.Application.Current)傳回目前的靜態屬性`Application`物件，您可以使用它來取得`Properties`字典。

第一個步驟是識別您想要保存在程式終止時的頁面上的所有變數。 如果您知道的所有地方，讓這些變數變更時，您可以直接將它們加入`Properties`此時字典。 在頁面的建構函式中，您可以設定之變數的`Properties`如果存在索引鍵的字典。

較大型的程式可能必須處理的應用程式生命週期事件。 最重要的是`OnSleep`方法。 呼叫此方法表示程式離開前景。 可能是使用者已按下**首頁**按鈕在裝置上，或顯示所有的應用程式，或正在關閉電話。 呼叫`OnSleep`是唯一的通知程式接收之前它就會終止。 程式應該利用此機會，請確認`Properties`字典是最新狀態。

呼叫`OnResume`指出程式並未終止之後，最後呼叫`OnSleep`但現在在前景中再次執行。 程式可能會利用這個機會 （舉例來說） 重新整理網際網路連線。

呼叫`OnStart`程式啟動期間發生。 不需要等候，直到此方法呼叫來存取`Properties`字典因為內容已經還原時`App`建構函式呼叫。

[ **PersistentKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad)範例是非常類似**SimplestKeypad**不同之處在於程式會使用`OnSleep`覆寫可儲存目前的數字鍵台輸入和若要還原資料的頁面建構函式。

> [!NOTE]
> 儲存計劃設定的另一種方法由 Xamarin.Essentials[喜好設定](~/essentials/preferences.md)類別。

## <a name="related-links"></a>相關連結

- [第 6 章全文檢索 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [第 6 章範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [第 6 章F#範例](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Xamarin.Forms 按鈕](~/xamarin-forms/user-interface/button.md)
