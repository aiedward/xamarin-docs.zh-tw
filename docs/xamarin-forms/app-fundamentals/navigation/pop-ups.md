---
title: "顯示快顯視窗"
description: "Xamarin.Forms 提供兩個 pop up 類似使用者介面項目 – 警示和動作工作表。 本文示範如何使用警示和動作表應用程式開發介面，詢問使用者簡單的問題，以及引導使用者進行的工作。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 957db750b852b40daf1556e8dc8f7ba18e022dba
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2018
---
# <a name="displaying-pop-ups"></a>顯示快顯視窗

_Xamarin.Forms 提供兩個 pop up 類似使用者介面項目 – 警示和動作工作表。本文示範如何使用警示和動作表應用程式開發介面，詢問使用者簡單的問題，以及引導使用者進行的工作。_

顯示警示，或要求使用者做出選擇，是常見的 UI 工作。 Xamarin.Forms 有兩種方法[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)與快顯視窗透過使用者互動的類別： [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/)和[ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/)。 它們會轉譯與每個平台上的適當原生控制項。

## <a name="displaying-an-alert"></a>顯示警示

所有 Xamarin.Forms 支援的平台都有強制回應的快顯視窗，警告使用者，或詢問簡單的問題。 若要顯示這些警示，在 Xamarin.Forms 中，使用[ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/)方法上任何[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)。 下列程式碼顯示簡單訊息給使用者：

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "具有一個按鈕的警示 對話方塊")

此範例不會從使用者收集資訊。 警示會顯示為強制回應和關閉使用者之後會繼續與應用程式互動。

[ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/)方法也可用來擷取使用者的回應，藉由呈現兩個按鈕，並傳回`boolean`。 若要從警示取得回應，提供兩個按鈕的文字和`await`方法。 在使用者選取其中一個選項之後的回應會傳回您的程式碼。 請注意`async`和`await`在下列範例程式碼中的關鍵字：

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[ ![DisplayAlert](pop-ups-images/alert2-sml.png "警示有兩個按鈕的對話方塊")](pop-ups-images/alert2.png "警示有兩個按鈕的對話方塊")

## <a name="guiding-users-through-tasks"></a>工作的逐步指導使用者

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html)是常見的 UI 項目，在 iOS 中。 Xamarin.Forms [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/)方法可讓您在跨平台應用程式中，呈現在 Android 和 Windows Phone 的原生替代項目中包含此控制項。

若要顯示動作的工作表， `await` [ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/)任何[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)、 將訊息傳遞和按鈕標籤字串設定為。 方法會傳回已由使用者所按下按鈕的字串標籤。 簡單的範例如下所示：

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "ActionSheet 對話方塊")

`destroy`按鈕呈現方式不同於其他項目，並可以保留`null`或指定為第三個字串參數。 下列範例會使用`destroy`按鈕：

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[ ![DisplayActionSheet](pop-ups-images/action2-sml.png "終結按鈕動作的工作表對話方塊")](pop-ups-images/action2.png "終結按鈕動作的工作表對話方塊")

## <a name="summary"></a>總結

本文示範使用警示和動作表應用程式開發介面，詢問使用者簡單的問題，以及引導使用者進行的工作。 Xamarin.Forms 有兩種方法[ `Page` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Page/)與快顯視窗透過使用者互動的類別： [ `DisplayAlert` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)/)和[ `DisplayActionSheet` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Page.DisplayActionSheet(System.String,System.String,System.String,System.String[])/)，而且它們可同時使用每個平台上的適當原生控制項呈現。



## <a name="related-links"></a>相關連結

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
