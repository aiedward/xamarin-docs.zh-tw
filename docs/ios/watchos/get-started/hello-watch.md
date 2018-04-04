---
title: Hello，監看式
description: 開始使用 Xamarin 和 watchOS
ms.prod: xamarin
ms.assetid: AD1DA488-51AB-420A-A0B7-3AE69A964A40
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 12/14/2016
ms.openlocfilehash: 2281fa801d32e8d8934767ae090503ca523d7eff
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="hello-watch"></a>Hello，監看式

在您建立的方案中的步驟之後[設定及安裝](~/ios/watchos/get-started/installation.md)，您會有 3 的專案：

- IOS 父應用程式是用來安裝程式或其他裝置上的系統管理工作。 （iOS 擴充功能的其他類型，這通常稱為"Container"應用程式。）透過監看式應用程式，將會開始執行而不監看式應用程式的使用者可能在**曾經**執行父應用程式。
- 監看式延伸，其中包含監看式應用程式的程式碼和
- 監看式應用程式，保留 監看式所呈現的分鏡腳本與影像資源。

請檢查您[參考是否正確](~/ios/watchos/get-started/project-references.md)： 父應用程式已擴充的參考和擴充功能都有監看式應用程式的參考。

確認套件組合識別碼遵循\*.watchkitextension \*.watchkitapp 慣例和擴充功能的 Info.plist 檔案具有它有**WKApp 套件組合識別碼**值設定為配套識別碼您監看式應用程式。

您應該能夠執行監看式應用程式，現在，但因為監看式應用程式內的分鏡腳本檔案是空白的您就無法分辨。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](hello-watch-images/projectstructure.png "[方案總管]")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-watch-images/vs-projectstructure.png "[方案總管]")

-----

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
    
按兩下 監看式應用程式以啟動 Xamarin iOS 設計工具中 Interface.storyboard (如果您是在 Mac 上您可以也以滑鼠右鍵按一下和**開啟 > Xcode 介面產生器**)


1.  請確定**工具箱**和**屬性**填補是可見的
1.  按一下以選取介面控制站
1.  設定識別項和標題介面控制站到**interfaceController**和**Hi 監看式**，
1.  確認**類別**設**InterfaceController**

    ![](hello-watch-images/interfacecontrollerattributes.png "識別項和介面控制站的標題設 interfaceController 和 Hi 監看式")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

在監看式應用程式中使用 Xamarin iOS 設計工具，Visual Studio 中編輯 Interface.storyboard 上，按兩下：

1.  開啟 [屬性] 窗格中。
1.  若要將類別變更**InterfaceController**;
1.  按一下介面的控制器。和
1.  設定識別項和標題介面控制站到**interfaceController**和**Hi 監看式**。

    ![](hello-watch-images/vs-interfacecontrollerattributes.png "識別項和介面控制站的標題設 interfaceController 和 Hi 監看式")

-----


建立您的 UI:

1. 從**工具箱**板，
1. 將拖放**按鈕**和**標籤**至場景，以及
1. 設定文字和控制項所示的屬性：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![](hello-watch-images/draganddrop.png "設定文字和控制項所示的屬性")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](hello-watch-images/vs-draganddrop.png "設定文字和控制項所示的屬性")

-----

1. 設定**名稱**中**屬性**每個控制項的填補。 在此範例中我們使用`myButton`和`myLabel`。

1. 選取的分鏡腳本上的按鈕，並移至**屬性**填補的**事件**清單，然後

1. 建立新**動作**輸入`OnButtonPress`按**Enter**。
  此動作會出現在清單中，和 C# 中，自動建立的部分方法。

![](hello-watch-images/buttonaction.png "OnButtonPress 動作加入至按鈕")

儲存分鏡腳本之後, **InterfaceController.designer.cs**取得更新的控制項名稱和動作... 如果您開啟這個檔案之後已更新,，您可以查看如何`RegisterAttribute`會對應至控制器和 UI 控制項如何對應至標示的 C# 執行個體變數`OutletAttribute`和動作如何對應至部分方法標記為`ActionAttribute`:

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

現在開啟**InterfaceController.cs** (*不*InterfaceController.designer.cs) 並加入下列程式碼：

```csharp
int clickCount = 0;

partial void OnButtonPress (WatchKit.WKInterfaceButton sender)
{
  var msg = String.Format("Clicked {0} times", ++clickCount);
  myLabel.SetText(msg);
}

```

此程式碼應該是相當透明： 執行個體變數`clickCount`是每次遞增函式`OnButtonPress`呼叫。 文字`myLabel`變更以反映這個計數;`myLabel`，當然是其中一個插座，您在 XCode 中建立的名稱。 `partial`函式是您所指定的動作名稱相關聯函式的實作。

如果還不是啟始專案，

1. 您監看式延伸模組專案上按一下滑鼠右鍵，然後選擇 **設定為啟始專案**，

1. 監看式套件相容模擬器映像 （例如 iPhone 6 iOS 8.2)，將部署目標

1. 按**偵錯**按鈕，以觸發組建] 和 [模擬器啟動。

    [![](hello-watch-images/readytodebug-sml.png "Visual Studio 介面項目")](hello-watch-images/readytodebug.png#lightbox)

模擬器啟動時，請按下按鈕，增加的標籤。
恭喜您，你自行監看式應用程式 ！

![](hello-watch-images/running.png "在模擬器中執行應用程式")


## <a name="related-links"></a>相關連結

- [使用者入門 （範例）](https://developer.xamarin.com/samples/monotouch/WatchKit/GettingStarted/)
- [設定與安裝](~/ios/watchos/get-started/installation.md)
- [第一個監看式應用程式的視訊](http://blog.xamarin.com/your-first-watch-kit-app/)
