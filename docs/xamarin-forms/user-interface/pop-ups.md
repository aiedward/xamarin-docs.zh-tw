---
title: 顯示快顯視窗
description: Xamarin.Forms 提供三個類似快顯視窗的使用者介面元素–警示、動作表和提示。 本文示範如何使用警示、動作表和提示 Api 來顯示對話方塊，以詢問使用者簡單的問題、引導使用者完成工作，以及顯示提示。
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 05ad6af1b094a980f5e226bcde22b43764576652
ms.sourcegitcommit: 322e7bcf9fb8c1ad52ab8e929bea95d45e280834
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751473"
---
# <a name="display-pop-ups"></a>顯示快顯視窗

[![下載範例](~/media/shared/download.png) 下載範例](/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

顯示警示、要求使用者進行選擇，或顯示提示是常見的 UI 工作。 Xamarin.Forms 在類別上有三個方法， [`Page`](xref:Xamarin.Forms.Page) 可透過快顯視窗與使用者互動： [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 、 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 和 `DisplayPromptAsync` 。 兩者會以適當的原生控制項轉譯在每個平台上。

## <a name="display-an-alert"></a>顯示警示

所有 Xamarin.Forms 支援的平臺都有強制回應快顯視窗，可警示使用者或詢問簡單的問題。 若要在中顯示這些警示 Xamarin.Forms ，請 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 在任何上使用方法 [`Page`](xref:Xamarin.Forms.Page) 。 下列程式碼會顯示簡易訊息給使用者：

```csharp
await DisplayAlert ("Alert", "You have been alerted", "OK");
```

[![IOS 和 Android 上有一個按鈕的警示對話方塊](pop-ups-images/simple-alert.png)](pop-ups-images/simple-alert-large.png#lightbox)

此範例不會向使用者收集資訊。 警示會以強制回應的方式顯示，關閉後，使用者就可繼續與應用程式互動。

[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*)方法也可以藉由呈現兩個按鈕並傳回，來捕獲使用者的回應 `boolean` 。 若要從警示取得回應，請為兩個按鈕提供文字並為方法加上 `await`。 當使用者選取其中一個選項後，回答就會傳回程式碼。 請注意下列範例程式碼中的 `async` 和 `await` 關鍵字：

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![有兩個按鈕的警示對話方塊](pop-ups-images/two-button-alert.png)](pop-ups-images/two-button-alert.png#lightbox)

[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*)方法也具有可接受引數的多載，此 [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) 引數會指定 UI 元素在警示內的流動方向。 如需流程方向的詳細資訊，請參閱由 [右至左的當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

> [!WARNING]
> 根據預設，在 UWP 上，警示會顯示在警示後方的頁面上定義的任何存取金鑰，仍然可以啟用。 如需詳細資訊，請參閱 [Windows 上的 VisualElement 存取金鑰](~/xamarin-forms/platform/windows/visualelement-access-keys.md)。

## <a name="guide-users-through-tasks"></a>引導使用者完成工作

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) 是 iOS 中常見的 UI 元素。 此 Xamarin.Forms [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 方法可讓您在跨平臺應用程式中包含此控制項，在 ANDROID 和 UWP 中轉譯原生替代專案。

若要顯示任何的動作表，請以 `await` [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) [`Page`](xref:Xamarin.Forms.Page) 字串形式傳遞訊息和按鈕標籤。 方法會傳回使用者所按按鈕的字串標籤。 簡易範例如下所示︰

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

[![IOS 和 Android 上的 [引導] 對話方塊](pop-ups-images/simple-actionsheet.png)](pop-ups-images/simple-actionsheet-large.png#lightbox)

按鈕的轉譯 `destroy` 方式不同于 iOS 上的其他按鈕，而且可以保留 `null` 或指定為第三個字串參數。 下列範例使用 `destroy` 按鈕︰

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![IOS 和 Android 上具有終結按鈕的引導對話方塊](pop-ups-images/actionsheet-destroy-button.png)](pop-ups-images/actionsheet-destroy-button-large.png#lightbox)

[`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)方法也具有可接受引數的多載，這個 [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) 引數會指定 UI 元素在動作表中的流動方向。 如需流程方向的詳細資訊，請參閱由 [右至左的當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)。

## <a name="display-a-prompt"></a>顯示提示

若要顯示提示，請呼叫 `DisplayPromptAsync` 任何 [`Page`](xref:Xamarin.Forms.Page) ，並傳遞標題和訊息作為 `string` 引數：

```csharp
string result = await DisplayPromptAsync("Question 1", "What's your name?");
```

系統會以強制回應方式顯示提示：

[![螢幕擷取畫面： iOS 和 Android 上的強制回應提示](pop-ups-images/simple-prompt.png "強制回應提示")](pop-ups-images/simple-prompt-large.png#lightbox "強制回應提示")

如果點擊 [確定] 按鈕，則會傳回輸入的回應做為 `string` 。 如果點擊 [取消] 按鈕， `null` 則會傳回。

方法的完整引數清單 `DisplayPromptAsync` 為：

- `title`型別為的， `string` 是要顯示在提示字元中的標題。
- `message`型別為的， `string` 是要顯示在提示字元中的訊息。
- `accept`的類型 `string` 是 [接受] 按鈕的文字。 這是選擇性的引數，其預設值為 OK。
- `cancel`型別為的， `string` 是 [取消] 按鈕的文字。 這是選擇性的引數，其預設值為 [取消]。
- `placeholder`型別為的， `string` 是要顯示在提示字元中的預留位置文字。 這是選擇性的引數，其預設值為 `null` 。
- `maxLength`型別為的， `int` 是使用者回應的最大長度。 這是選擇性的引數，其預設值為-1。
- `keyboard`型別為的， `Keyboard` 是用於使用者回應的鍵盤型別。 這是選擇性的引數，其預設值為 `Keyboard.Default` 。
- `initialValue`型別為的， `string` 是預先定義的回應，會顯示並可加以編輯。 這是選擇性的引數，其預設值為空白 `string` 。

下列範例會示範如何設定一些選擇性引數：

```csharp
string result = await DisplayPromptAsync("Question 2", "What's 5 + 5?", initialValue: "10", maxLength: 2, keyboard: Keyboard.Numeric);
```

此程式碼會顯示預先定義的回應10、將可輸入的字元數限制為2，並顯示使用者輸入的數字鍵台：

[![螢幕擷取畫面：在 iOS 和 Android 上選擇性的強制回應提示](pop-ups-images/keyboard-prompt.png "強制回應提示")](pop-ups-images/keyboard-prompt-large.png#lightbox "強制回應提示")

> [!WARNING]
> 在 UWP 上，根據預設，當提示出現時，仍然會啟用提示之後在頁面上定義的任何存取金鑰。 如需詳細資訊，請參閱 [Windows 上的 VisualElement 存取金鑰](~/xamarin-forms/platform/windows/visualelement-access-keys.md)。

## <a name="related-links"></a>相關連結

- [PopupsSample](/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
- [從右至左的當地語系化](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
