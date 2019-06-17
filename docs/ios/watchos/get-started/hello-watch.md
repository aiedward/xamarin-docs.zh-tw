---
title: Hello watchOS – 逐步解說
description: 本文件提供的逐步解說建置使用 Xamarin 的簡單 watchOS 應用程式。 它說明如何在 Visual Studio 和 Visual Studio for Mac，使用分鏡腳本，和回應程式碼中的事件。
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 12/14/2016
ms.openlocfilehash: de04a2d7f42ec36c464c75ced73bf8f8029ec1da
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291007"
---
# <a name="hello-watchos--walkthrough"></a>Hello watchOS – 逐步解說

您已建立方案中的步驟之後[設定與安裝](~/ios/watchos/get-started/installation.md)，您會有 3 個專案：

- IOS 父應用程式用來設定或其他裝置上的系統管理工作。 （與 iOS 延伸模組的其他型別，這通常稱為 「 容器 」 應用程式。）使用監看式應用程式，將可實現讓使用者開始執行監看式應用程式，而不需要**曾經**執行父應用程式;
- 監看式延伸模組，其包含監看式應用程式的程式碼和
- 監看式應用程式，可保存監看式所呈現的分鏡腳本和映像資源。

請檢查您[參考是否正確](~/ios/watchos/get-started/project-references.md)： 父應用程式具有延伸模組的參考，並延伸具有監看式應用程式的參考。

確認您的套件組合識別碼，請遵循\*.watchkitextension \*.watchkitapp 慣例，而且您的延伸模組 Info.plist 檔案具有它已 **WKApp 套件組合識別碼** 值設定為套件組合識別碼您的監看式應用程式。

您應該能夠立即，執行您的監看式應用程式，但因為監看式應用程式內的分鏡腳本檔案是空白的您便無法向。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](hello-watch-images/projectstructure.png "[方案總管]")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-projectstructure.png "[方案總管]")

-----

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)
    
按兩下 Interface.storyboard Watch 應用程式啟動 Xamarin iOS 設計工具中 (如果您是在 Mac 上您可以用右鍵按一下並**開啟 > Xcode Interface Builder**)


1.  請確定**工具箱**並**屬性**填補是可見的
1.  按一下以選取介面控制器，
1.  設定識別項和標題的介面控制器**interfaceController**並**Hi 監看式**，
1.  請確認**類別**設定為**InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "識別項和介面控制器的標題設 interfaceController 和 Hi 監看式")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

按兩下 在您的監看式應用程式使用 Xamarin iOS 設計工具在 Visual Studio 中編輯 Interface.storyboard:

1.  開啟 [屬性] 窗格中;
1.  將類別變更為**InterfaceController**;
1.  按一下介面的控制器;和
1.  設定識別項和標題的介面控制器**interfaceController**並**Hi 監看式**。

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "識別項和介面控制器的標題設 interfaceController 和 Hi 監看式")

-----


建立您的 UI:

1. 從**工具箱**平台，
1. 將拖放 **按鈕** 並 **標籤** 光芒，和
1. 設定文字和控制項所示的屬性：

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![](hello-watch-images/draganddrop.png "設定文字和控制項所示的屬性")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](hello-watch-images/vs-draganddrop.png "設定文字和控制項所示的屬性")

-----

1. 設定**名稱**中**屬性**每個控制項的面板。 在此範例中，我們便`myButton`和`myLabel`。

1. 選取分鏡腳本上的按鈕，並移至**屬性**板**事件**清單，然後

1. 建立新**動作**輸入`OnButtonPress`並按下**Enter**。
  此動作會出現在清單中，與部分方法會自動建立在C#。

![](hello-watch-images/buttonaction.png "OnButtonPress 動作加入至按鈕")

儲存分鏡腳本之後, **InterfaceController.designer.cs**取得更新的控制項名稱和動作... 如果已在更新之後，您就會開啟此檔案，您可以看到如何`RegisterAttribute`對應至控制器和 UI 控制項如何對應至C#執行個體變數標示`OutletAttribute`以及動作如何對應到部分方法加上`ActionAttribute`:

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

現在，請開啟**InterfaceController.cs** (*不*InterfaceController.designer.cs)，並新增下列程式碼：

```csharp
int clickCount = 0;
partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}
```

此程式碼應該是相當透明化： 執行個體變數`clickCount`會遞增每次函式`OnButtonPress`呼叫。 文字`myLabel`變更以反映此計數;`myLabel`，當然是您在 XCode 中建立輸出的其中一個的名稱。 `partial`函式是您所指定的動作名稱相關聯的函式的實作。

如果它尚未啟動專案，

1. 您監看式延伸模組專案上按一下滑鼠右鍵，然後選擇 **設定為啟始專案**，

1. 監看式套件相容模擬器映像 （例如 iPhone 6 iOS 8.2)，將部署目標

1. 按下**偵錯**按鈕觸發組建和模擬器啟動。

    [![](hello-watch-images/readytodebug-sml.png "Visual Studio 介面項目")](hello-watch-images/readytodebug.png#lightbox)

當模擬器啟動時，按下按鈕，增加的標籤。
恭喜，您可盡情自行 Watch 應用程式 ！

![](hello-watch-images/running.png "在模擬器中執行的應用程式")


## <a name="related-links"></a>相關連結

- [開始使用 （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/GettingStarted/)
- [設定與安裝](~/ios/watchos/get-started/installation.md)
- [Watch 應用程式上的影片](https://blog.xamarin.com/your-first-watch-kit-app/)
