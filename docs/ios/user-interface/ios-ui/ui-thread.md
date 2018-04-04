---
title: 使用 UI 執行緒
ms.prod: xamarin
ms.assetid: 98762ACA-AD5A-4E1E-A536-7AF3BE36D77E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 72f161001509519fb02a652f23eaa7805a55f7ca
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="working-with-the-ui-thread"></a>使用 UI 執行緒

應用程式使用者介面一律是單一執行緒，即使是在多執行緒的裝置 – 螢幕只有一個表示法，並顯示內容的任何變更需要在透過 「 存取點 」 的單一協調。 這可避免多個執行緒嘗試同時更新相同的像素 （例如）。

您的程式碼應該只會提供對使用者介面控制項，從主要 （或 UI） 執行緒。 在不同的執行緒 （例如回呼或背景的執行緒） 進行任何 UI 更新可能就無法取得轉譯至螢幕，或甚至會損毀。

## <a name="ui-thread-execution"></a>UI 執行緒執行

當您建立的控制項檢視中，或處理使用者啟動的事件，例如觸控時，已經 UI 執行緒的內容中執行程式碼。

如果在背景執行緒，工作或回呼中執行程式碼則可能不在主要 UI 執行緒上執行。 您在此情況下設定呼叫的程式碼包裝`InvokeOnMainThread`或`BeginInvokeOnMainThread`如下所示：

```csharp
InvokeOnMainThread ( () => {
    // manipulate UI controls
});
```

`InvokeOnMainThread`方法上定義`NSObject`讓它可以從內呼叫方法 （例如，檢視或檢視控制器） 的任何 UIKit 物件上定義。

偵錯時 Xamarin.iOS 應用程式，錯誤會擲回您的程式碼嘗試從錯誤的執行緒存取 UI 控制項。 這可協助您追蹤並修正這些問題 InvokeOnMainThread 方法。 這只偵錯時，就會發生，並在發行組建中沒有擲回錯誤。 將會出現錯誤訊息如下：

 ![](ui-thread-images/image10.png "UI 執行緒執行")

 <a name="Background_Thread_Example" />


## <a name="background-thread-example"></a>背景執行緒範例

以下是嘗試存取使用者介面控制項的範例 ( `UILabel`) 從背景執行緒使用簡單的執行緒：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    label1.Text = "updated in thread"; // should NOT reference UILabel on background thread!
})).Start();
```

程式碼將會擲回`UIKitThreadAccessException`偵錯時。 若要修正此問題 （並確保使用者介面控制項只從主要 UI 執行緒存取），包裝參考 UI 控制項內的任何程式碼`InvokeOnMainThread`運算式如下：

```csharp
new System.Threading.Thread(new System.Threading.ThreadStart(() => {
    InvokeOnMainThread (() => {
        label1.Text = "updated in thread"; // this works!
    });
})).Start();
```

您不需要執行這在本文件中，但是它範例的其餘部分是您的應用程式可讓網路要求時要注意的重要概念的使用會使用通知中心或其他方法的要求會在另一台執行完成處理常式執行緒。

 <a name="Async_Await_Example" />


## <a name="asyncawait-example"></a>非同步/等候範例

使用 C# 5 非同步/等候關鍵字時`InvokeOnMainThread`由於不需要等候的工作完成時呼叫的執行緒上的此方法會繼續。

這個範例程式碼 （其會等候的延遲方法呼叫，純粹供示範之用） 顯示 （它是 TouchUpInside 處理常式） 的 UI 執行緒呼叫的非同步方法。 UI 執行緒上呼叫包含的方法，因為 UI 之類的作業上設定的文字`UILabel`或顯示`UIAlertView`可以安全地呼叫在背景執行緒上非同步作業完成之後。

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

如果非同步方法呼叫從背景執行緒 （不主要 UI 執行緒） 然後`InvokeOnMainThread`仍然是必要。


## <a name="related-links"></a>相關連結

- [控制項 （範例）](https://developer.xamarin.com/samples/Controls/)
- [執行緒處理](~/ios/app-fundamentals/threading.md)
