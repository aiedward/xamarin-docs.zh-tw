---
title: iOS 6 簡介
description: 本檔連結的指南會說明 iOS 6 中引進的功能。 集合視圖、PassKit、社交架構和 StoreKit 的變更全都討論過。
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: d30789a33eb8023f4ce77b4a7c2445fabca0f2db
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031840"
---
# <a name="introduction-to-ios-6"></a>iOS 6 簡介

_iOS 6 包含各種用來開發應用程式的新技術，而 Xamarin. iOS 6 則C#帶給開發人員。_

[![](images/ios6-large.jpg "The iOS 6 logo")](images/ios6-large.jpg#lightbox)

在 iOS 6 和 Xamarin ios 6 中，開發人員現在有豐富的功能可供他們建立 iOS 應用程式，包括以 iPhone 5 為目標的工作。
本檔列出一些較令人興奮的新功能，以及每個主題的文章連結。 此外，它還會在開發人員移至 iOS 6 和新的 iPhone 5 解決方案時，對一些重要的變更有所區別。

## <a name="introduction-to-collection-viewsiosuser-interfacecontrolsuicollectionviewmd"></a>[集合視圖簡介](~/ios/user-interface/controls/uicollectionview.md)

集合視圖可讓您使用任意版面配置來顯示內容。 它們可讓您輕鬆地建立現成的格線版面配置，同時也支援自訂版面配置。 如需詳細資訊，請參閱[集合 Views 簡介](~/ios/user-interface/controls/uicollectionview.md) [](~/ios/user-interface/controls/uicollectionview.md)指南。

## <a name="introduction-to-passkitiosplatformpasskitmd"></a>[PassKit 簡介](~/ios/platform/passkit.md)

PassKit 架構可讓應用程式與 Passbook 應用程式中所管理的數位傳遞互動。 如需詳細資訊，請參閱[通過套件簡介指南](~/ios/platform/passkit.md)。

## <a name="introduction-to-eventkitiosplatformeventkitmd"></a>[EventKit 簡介](~/ios/platform/eventkit.md)

EventKit 架構可讓您存取行事曆資料庫儲存的行事曆、行事曆事件和提醒資料。 從 iOS 4 開始可以存取行事曆和行事曆事件，但 iOS 6 現在會公開提醒資料的存取權。 如需詳細資訊，請參閱 < [I](~/ios/platform/eventkit.md) [ntroduction to EventKit](~/ios/platform/eventkit.md)指南。

## <a name="introduction-to-the-social-frameworkiosplatformsocial-frameworkmd"></a>[社交架構簡介](~/ios/platform/social-framework.md)

社交架構提供統一的 API 來與社交網路互動，包括 Twitter 和 Facebook，以及適用于中國使用者的 SinaWeibo。 如需詳細資訊，請參閱[《社交架構簡介](~/ios/platform/social-framework.md)》指南。

## <a name="changes-to-storekitchanges-to-storekitmd"></a>[StoreKit 的變更](changes-to-storekit.md)

Apple 在商店套件中引進了兩項新功能：從您的應用程式購買並下載 iTunes 或 App Store 內容，以及裝載應用程式內購買的內容檔案！。 如需詳細資訊，請參閱[儲存套件](changes-to-storekit.md)指南的變更。

## <a name="other-changes"></a>其他變更

### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload 和 ViewDidUnload 已被取代

在 iOS 6 中，不會再呼叫 `UIViewController` 的 `ViewWillUnload` 和 `ViewDidUnload` 方法。 在舊版的 iOS 中，應用程式可能會使用這些方法來儲存視圖，然後分別清除。

例如，Visual Studio for Mac 會建立名為 `ReleaseDesignerOutlets`的方法，如下所示，這會從 `ViewDidUnload`呼叫：

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

不過，在 iOS 6 中，已不再需要呼叫 `ReleaseDesignerOutlets`。   

針對清除程式碼，iOS 6 應用程式應該使用 `DidReceiveMemoryWarning`。 不過，呼叫 `Dispose` 的程式碼應該謹慎使用，而且只用于需要大量記憶體的物件，如下所示：

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

同樣地，很少需要呼叫如上所述的 `Dispose`。 一般來說，大部分的應用程式都應該移除事件處理常式。

針對儲存狀態的情況，應用程式可以在 `ViewWillDisappear` 中執行此工作，並 `ViewDidDisappear` 而不是 `ViewWillUnload`。

### <a name="iphone-5-resolution"></a>iPhone 5 解決方案

iPhone 5 裝置具有640x1136 解析度。 以舊版 iOS 為目標的應用程式會在 iPhone 5 上執行時顯示為 letterboxed，如下所示：

 [![](images/01-letterboxed.png "Applications that targeted previous versions of iOS will appear letterboxed when run on an iPhone 5")](images/01-letterboxed.png#lightbox)

若要讓應用程式在 iPhone 5 上以全螢幕顯示，只要新增名為的映射，`Default-568h@2x.png` 640x1136 的解析度即可。 下列螢幕擷取畫面顯示在包含此映射之後執行的應用程式：

 [![](images/02-fullscreen.png "This screenshot shows the application running after this image has been included")](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>子類別化 UINavigationBar

在 iOS 6 中 `UINavigationBar` 可以子類別化。 這可讓您進一步控制 `UINavigationBar`的外觀與風格。 例如，應用程式可以子類別化以加入子檢視、建立這些視圖的動畫，以及修改 `UINavigationBar`的範圍。

下列程式碼顯示加入 `UIImageView`之子類別化 `UINavigationBar` 的範例：

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

若要將子類別化 `UINavigationBar` 新增至 `UINavigationController`，請使用採用 `UINavigationBar` 和 `UIToolbar`類型的 `UINavigationController` 函式，如下所示：

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

使用此 `UINavigationBar` 子類別會導致影像視圖顯示，如下列螢幕擷取畫面所示：

 [![](images/03-navbar.png "Using this UINavigationBar subclass results in the image view being displayed as shown in this screenshot")](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>介面方向

在 iOS 6 應用程式之前，您可以覆寫 `ShouldAutorotateToInterfaceOrientation`，為特定控制器支援的任何方向傳回 true。 例如，下列程式碼只會用來支援直向：

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

在 iOS 6 中，`ShouldAutorotateToInterfaceOrientation` 已被取代。
相反地，應用程式可以覆寫根視圖控制器上的 `GetSupportedInterfaceOrientations`，如下所示：

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

在 iPad 上，如果未執行 `GetSupportedInterfaceOrientation`，這會預設為全部四個方向。 在 iPhone 和 iPod Touch 上，預設值是除了 `PortraitUpsideDown`以外的所有方向。
