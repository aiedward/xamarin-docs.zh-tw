---
title: iOS 6 簡介
description: 本檔連結的指南會說明 iOS 6 中引進的功能。 將會討論集合視圖、PassKit、社交架構以及 StoreKit 的變更。
ms.prod: xamarin
ms.assetid: 242DA7E3-8FD8-5F20-285D-603259CA622D
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: b81e7b980c37f238fe9c2a299aa360cc01294ebe
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997185"
---
# <a name="introduction-to-ios-6"></a>iOS 6 簡介

_iOS 6 包含開發應用程式的各種新技術，Xamarin. iOS 6 帶給 c # 開發人員。_

[![IOS 6 標誌](images/ios6-large.jpg)](images/ios6-large.jpg#lightbox)

使用 iOS 6 和 Xamarin. iOS 6，開發人員現在有豐富的功能，可建立 iOS 應用程式，包括以 iPhone 5 為目標的應用程式。
本檔列出一些更令人興奮的新功能，以及每個主題的文章連結。 此外，它還在開發人員移至 iOS 6 和新的 iPhone 5 解決方案時，也需要進行一些重要的變更。

## <a name="introduction-to-collection-views"></a>[集合視圖簡介](~/ios/user-interface/controls/uicollectionview.md)

集合視圖可讓您使用任意版面配置來顯示內容。 它們可讓您輕鬆地建立現成的類似格線版面配置，同時也支援自訂版面配置。 如需詳細資訊，請參閱「 [收集視圖簡介](~/ios/user-interface/controls/uicollectionview.md) 」指南。

## <a name="introduction-to-passkit"></a>[PassKit 簡介](~/ios/platform/passkit.md)

PassKit 架構可讓應用程式與 Passbook 應用程式中所管理的數位傳遞進行互動。 如需詳細資訊，請參閱 [Pass 套件簡介指南](~/ios/platform/passkit.md)。

## <a name="introduction-to-eventkit"></a>[EventKit 簡介](~/ios/platform/eventkit.md)

EventKit 架構提供一種方式來存取行事曆資料庫儲存的行事曆、行事曆事件和提醒資料。 從 iOS 4 開始，可以存取行事曆和行事曆事件，但 iOS 6 現在會公開提醒資料的存取權。 如需詳細資訊，請參閱《 [I](~/ios/platform/eventkit.md) [組織 to EventKit](~/ios/platform/eventkit.md) guide 》。

## <a name="introduction-to-the-social-framework"></a>[社交架構簡介](~/ios/platform/social-framework.md)

社交架構提供整合的 API，可與社交網路（包括 Twitter 和 Facebook）互動，也提供適用于中國使用者的 SinaWeibo。 如需詳細資訊，請參閱《 [社交架構](~/ios/platform/social-framework.md) 指南》的簡介。

## <a name="changes-to-storekit"></a>[StoreKit 的變更](changes-to-storekit.md)

Apple 引進了商店套件中的兩項新功能：從您的應用程式購買及下載 iTunes 或 App Store 內容，並裝載應用程式內購買的內容檔案！。 如需詳細資訊，請參閱 [儲存套件](changes-to-storekit.md) 指南的變更。

## <a name="other-changes"></a>其他變更

### <a name="viewwillunload-and-viewdidunload-deprecated"></a>ViewWillUnload 和 ViewDidUnload 已淘汰

的 `ViewWillUnload` 和 `ViewDidUnload` 方法不再 `UIViewController` 于 iOS 6 中呼叫。 在舊版的 iOS 中，應用程式可能會使用這些方法在視圖卸載之前儲存狀態，並分別清除程式碼。

例如，Visual Studio for Mac 會建立名為的方法 `ReleaseDesignerOutlets` （如下所示），然後從下列方法呼叫 `ViewDidUnload` ：

```csharp
void ReleaseDesignerOutlets ()
{
    if (myOutlet != null) {
        myOutlet.Dispose ();
        myOutlet = null;
    }
}
```

不過，在 iOS 6 中，不再需要呼叫 `ReleaseDesignerOutlets` 。   

針對清除程式碼，iOS 6 應用程式應該使用 `DidReceiveMemoryWarning` 。 不過，呼叫的程式碼 `Dispose` 應謹慎使用，且僅適用于記憶體密集的物件，如下所示：

```csharp
if (myImageView != null){
    if (myImageView.Superview == null){
        myImageView.Dispose();
        myImageView = null;
    }
}
```

同樣地，您 `Dispose` 應該很少需要呼叫。 一般而言，大部分的應用程式應該要移除事件處理常式。

針對儲存狀態的情況，應用程式可以在和中執行這項工作， `ViewWillDisappear` `ViewDidDisappear` 而不是 `ViewWillUnload` 。

### <a name="iphone-5-resolution"></a>iPhone 5 解析

iPhone 5 裝置具有640x1136 解析度。 以舊版 iOS 為目標的應用程式在 iPhone 5 上執行時，將會顯示 letterboxed，如下所示：

 [![以舊版 iOS 為目標的應用程式在 iPhone 5 上執行時，會顯示為 letterboxed](images/01-letterboxed.png)](images/01-letterboxed.png#lightbox)

若要讓應用程式在 iPhone 5 上顯示全螢幕，只要新增名為 `Default-568h@2x.png` 具有640x1136 解析度的映射即可。 下列螢幕擷取畫面顯示在包含此映射之後執行的應用程式：

 [![此螢幕擷取畫面顯示在包含此映射之後，應用程式正在執行](images/02-fullscreen.png)](images/02-fullscreen.png#lightbox)

### <a name="subclassing-uinavigationbar"></a>子類別化 UINavigationBar

在 iOS 6 可以子類別化 `UINavigationBar` 。 這可讓您進一步控制的外觀和風格 `UINavigationBar` 。 例如，應用程式可以子類別來新增子檢視、建立這些視圖的動畫，以及修改的界限 `UINavigationBar` 。

下列程式碼顯示加入的子類別化範例 `UINavigationBar` `UIImageView` ：

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

若要將子 `UINavigationBar` 類別加入至 `UINavigationController` ，請使用 `UINavigationController` 接受和類型的函式 `UINavigationBar` `UIToolbar` ，如下所示：

```csharp
navController = new UINavigationController (typeof(CustomNavBar), typeof(UIToolbar));
```

使用這個子 `UINavigationBar` 類別會導致顯示影像視圖，如下列螢幕擷取畫面所示：

 [![使用這個 UINavigationBar 子類別會導致顯示影像視圖，如下列螢幕擷取畫面所示](images/03-navbar.png)](images/03-navbar.png#lightbox)

### <a name="interface-orientation"></a>介面方向

在 iOS 6 應用程式可能會覆寫之前 `ShouldAutorotateToInterfaceOrientation` ，針對特定控制器支援的任何方向傳回 true。 例如，下列程式碼只會用來支援直向：

```csharp
public override bool ShouldAutorotateToInterfaceOrientation (UIInterfaceOrientation toInterfaceOrientation)
    {
        return (toInterfaceOrientation == UIInterfaceOrientation.Portrait);
    }
```

在 iOS 6 中 `ShouldAutorotateToInterfaceOrientation` 已淘汰。
相反地，應用程式可以 `GetSupportedInterfaceOrientations` 在根視圖控制器上覆寫，如下所示：

```csharp
public override UIInterfaceOrientationMask GetSupportedInterfaceOrientations ()
    {
        return UIInterfaceOrientationMask.Portrait;
    }
```

在 iPad 上，如果未執行，這會預設為四個方向 `GetSupportedInterfaceOrientation` 。 在 iPhone 和 iPod touch 上，預設值為所有方向，但除外 `PortraitUpsideDown` 。
