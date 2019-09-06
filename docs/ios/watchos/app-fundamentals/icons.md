---
title: 使用 Xamarin 中的 watchOS 圖示
description: 本檔說明 watchOS 應用程式所需的各種圖示，以及如何設定解決方案以包含這些圖示。
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/26/2018
ms.openlocfilehash: b3968f211ff5445e8b704bbd7897f4c46ecc2be9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292584"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>使用 Xamarin 中的 watchOS 圖示

Apple Watch 解決方案需要兩組圖示：

- 將出現在 iPhone 上的 iOS 應用程式圖示。
- Apple Watch 圖示，將會在 [監看式] 功能表和通知畫面上的圓圈中轉譯。 [監看式應用程式] 圖示也會出現在[Apple Watch](~/ios/watchos/app-fundamentals/settings.md) iOS 應用程式中。

## <a name="apple-watch-icons"></a>Apple Watch 圖示

| | | |
|-|-|-|
|iOS 應用程式圖示|出現在 iPhone 上並啟動父系應用程式|![iOS 應用程式圖示](icons-images/icon-ios.png)|
|監看應用程式圖示|出現在 Apple Watch 主畫面上|![watchOS 應用程式圖示](icons-images/icon-home.png)|
||出現在監看式通知上|![watchOS 通知圖示](icons-images/notification-icon.png)|
||出現在[iOS Apple Watch 應用程式](~/ios/watchos/app-fundamentals/settings.md)中|![iOS 監看式應用程式圖示](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>設定您的解決方案

若要確保您的 iOS 應用程式和監看式應用程式都顯示正確的名稱和圖示，請針對每個專案遵循下列指示：

### <a name="ios-app"></a>iOS 應用程式

請參閱[Ios 應用程式圖示指南](~/ios/app-fundamentals/images-icons/app-icons.md)，以確保您的 ios 應用程式圖示已正確設定。

#### <a name="infoplist"></a>Info.plist

在 [ [Apple Watch 設定] 應用程式](~/ios/watchos/app-fundamentals/settings.md)的監看式應用程式旁顯示的字串，是在**iOS 應用程式的 plist**中設定。

請確認您的**plist**有`CFBundleName`索引鍵和值（注意：這`CFBundleDisplayName`與不同，您可以同時擁有兩者）：

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Apple Watch 應用程式

一旦您的[父系應用](~/ios/watchos/app-fundamentals/parent-app.md)程式已設定其圖示，您就必須將應用程式圖示資產類別目錄新增至 watch 應用程式。

1. 以滑鼠右鍵按一下 監看式應用程式 專案，然後選取 檔案 **> 加入 > 新增檔案 ...> iOS > 資產目錄**，將資產目錄新增至專案。

    ![](icons-images/newasset.png "將資產目錄新增至專案")

2. 按兩下**appicons.appiconset/內容 json**檔案

    ![](icons-images/xcassets-iconset-sml.png "AppIcon 內容")

3. 新增所有 watchOS 的映射，如下列螢幕擷取畫面所示：

    [![](icons-images/appicons-sml.png "新增所有 watchOS 的映射，如下列螢幕擷取畫面所示")](icons-images/appicons.png#lightbox)

    請參閱[Apple 的圖示指導方針](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/)，以取得所需的大小（這些維度也會顯示在畫面上）。 請記住，這些圖示會自動裁剪以在圓形中呈現。

    您的圖示清單看起來應該像這樣：

    ![](icons-images/xcassets-complete-sml.png "方案總管中的圖示清單")

4. 若要確保資產目錄包含在應用程式中，請將下列索引鍵和值新增至**Watch 應用程式的 Info. plist**：

    ```xml
    <key>XSAppIconAssets</key>
    <string>Images.xcassets/AppIcon.appiconset</string>
    ```

您可以檢查 iPhone 模擬器中的 [ [Apple Watch 設定] 應用程式](~/ios/watchos/app-fundamentals/settings.md)，或產生[通知](~/ios/watchos/platform/notifications.md)並確認圖示是否出現在通知畫面上，確認已正確設定圖示。

> [!NOTE]
> 圖示不能有 Alpha 色板（如果有 Alpha 色板，應用程式將會在 App Store 提交期間遭到拒絕）。 您可以[使用 Mac OS X 上的預覽應用程式](~/ios/watchos/troubleshooting.md#noalpha)，檢查 Alpha 通道是否存在，並將其移除。


## <a name="related-links"></a>相關連結

- [Apple 的 watchOS 圖示 & 影像指南](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
