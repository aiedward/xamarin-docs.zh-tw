---
title: Hello，watchOS –逐步解說
description: 本檔提供使用 Xamarin 建立簡單 watchOS 應用程式的逐步解說。 它描述如何在 Visual Studio 和 Visual Studio for Mac 工作、使用分鏡腳本，以及在程式碼中回應事件。
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/14/2016
ms.openlocfilehash: 89f3de944004404cdc1f4d192426a50f6746fe73
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928603"
---
# <a name="hello-watchos--walkthrough"></a>Hello，watchOS –逐步解說

依照 [設定和安裝](~/ios/watchos/get-started/installation.md)中的步驟建立解決方案之後，您將會有3個專案：

- IOS 父應用程式，用於安裝程式或其他裝置上的系統管理工作。  (與其他類型的 iOS 延伸模組，這通常稱為「容器」應用程式。 ) 使用 Watch 應用程式，使用者就可以開始執行 Watch 應用程式 **，而不** 需要執行父應用程式;
- 監看式延伸模組，其中包含 watch 應用程式的程式碼;和
- Watch 應用程式，其保存在監看式上轉譯的腳本和影像資源。

請檢查您的 [參考是否正確](~/ios/watchos/get-started/project-references.md)：父應用程式是否有 Watch 應用程式的參考，以及 Watch 應用程式是否有延伸模組的參考。

確認您的套件組合識別碼遵循 \* watchkitextension \* . watchkitapp 慣例，且您的延伸模組資訊 plist 檔案將其 **WKApp** 套件組合識別碼值設定為 Watch 應用程式的套件組合識別碼。

您現在應該能夠執行您的 Watch 應用程式，但因為 Watch 應用程式內的分鏡腳本檔案是空白的，所以您將無法分辨。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![方案總管](hello-watch-images/projectstructure.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![方案總管](hello-watch-images/vs-projectstructure.png)

-----

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

在您的 Watch 應用程式中按兩下介面，以啟動 Xamarin iOS 設計工具 (如果您是在 Mac 上，您也可以用滑鼠右鍵按一下並 **開啟 > Xcode Interface Builder** ) 

1. 確定 [ **工具箱** ] 和 [ **屬性** ] 板都可見，
1. 按一下以選取介面控制器，
1. 將介面控制器的識別碼和標題設定為 **interfaceController** 和 **嗨觀賞**
1. 確認 **類別** 設定為  **InterfaceController**

    ![將介面控制器的識別碼和標題設定為 interfaceController 和嗨觀賞](hello-watch-images/interfacecontrollerattributes.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

按兩下 [監看式] 應用程式中的介面，以 Visual Studio 中的 Xamarin iOS 設計工具進行編輯：

1. 開啟 [屬性] 窗格;
1. 將類別變更為 **InterfaceController** ;
1. 按一下介面控制器;和
1. 將介面控制器的識別碼和標題設定為 **interfaceController** 和 **嗨監看** 。

    ![將介面控制器的識別碼和標題設定為 interfaceController 和嗨觀賞](hello-watch-images/vs-interfacecontrollerattributes.png)

-----

建立您的 UI：

1. 從 [ **工具箱** ] 面板
1. 將 **按鈕** 和 **標籤** 拖放到場景上，
1. 設定控制項的文字和屬性，如下所示：

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

![設定控制項的文字和屬性，如下所示](hello-watch-images/draganddrop.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![設定控制項的文字和屬性，如下所示](hello-watch-images/vs-draganddrop.png)

-----

1. 在 [ **屬性** ] 面板中設定每個控制項的 **名稱** 。 在此範例中，我們使用了 `myButton` 和 `myLabel` 。

1. 選取腳本上的按鈕，並移至 **Properties** Pad 的 **事件** 清單，然後

1. 輸入 **Action** `OnButtonPress` 並按 **enter** ，以建立新的動作。
  動作將會出現在清單中，而部分方法會在 c # 中自動建立。

![新增至按鈕的 OnButtonPress 動作](hello-watch-images/buttonaction.png)

儲存腳本之後，會以控制項名稱和動作更新 **InterfaceController.designer.cs** 。 如果您在更新之後開啟此檔案，您可以看到對應至控制器的方式， `RegisterAttribute` 以及 UI 控制項如何對應至標示為的 c # 執行個體變數， `OutletAttribute` 以及如何將動作對應至標記為的部分方法 `ActionAttribute` ：

```csharp
// WARNING
//
// This file has been generated automatically by Visual Studio for Mac from the outlets and
// actions declared in your storyboard file.
// Manual changes to this file will not be maintained.
//
[Register ("InterfaceController")]
partial class InterfaceController
{
    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceButton myButton { get; set; }

    [Outlet]
    [GeneratedCode ("iOS Designer", "1.0")]
    WatchKit.WKInterfaceLabel myLabel { get; set; }

    [Action ("OnButtonPress:")]
    [GeneratedCode ("iOS Designer", "1.0")]
    partial void OnButtonPress (WatchKit.WKInterfaceButton sender);

    void ReleaseDesignerOutlets ()
    {
        if (myButton != null) {
            myButton.Dispose ();
            myButton = null;
        }
        if (myLabel != null) {
            myLabel.Dispose ();
            myLabel = null;
        }
    }
}
```

現在開啟 **InterfaceController.cs** ( *不會* InterfaceController.designer.cs) 並新增下列程式碼：

```csharp
int clickCount = 0;
partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}
```

這段程式碼應該相當透明：每次呼叫函式時，執行個體變數 `clickCount` 都會遞增 `OnButtonPress` 。 的文字 `myLabel` 會變更以反映此計數; `myLabel` 當然，這是您在 XCode 中建立的其中一個輸出的名稱。 `partial`函數是與您指定之動作名稱相關聯的函式執行。

如果它還不是啟始專案，

1. 以滑鼠右鍵按一下您的 Watch 延伸模組專案，然後選擇 [ **設定為啟始專案** ]。

1. 將部署目標設定為 Watch 套件相容的模擬器映射 (例如 iPhone 6 iOS 8.2) 、

1. 按下 [ **調試** 程式] 按鈕，以觸發組建和模擬器啟動。

    [![Visual Studio 介面元素](hello-watch-images/readytodebug-sml.png)](hello-watch-images/readytodebug.png#lightbox)

當模擬器啟動時，按下按鈕可遞增標籤。
恭喜您，您已擁有 Watch 應用程式！

![在模擬器中執行的應用程式](hello-watch-images/running.png)

## <a name="related-links"></a>相關連結

- [設定和安裝](~/ios/watchos/get-started/installation.md)
- [第一次觀看應用程式影片](https://blog.xamarin.com/your-first-watch-kit-app/)
