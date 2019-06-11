---
title: 使用 Xamarin.iOS 中的 UI 執行緒
description: 本文件說明如何使用在 Xamarin.iOS 中的 UI 執行緒。 它討論 UI 執行緒執行，提供一個背景執行緒的範例，並檢查 async/await。
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: e4485c485b708bdec06f7f1dc22f0bf33e07e982
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827753"
---
# <a name="working-with-the-ui-thread-in-xamarinios"></a>使用 Xamarin.iOS 中的 UI 執行緒

應用程式使用者介面一律是單一執行緒，即使是在多執行緒的裝置 – 只能有一個表示法的螢幕，並顯示的內容的任何變更需要透過單一的 「 存取點 」 的協調。 這可防止多個執行緒嘗試同時更新相同的像素 （例如）。

您的程式碼只應該要對使用者介面控制項，從主要 （或 UI） 執行緒。 （例如回呼或背景執行緒） 的不同執行緒發生的任何 UI 更新可能不取得轉譯至螢幕，或甚至可能會造成當機。

## <a name="ui-thread-execution"></a>UI 執行緒執行

當您在檢視中，建立控制項或處理使用者啟動的事件，例如觸控時，程式碼已經在 UI 執行緒內容中執行。

如果在工作或回呼的背景執行緒上執行程式碼則可能不在主要 UI 執行緒上執行。 在此情況下您應該將程式碼包裝在呼叫`InvokeOnMainThread`或`BeginInvokeOnMainThread`如下所示：

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

`InvokeOnMainThread`方法定義上`NSObject`讓它可以從方法內呼叫 （例如，檢視或檢視控制器） 任何 UIKit 物件上定義。

偵錯時的 Xamarin.iOS 應用程式，發生錯誤則會擲回您的程式碼會嘗試從錯誤的執行緒存取 UI 控制項。 這可協助您追蹤並修正這些問題 InvokeOnMainThread 方法。 這只在偵錯時，就會發生，並不會擲回錯誤發行組建中。 錯誤訊息將會如下所示：

 ![](ui-thread-images/image10.png "UI 執行緒執行")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>背景執行緒範例

以下是嘗試存取使用者介面控制項的範例 ( `UILabel`) 從背景執行緒，使用簡單的執行緒：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

程式碼將會擲回`UIKitThreadAccessException`偵錯時。 若要修正此問題 （並確保在使用者介面控制項只存取從主要 UI 執行緒），來包裝參考 UI 控制項內的任何程式碼`InvokeOnMainThread`運算式如下：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

您不需要將此選項用於本文件中，範例的其餘部分，但它是一個重要的概念，請記住您的應用程式發出網路要求時使用通知中心或其他需要另一個將執行完成處理常式的方法執行緒。

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>Async/Await 範例

當使用C#5 async/await 關鍵字`InvokeOnMainThread`由於不需要等候的工作完成時呼叫的執行緒上的方法會繼續。

這個範例程式碼 （其會等候的延遲方法呼叫，純粹供示範之用） 將示範非同步方法呼叫 （它是 TouchUpInside 處理常式） 在 UI 執行緒上。 包含的方法在 UI 執行緒上呼叫，因為 UI 之類的作業上設定的文字`UILabel`或顯示`UIAlertView`可以安全地呼叫背景執行緒上非同步作業完成之後。

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

如果非同步方法呼叫從背景執行緒 （不主要 UI 執行緒） 再`InvokeOnMainThread`仍將是必要。


## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/monotouch/Controls/)
- [執行緒處理](~/ios/app-fundamentals/threading.md)
