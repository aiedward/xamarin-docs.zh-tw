---
title: 顯示快顯視窗
description: Xamarin.Forms 提供兩個快顯註冊類似使用者介面項目 – 警示和動作的工作表。 這篇文章示範如何使用警示和動作表 Api，詢問使用者簡單的問題，並引導使用者完成工作。
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 156c2f9dca47a7755d4f810d7921a05662388ded
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996710"
---
# <a name="displaying-pop-ups"></a>顯示快顯視窗

_Xamarin.Forms 提供兩個快顯註冊類似使用者介面項目 – 警示和動作的工作表。這篇文章示範如何使用警示和動作表 Api，詢問使用者簡單的問題，並引導使用者完成工作。_

顯示警示，或要求使用者做出的選擇是常見 UI 的工作。 Xamarin.Forms 有兩種方法[ `Page` ](xref:Xamarin.Forms.Page)與快顯視窗，透過使用者互動的類別： [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*)並[ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*)。 它們會與每個平台上的適當原生控制項呈現。

## <a name="displaying-an-alert"></a>顯示警示

所有的 Xamarin.Forms 支援平台會有以警示使用者，或詢問簡單的問題，其中的強制回應快顯視窗。 若要顯示在 Xamarin.Forms 中的這些警示，請使用[ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*)方法在任何[ `Page` ](xref:Xamarin.Forms.Page)。 下列程式碼會顯示簡單訊息給使用者：

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "具有一個按鈕的警示對話方塊")

此範例不會從使用者收集資訊。 會以強制回應方式顯示的警示和關閉使用者之後會繼續與應用程式互動。

[ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*)方法也可用來擷取使用者的回應呈現兩個按鈕，並傳回`boolean`。 若要從警示取得回應，提供這兩個按鈕的文字和`await`方法。 在使用者選取其中一個選項之後的回應會傳回您的程式碼。 附註`async`和`await`在下列範例程式碼中的關鍵字：

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  var answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "警示有兩個按鈕的對話方塊")](pop-ups-images/alert2.png#lightbox "警示有兩個按鈕的對話方塊")

## <a name="guiding-users-through-tasks"></a>引導使用者完成工作

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html)是在 iOS 中的常見 UI 項目。 Xamarin.Forms [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*)方法可讓您跨平台的應用程式，呈現在 Android 和 UWP 的原生替代項目加入此控制項。

若要顯示的動作工作表中， `await` [ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*)任何[ `Page` ](xref:Xamarin.Forms.Page)、 將訊息傳遞和按鈕標籤為字串。 方法會傳回使用者所按按鈕的字串標籤。 簡單的範例如下所示：

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "ActionSheet 對話方塊")

`destroy`  按鈕的方式不同於其他轉譯，可以保留`null`或指定為第三個字串參數。 下列範例會使用`destroy`按鈕：

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  var action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Destroy 按鈕動作的工作表對話方塊")](pop-ups-images/action2.png#lightbox "終結按鈕動作的工作表對話方塊")

## <a name="summary"></a>總結

這篇文章示範如何使用警示和動作表 Api，詢問使用者簡單的問題，並引導使用者完成工作。 Xamarin.Forms 有兩種方法[ `Page` ](xref:Xamarin.Forms.Page)與快顯視窗，透過使用者互動的類別： [ `DisplayAlert` ](xref:Xamarin.Forms.Page.DisplayAlert*)並[ `DisplayActionSheet` ](xref:Xamarin.Forms.Page.DisplayActionSheet*)，而且是同時使用每個平台上的適當原生控制項呈現。



## <a name="related-links"></a>相關連結

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
