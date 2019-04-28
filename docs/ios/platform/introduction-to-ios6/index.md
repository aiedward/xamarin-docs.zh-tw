---
title: iOS 6 簡介
description: 本文件所連結說明 iOS 6 中引進的功能的指南。 集合檢視，PassKit 社交架構中，所有討論 StoreKit 的變更。
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 25926d82e060b91b007da9c2295b328cb049e8df
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61302234"
---
# <a name="introduction-to-ios-6"></a>iOS 6 簡介

_iOS 6 包含新的技術，用於開發應用程式，Xamarin.iOS 6 帶入各種C#開發人員。_

[ ![](images/ios6-large.jpg "IOS 6 標誌")](images/ios6-large.jpg#lightbox)

IOS 6 和 Xamarin.iOS 6，開發人員現在會自行建立 iOS 應用程式，包括該目標 iphone 5 以上有豐富的功能。
本文件列出一些更令人興奮的新功能，可和每個主題的文章連結。 此外它會牽涉到幾個變更將會重要，因為開發人員將移至 iOS 6 和 iPhone 5 的新的解析度。


## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[集合檢視簡介](~/ios/user-interface/controls/uicollectionview.md)

集合檢視允許使用任意的版面配置來顯示的內容。 可輕鬆地同時支援自訂的版面配置建立內建的類似方格的版面配置。 如需詳細資訊，則[集合檢視簡介](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md)指南。


## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[PassKit 簡介](~/ios/platform/passkit.md)

「 PassKit 架構可讓應用程式與數位傳遞 Passbook 應用程式中管理的互動。 如需詳細資訊，則[傳遞的組件指南簡介](~/ios/platform/passkit.md)。


##  <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[EventKit 簡介](~/ios/platform/eventkit.md)

EventKit framework 會提供方法來存取行事曆、 行事曆事件和提醒行事曆資料庫所儲存的資料。 存取行事曆和行事曆事件便已提供 iOS 4，但 iOS 6 現在會公開提醒資料的存取權。 如需詳細資訊，請參閱 [ 我](~/ios/platform/eventkit.md)[簡介 EventKit](~/ios/platform/eventkit.md)指南。


##  <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[社交架構簡介](~/ios/platform/social-framework.md)

社交架構會提供統一的 API 來與 Twitter 和 Facebook，以及 SinaWeibo 包括中國地區使用者的社交網路互動。 如需詳細資訊，則[社交架構簡介](~/ios/platform/social-framework.md)指南。


##  <a name="changes-to-storekitchanges-to-storekitmd"></a>[StoreKit 的變更](changes-to-storekit.md)

Apple 已引進兩個套件存放區中的新功能： 購買並下載 iTunes 或您的應用程式，從 App Store 內容與裝載您的內容檔案的應用程式內購買 ！。 如需詳細資訊，請參閱，則[變更為存放區套件](changes-to-storekit.md)指南。


## <a name="other-changes"></a>其他變更


### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload 和 ViewDidUnload 已被取代

`ViewWillUnload`並`ViewDidUnload`方法`UIViewController`不再呼叫 iOS 6。 在舊版 iOS 中，這些方法可能有已使用應用程式來分別儲存檢視卸載之前, 的狀態，並清除程式碼。

比方說，Visual Studio for Mac 會建立一個方法，叫做`ReleaseDesignerOutlets`，如下所示，它會接著呼叫從`ViewDidUnload`:

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

不過，在 iOS 6 中，不再需要呼叫`ReleaseDesignerOutlets`。   
   
   
   
IOS 6 應用程式應該使用的清除程式碼， `DidReceiveMemoryWarning`。 不過，程式碼呼叫`Dispose`應謹慎且僅限記憶體密集物件做為顯示下列：

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

同樣地，呼叫`Dispose`如上應該很少會需要。 在大部分的應用程式應該執行的一般是移除事件處理常式。

儲存狀態的情況下，應用程式可以執行這`ViewWillDisappear`並`ViewDidDisappear`而不是`ViewWillUnload`。


### <a name="iphone-5-resolution"></a>iphone 5 以上解析

iphone 5 以上裝置有 640 x 1136 解析。 設為目標的舊版 iOS 的應用程式會出現 letterboxed iPhone 5 上執行時，如下所示：

 [![](images/01-letterboxed.png "設為目標的舊版 iOS 的應用程式會出現 letterboxed iPhone 5 上執行時")](images/01-letterboxed.png#lightbox)

為了讓應用程式出現在 iphone 5 以上，全螢幕只是新增名為映像`Default-568h@2x.png`有 640 x 1136 的解析度。 下列螢幕擷取畫面顯示此映像已包含在內之後執行的應用程式：

 [![](images/02-fullscreen.png "此螢幕擷取畫面顯示此映像已包含在內之後執行的應用程式")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>子類別化 UINavigationBar

在 iOS 6`UINavigationBar`可以成為子類別。 這可讓其他控制項的外觀與風格的`UINavigationBar`。 例如，應用程式可以進行子分類，即可新增子檢視，以動畫顯示這些檢視及修改的界限`UINavigationBar`。

下列程式碼顯示的子類別化之範例`UINavigationBar`，將加上`UIImageView`:

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

若要新增子類別化之`UINavigationBar`來`UINavigationController`，使用`UINavigationController`使用的型別建構函式`UINavigationBar`和`UIToolbar`，如下所示：

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

使用此`UINavigationBar`結果顯示如下列螢幕擷取畫面所示的映像檢視中的子類別：

 [![](images/03-navbar.png "正在顯示此螢幕擷取畫面所示的映像檢視中使用此 UINavigationBar 子類別化結果")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>介面方向

之前 iOS 6 應用程式可以覆寫`ShouldAutorotateToInterfaceOrientation`，特定的控制器支援任何方向傳回 true。 例如，下列程式碼可用來支援只直向：

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

在 iOS 6`ShouldAutorotateToInterfaceOrientation`已被取代。
應用程式可以改為覆寫`GetSupportedInterfaceOrientations`在根檢視控制器，如下所示：

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

在iPad上，如果未實現 `GetSupportedInterfaceOrientation`，則默認為所有四個方向。 在 iPhone 和 iPod Touch 上，預設值是以外的所有方向`PortraitUpsideDown`。
