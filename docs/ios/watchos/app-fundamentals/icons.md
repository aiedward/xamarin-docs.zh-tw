---
title: 使用 watchOS 在 Xamarin 中的圖示
description: 本文件說明各種所需的 watchOS 應用程式，以及如何設定解決方案，以包含這些圖示的圖示。
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/26/2018
ms.openlocfilehash: e46ecc9d78ccc5dcfbe571c9ec5350fe6c391b7e
ms.sourcegitcommit: ffb0f3dbf77b5f244b195618316bbd8964541e42
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39275920"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>使用 watchOS 在 Xamarin 中的圖示

Apple Watch 解決方案需要兩個集合的圖示：

* 會出現在 iPhone iOS 應用程式圖示。
* Apple Watch 通知畫面和 [監看式] 功能表上的圓圈中呈現的圖示。 監看式應用程式圖示也會出現在[Apple Watch](~/ios/watchos/app-fundamentals/settings.md) iOS 應用程式。

## <a name="apple-watch-icons"></a>Apple Watch 圖示

| | | |
|-|-|-|
|iOS 應用程式圖示|在 iPhone 上隨即出現，並啟動父應用程式|![iOS 應用程式圖示](icons-images/icon-ios.png)|
|監看式應用程式圖示|Apple Watch 主畫面上會出現|![watchOS 應用程式圖示](icons-images/icon-home.png)|
||會出現在 監看式通知|![watchOS 通知圖示](icons-images/notification-icon.png)|
||會出現在[iOS Apple Watch 應用程式](~/ios/watchos/app-fundamentals/settings.md)|![iOS Watch 應用程式圖示](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>設定您的解決方案

若要確保您的 iOS 應用程式和 watch 應用程式會示範正確的名稱和圖示，請遵循下列指示每個專案：

### <a name="ios-app"></a>iOS 應用程式

請參閱[iOS 應用程式圖示引導](~/ios/app-fundamentals/images-icons/app-icons.md)以確保您的 iOS 應用程式圖示已正確設定。

#### <a name="infoplist"></a>Info.plist

中的應用程式監看式旁邊顯示的字串[Apple Watch 設定 應用程式](~/ios/watchos/app-fundamentals/settings.md)中設定**iOS 應用程式的 Info.plist**。

確認您**Info.plist**已`CFBundleName`索引鍵和值 (注意： 這與不同`CFBundleDisplayName`，您可以同時具有):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Apple Watch 應用程式

一旦您[父應用程式](~/ios/watchos/app-fundamentals/parent-app.md)已設定其圖示，您需要監看式應用程式中新增的應用程式圖示資產目錄。

1. 監看式應用程式專案上按一下滑鼠右鍵，然後選取**檔案 > 新增 > 新增檔案 > iOS > 資產目錄**將資產目錄加入至專案。

 ![](icons-images/newasset.png "加入專案中的資產目錄")

2. 按兩下**AppIcon.appiconset/Contents.json**檔案

  ![](icons-images/xcassets-iconset-sml.png "Assets.xcassets 內容")

3. 新增 watchOS 映像時，，在此螢幕擷取畫面所示：

  [![](icons-images/appicons-sml.png "此螢幕擷取畫面所示，新增所有 watchOS 映像，")](icons-images/appicons.png#lightbox)

  請參閱[Apple 圖示的指導方針](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/)（維度也會顯示在螢幕上） 的必要大小。 請記住，這些圖示會自動裁剪在圓形中轉譯。

  圖示清單看起來應該像這樣：

  ![](icons-images/xcassets-complete-sml.png "在 [方案總管] 中的圖示清單")

4. 若要確保資產目錄包含應用程式中，新增下列索引鍵和值加入**監看式應用程式的 Info.plist**:

```xml
<key>XSAppIconAssets</key>
<string>Images.xcassets/AppIcon.appiconset</string>
```

您可以確認圖示會藉由檢查設定正確[Apple Watch 設定 應用程式](~/ios/watchos/app-fundamentals/settings.md)在 iPhone 模擬器中，或產生[通知](~/ios/watchos/platform/notifications.md)和確認圖示會出現在通知畫面。

> [!NOTE]
> 圖示不能有 alpha 色頻 （alpha 色頻是否存在，將應用程式市集提交期間拒絕應用程式）。 您可以檢查 alpha 色頻是否存在，並將它移除[Mac OS X 上使用預覽應用程式](~/ios/watchos/troubleshooting.md#noalpha)。


## <a name="related-links"></a>相關連結

- [Apple 的 watchOS 圖示和影像指南](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
