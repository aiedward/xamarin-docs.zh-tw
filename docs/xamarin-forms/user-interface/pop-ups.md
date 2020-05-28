---
title: ''
description: Xamarin.Forms提供三個類似快顯視窗的使用者介面元素–警示、動作表和提示。 本文示範如何使用警示、動作表和提示 Api 來顯示對話方塊，以詢問使用者簡單的問題、引導使用者完成工作，以及顯示提示。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a7ddd9134b7214b84a883e171d7b0cadaba3390b
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136314"
---
# <a name="display-pop-ups"></a>顯示快顯視窗

[![下載範例 ](~/media/shared/download.png) 下載範例](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

顯示警示、要求使用者進行選擇，或顯示提示是常見的 UI 工作。 Xamarin.Forms在類別上有三個方法， [`Page`](xref:Xamarin.Forms.Page) 可透過快顯視窗與使用者互動： [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 、 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 和 `DisplayPromptAsync` 。 兩者會以適當的原生控制項轉譯在每個平台上。

## <a name="display-an-alert"></a>顯示警示

所有 Xamarin.Forms 支援的平臺都具有強制回應快顯視窗，以提醒使用者或詢問簡單的問題。 若要在中顯示這些警示 Xamarin.Forms ，請 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 在任何上使用方法 [`Page`](xref:Xamarin.Forms.Page) 。 下列程式碼會顯示簡易訊息給使用者：

```csharp
await DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Alert Dialog with One Button")

此範例不會向使用者收集資訊。 警示會以強制回應的方式顯示，關閉後，使用者就可繼續與應用程式互動。

[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*)方法也可用來藉由呈現兩個按鈕並傳回，來捕捉使用者的回應 `boolean` 。 若要從警示取得回應，請為兩個按鈕提供文字並為方法加上 `await`。 當使用者選取其中一個選項後，回答就會傳回程式碼。 請注意下列範例程式碼中的 `async` 和 `await` 關鍵字：

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "有兩個按鈕的警示對話方塊")](pop-ups-images/alert2.png#lightbox "有兩個按鈕的警示對話方塊")

## <a name="guide-users-through-tasks"></a>引導使用者完成工作

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) 是 iOS 中常見的 UI 元素。 Xamarin.Forms [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 方法可讓您在跨平臺應用程式中包含此控制項，並在 ANDROID 和 UWP 中呈現原生替代專案。

若要在任何情況下顯示動作表， `await` [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) [`Page`](xref:Xamarin.Forms.Page) 請將訊息和按鈕標籤當做字串傳遞。 方法會傳回使用者所按按鈕的字串標籤。 簡易範例如下所示︰

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "ActionSheet Dialog")

`destroy` 按鈕的轉譯方式不同於其他按鈕，可以保留 `null`，也可以指定為第三個字串參數。 下列範例使用 `destroy` 按鈕︰

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "具有損毀按鈕的動作表對話方塊")](pop-ups-images/action2.png#lightbox "具有損毀按鈕的動作表對話方塊")

## <a name="display-a-prompt"></a>顯示提示

若要顯示提示，請 `DisplayPromptAsync` 在 any 中呼叫 [`Page`](xref:Xamarin.Forms.Page) ，並將標題和訊息當做 `string` 引數傳遞：

```csharp
string result = await DisplayPromptAsync("Question 1", "What's your name?");
```

系統會以強制回應方式顯示提示：

[![在 iOS 和 Android 上強制回應提示的螢幕擷取畫面](pop-ups-images/simple-prompt.png "強制回應提示")](pop-ups-images/simple-prompt-large.png#lightbox "強制回應提示")

如果按下 [確定] 按鈕，則會以形式傳回輸入的回應 `string` 。 如果按下 [取消] 按鈕， `null` 則會傳回。

方法的完整引數清單 `DisplayPromptAsync` 是：

- `title`，屬於類型 `string` ，這是要在提示字元中顯示的標題。
- `message`，屬於類型 `string` ，這是要在提示字元中顯示的訊息。
- `accept`，屬於類型 `string` ，是 [接受] 按鈕的文字。 這是選擇性引數，其預設值為 OK。
- `cancel`，屬於類型 `string` ，是 [取消] 按鈕的文字。 這是選擇性引數，其預設值為 Cancel。
- `placeholder`，屬於類型 `string` ，這是要在提示中顯示的預留位置文字。 這是選擇性引數，其預設值為 `null` 。
- `maxLength`，屬於類型 `int` ，是使用者回應的最大長度。 這是選擇性引數，其預設值為-1。
- `keyboard`，屬於類型 `Keyboard` ，是用於使用者回應的鍵盤類型。 這是選擇性引數，其預設值為 `Keyboard.Default` 。
- `initialValue`（屬於類型 `string` ）是預先定義的回應，將會顯示並可供編輯。 這是選擇性引數，其預設值為空的 `string` 。

下列範例會示範如何設定一些選擇性引數：

```csharp
string result = await DisplayPromptAsync("Question 2", "What's 5 + 5?", initialValue: "10", maxLength: 2, keyboard: Keyboard.Numeric);
```

此程式碼會顯示預先定義的回應10、將可輸入的字元數限制為2，並顯示數字鍵台以進行使用者輸入：

[![在 iOS 和 Android 上強制回應提示的螢幕擷取畫面](pop-ups-images/keyboard-prompt.png "強制回應提示")](pop-ups-images/keyboard-prompt-large.png#lightbox "強制回應提示")

## <a name="related-links"></a>相關連結

- [PopupsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
