---
title: IOS 6 簡介
description: iOS 6 包含了許多新技術來開發應用程式中，為 C# 開發人員的 Xamarin.iOS 6。
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 8f3be80ffb8156c24c96b03fda8eac3907ca88bd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-ios-6"></a>IOS 6 簡介

_iOS 6 包含了許多新技術來開發應用程式中，為 C# 開發人員的 Xamarin.iOS 6。_

[ ![](images/ios6-large.jpg "IOS 6 標誌")](images/ios6-large.jpg#lightbox)

IOS 6 和 Xamarin.iOS 6，開發人員現在有豐富的功能來建立 iOS 應用程式，包括該目標 iPhone 5 處置。
本文列出一些更有趣的新功能可與每個主題的文章連結。 此外它牽涉到重要，因為開發人員將移至 iOS 6 和新的解析度 iPhone 5 的一些變更。


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[集合檢視的簡介](~/ios/user-interface/controls/uicollectionview.md)

集合檢視允許使用任意的版面配置來顯示的內容。 它們允許更輕鬆地建立類似格線版面配置，根據預設，同時支援自訂的版面配置。 如需詳細資訊，請參閱、[集合檢視簡介](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md)指南。


## <a name="introduction-to-pass-kitiosplatformpasskitmd"></a>[將組件的簡介](~/ios/platform/passkit.md)

傳遞的套件架構允許 Passbook 應用程式中受管理的數位傳遞與互動的應用程式。 如需詳細資訊，請參閱、[傳遞的組件指南簡介](~/ios/platform/passkit.md)。


##  <a name="introduction-to-event-kitiosplatformeventkitmd"></a>[事件套件簡介](~/ios/platform/eventkit.md)

EventKit 架構提供存取行事曆、 行事曆事件和行事曆資料庫儲存備忘提醒資料的方法。 存取行事曆與行事曆事件已經提供自 iOS 4，但 iOS 6 現在會公開提醒資料的存取權。 如需詳細資訊，請參閱[我](~/ios/platform/eventkit.md) [EventKit 的 ntroduction](~/ios/platform/eventkit.md)指南。


##  <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[社交 Framework 簡介](~/ios/platform/social-framework.md)

社交 Framework 會提供統一的 API，來與社交網路包括 Twitter 與 Facebook、 以及 SinaWeibo 中國中的使用者互動。 如需詳細資訊，請參閱、[社交 Framework 簡介](~/ios/platform/social-framework.md)指南。


##  <a name="changes-to-store-kitchanges-to-storekitmd"></a>[若要儲存套件的變更](changes-to-storekit.md)

Apple 已引進了兩個存放區套件中的新功能： 購買和下載 iTunes 或將內容從 App Store 應用程式中的裝載的應用程式內購買的內容檔案 ！。 如需詳細資訊，請參閱、[變更為市集套件](changes-to-storekit.md)指南。


## <a name="other-changes"></a>其他變更


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload 和 ViewDidUnload 已被取代

`ViewWillUnload`和`ViewDidUnload`方法`UIViewController`不再稱為 iOS 6。 在舊版 iOS，這些方法可能有已使用應用程式分別儲存狀態，才可檢視卸載，並清除程式碼。

例如，適用於 Mac 的 Visual Studio 會建立呼叫的方法`ReleaseDesignerOutlets`，如下所示，它會再從呼叫`ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

不過，在 iOS 6，就不再需要呼叫`ReleaseDesignerOutlets`。   
   
   
   
IOS 6 應用程式應使用的清除程式碼， `DidReceiveMemoryWarning`。 不過，程式碼呼叫`Dispose`應該謹慎而且只適用於記憶體密集物件所示下方：

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

同樣地，呼叫`Dispose`依照上述應該很少會需要。 在大部分的應用程式應該進行的一般是要移除事件處理常式。

對於儲存狀態的情況，應用程式可以執行在`ViewWillDisappear`和`ViewDidDisappear`而不是`ViewWillUnload`。


### <a name="iphone-5-resolution"></a>iPhone 5 解析度

iPhone 5 裝置都具備 640 x 1136 解析度。 IOS 的先前版本為目標的應用程式會出現 letterboxed iPhone 5 上執行時如下所示：

 [![](images/01-letterboxed.png "IOS 的先前版本為目標的應用程式會出現 letterboxed iPhone 5 上執行時")](images/01-letterboxed.png#lightbox)

為了讓應用程式會顯示全螢幕上 iphone 5 以上，只要加入名為映像`Default-568h@2x.png`有 640 x 1136 的解析度。 下列螢幕擷取畫面顯示已包含此映像後，執行的應用程式：

 [![](images/02-fullscreen.png "這個螢幕擷取畫面顯示已包含此映像後，執行的應用程式")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>子類別化 UINavigationBar

在 iOS 6`UINavigationBar`可以子類別化。 這可讓其他控制項的外觀及操作的`UINavigationBar`。 例如，應用程式都可以子類別新增 subviews、 以動畫顯示這些檢視和修改的界限`UINavigationBar`。

下列程式碼顯示範例子類別化`UINavigationBar`加入`UIImageView`:

```csharp
public class CustomNavBar : UINavigationBar
{
    UIImageView iv;
    public CustomNavBar (IntPtr h) : base(h)
    {
        iv = new UIImageView (UIImage.FromFile ("monkey.png"));
        iv.Frame = new CGRect (75, 0, 30, 39);
    }
    public override void Draw (RectangleF rect)
    {
        base.Draw (rect);
        TintColor = UIColor.Purple;
        AddSubview (iv);
    }
}
```

若要加入子類別化`UINavigationBar`至`UINavigationController`，使用`UINavigationController`建構函式的型別之`UINavigationBar`和`UIToolbar`，如下所示：

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

使用此`UINavigationBar`子類別會導致影像檢視，顯示如下列螢幕擷取畫面所示：

 [![](images/03-navbar.png "在這個螢幕擷取畫面所示顯示映像檢視中使用這個 UINavigationBar 子類別化結果")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>介面方向

之前 iOS 6 的應用程式無法覆寫`ShouldAutorotateToInterfaceOrientation`，支援特定控制器的任何方向傳回 true。 例如，下列程式碼會用來支援只有直式：

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

在 iOS 6`ShouldAutorotateToInterfaceOrientation`已被取代。
應用程式可以改為覆寫`GetSupportedInterfaceOrientations`在根檢視控制站如下所示：

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

在 iPad 上就會預設為所有四個方向如果`GetSupportedInterfaceOrientation`未實作。 在 iPhone 或 iPod Touch，預設值是所有方向除了`PortraitUpsideDown`。
