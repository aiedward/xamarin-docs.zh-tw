---
title: 顯示快顯視窗
description: Xamarin.Forms 提供兩個類似使用者介面元素的快顯視窗：警示和動作表。 本文示範如何使用警示和動作表 API，詢問使用者簡易問題，並引導使用者完成工作。
ms.prod: xamarin
ms.assetid: 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/01/2017
ms.openlocfilehash: 36a253a46b76e4c883639ca683a8eab64700b064
ms.sourcegitcommit: 5d4e6677224971e2bc0268f405d192d0358c74b8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/21/2019
ms.locfileid: "58329308"
---
# <a name="displaying-pop-ups"></a>顯示快顯視窗

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)

_Xamarin.Forms 提供兩個類似使用者介面元素的快顯視窗：警示和動作表。本文示範如何使用警示和動作表 API，詢問使用者簡易問題，並引導使用者完成工作。_

顯示警示或要求使用者選擇是常見 UI 的工作。 Xamarin.Forms 在 [`Page`](xref:Xamarin.Forms.Page) 類別上有兩種方法，可用來透過快顯視窗與使用者互動：[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 和 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)。 兩者會以適當的原生控制項轉譯在每個平台上。

## <a name="displaying-an-alert"></a>顯示警示

所有 Xamarin.Forms 支援的平台都有強制回應快顯視窗，可警示使用者或是詢問簡易問題。 若要在 Xamarin.Forms 中顯示警示，請在任何 [`Page`](xref:Xamarin.Forms.Page) 上使用 [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法。 下列程式碼會顯示簡易訊息給使用者：

```csharp
DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "具有一個按鈕的警示對話方塊")

此範例不會向使用者收集資訊。 警示會以強制回應的方式顯示，關閉後，使用者就可繼續與應用程式互動。

[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 方法也可用來擷取使用者的回應，方法是顯示兩個按鈕，並傳回 `boolean`。 若要從警示取得回應，請為兩個按鈕提供文字並為方法加上 `await`。 當使用者選取其中一個選項後，回答就會傳回程式碼。 請注意下列範例程式碼中的 `async` 和 `await` 關鍵字：

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "具有兩個按鈕的警示對話方塊")](pop-ups-images/alert2.png#lightbox "具有兩個按鈕的警示對話方塊")

## <a name="guiding-users-through-tasks"></a>引導使用者完成工作

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) 是 iOS 中常見的 UI 元素。 Xamarin.Forms [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)方法可讓您在跨平台應用程式中包含此控制項，以在 Android 和 UWP 中轉譯原生替代項目。

若要顯示動作表，請在任何 [`Page`](xref:Xamarin.Forms.Page) 中的 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) 加上 `await`，將以字串的形式傳遞訊息和按鈕標籤。 方法會傳回使用者所按按鈕的字串標籤。 簡易範例如下所示︰

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "ActionSheet 對話方塊")

`destroy` 按鈕的轉譯方式不同於其他按鈕，可以保留 `null`，也可以指定為第三個字串參數。 下列範例使用 `destroy` 按鈕︰

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "具有終結按鈕的動作表對話方塊")](pop-ups-images/action2.png#lightbox "具有終結按鈕的動作表對話方塊")

## <a name="summary"></a>總結

本文示範如何使用警示和動作表 API，詢問使用者簡易問題，並引導使用者完成工作。 Xamarin.Forms 在 [`Page`](xref:Xamarin.Forms.Page) 類別上有兩種方法，可用來透過快顯視窗，與使用者互動：[`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) 和 [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*)，而且兩者會以適當的原生控制項轉譯在每個平台上。



## <a name="related-links"></a>相關連結

- [PopupsSample](https://developer.xamarin.com/samples/xamarin-forms/Navigation/Pop-ups/)
