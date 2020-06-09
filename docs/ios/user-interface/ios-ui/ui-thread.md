---
title: 在 Xamarin 中使用 UI 執行緒
description: 本檔說明如何在 Xamarin 中使用 UI 執行緒。 它討論 UI 執行緒執行、提供背景執行緒範例，以及檢查 async/await。
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 584b398deafd233fdbe6b24189a2047ae712fdcf
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573517"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>在 Xamarin 中使用 UI 執行緒

應用程式使用者介面一律是單一執行緒，即使在多執行緒的裝置中，畫面只有一個標記法，而所顯示內容的任何變更都必須透過單一「存取點」進行協調。 這可防止多個執行緒同時嘗試更新相同的圖元（例如）。

您的程式碼應該只會對主要（或 UI）執行緒變更使用者介面控制項。 在不同的執行緒上發生的任何 UI 更新（例如回呼或背景執行緒）可能不會轉譯至螢幕，或甚至會造成當機。

## <a name="ui-thread-execution"></a>UI 執行緒執行

當您在視圖中建立控制項，或處理使用者起始的事件（例如觸控）時，程式碼已經在 UI 執行緒的內容中執行。

如果程式碼是在背景執行緒上執行，則在工作或回呼中，它可能不會在主要 UI 執行緒上執行。 在此情況下，您應該將程式碼包裝在的呼叫中， `InvokeOnMainThread` 或 `BeginInvokeOnMainThread` 如下所示：

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

`InvokeOnMainThread`方法是在上定義， `NSObject` 因此可以從任何 UIKit 物件（例如 View 或 view Controller）上定義的方法內呼叫。

在進行 Xamarin iOS 應用程式的調試時，如果您的程式碼嘗試從錯誤的執行緒存取 UI 控制項，則會擲回錯誤。 這可協助您使用 InvokeOnMainThread 方法來追蹤並修正這些問題。 這只會在進行偵錯工具時發生，而且不會在發行組建中擲回錯誤。 錯誤訊息會如下所示：

 ![](ui-thread-images/image10.png "UI Thread Execution")

 <a name="Background_Thread_Example"></a>

## <a name="background-thread-example"></a>背景執行緒範例

以下範例會嘗試 `UILabel` 使用簡單的執行緒，從背景執行緒存取使用者介面控制項（a）：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

該程式碼會在進行 `UIKitThreadAccessException` 調試時擲回。 若要修正此問題（並確保只能從主要 UI 執行緒存取使用者介面控制項），請將參考 UI 控制項的任何程式碼包裝在運算式內， `InvokeOnMainThread` 如下所示：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

本檔中的其餘範例不需要使用此資訊，但在您的應用程式提出網路要求時，請記住這項重要概念，使用通知中心或其他需要在另一個執行緒上執行之完成處理常式的方法。

 <a name="Async_Await_Example"></a>

## <a name="asyncawait-example"></a>Async/Await 範例

不需要使用 c # 5 async/await 關鍵字，因為當等候的工作 `InvokeOnMainThread` 完成時，方法會繼續在呼叫執行緒上執行。

這個範例程式碼（僅在 Delay 方法呼叫上，純粹是為了示範用途）會顯示在 UI 執行緒上呼叫的非同步方法（這是 TouchUpInside 處理常式）。 因為包含方法是在 UI 執行緒上呼叫，所以在 `UILabel` `UIAlertView` 背景執行緒上完成非同步作業之後，可以安全地呼叫 ui 作業，例如在上設定文字或顯示。

```csharp
async partial void button2_TouchUpInside (UIButton sender)
{
    textfield1.ResignFirstResponder ();
    textfield2.ResignFirstResponder ();
    textview1.ResignFirstResponder ();
    label1.Text = "async method started";
    await Task.Delay(1000); // example purpose only
    label1.Text = "1 second passed";
    await Task.Delay(2000);
    label1.Text = "2 more seconds passed";
    await Task.Delay(1000);
    new UIAlertView("Async method complete", "This method", 
               null, "Cancel", null)
        .Show();
    label1.Text = "async method completed";
}
```

如果從背景執行緒（而非主要 UI 執行緒）呼叫非同步方法，則 `InvokeOnMainThread` 仍然需要。

## <a name="related-links"></a>相關連結

- [控制項（範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
- [執行緒](~/ios/app-fundamentals/threading.md)
