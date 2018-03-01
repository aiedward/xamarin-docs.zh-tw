---
title: "使用圖示"
ms.topic: article
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 38a2e8b8cd5932bf96c1e0032a6f47627c3ea592
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-icons"></a>使用圖示

Apple Watch 解決方案都需要兩個集合的圖示：

* 會出現在 iPhone iOS 應用程式圖示。
* 在 [監看式] 功能表上的圓圈和通知螢幕中呈現的 Apple Watch 圖示。 監看式應用程式圖示也會出現在[Apple Watch](~/ios/watchos/app-fundamentals/settings.md) iOS 應用程式。

## <a name="apple-watch-icons"></a>Apple Watch 圖示

<table align="center" border="1" cellpadding="1" cellspacing="1">
    <tr>
      <td valign="top">
        <b>iOS 應用程式圖示</b>
      </td>
      <td valign="top">
在 iPhone 上隨即出現，並啟動父應用程式 </td>
      <td>
        <img src="icons-images/icon-ios.png" class="tableimg">
      </td>
    </tr>
    <tr>
      <td valign="top" rowspan="3">
        <b>監看式應用程式圖示</b>
      </td>
      <td valign="top">
Apple Watch 主畫面上會出現 </td>
      <td>
        <img src="icons-images/icon-home.png" class="tableimg" />
      </td>
    </tr>
    <tr>
      <td valign="top">
會出現在 監看式通知 </td>
      <td>
        <img src="icons-images/notification-icon.png" class="tableimg" />
      </td>
    </tr>
    <tr>
      <td valign="top">
會出現在<a href="~/ios/watchos/app-fundamentals/settings.md">iOS Apple Watch 應用程式</a>
      </td>
      <td>
        <a href="icons-images/watch-app.png">
          <img src="icons-images/watch-app-sml.png" class="tableimg">
        </a>
      </td>
    </tr>
    <tbody>
</table>



## <a name="configuring-your-solution"></a>設定您的方案

若要確保您的 iOS 應用程式和監看式應用程式會顯示正確的名稱和圖示，請遵循這些指示，針對每個專案：

### <a name="ios-app"></a>iOS App

請參閱[iOS 應用程式圖示引導](~/ios/app-fundamentals/images-icons/app-icons.md)來確定已正確設定您的 iOS 應用程式圖示。

#### <a name="infoplist"></a>Info.plist

中的應用程式監看式旁邊會出現在字串[Apple Watch 設定應用程式](~/ios/watchos/app-fundamentals/settings.md)中設定**iOS 應用程式 Info.plist**。

確認您**Info.plist**具有`CFBundleName`索引鍵和值 (注意： 這是不同`CFBundleDisplayName`，您可以同時具有):

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Apple Watch 應用程式

一次您[父應用程式](~/ios/watchos/app-fundamentals/parent-app.md)已設定其圖示，您需要將應用程式圖示資產目錄加入至監看式應用程式。

1. 監看式應用程式專案上按一下滑鼠右鍵，然後選取**檔案 > 新增 > 新檔案 > iOS > 資產目錄**將資產目錄加入至專案。

 ![](icons-images/newasset.png "將資產目錄加入至專案")

2. 按兩下**AppIcons.appiconset/Contents.json**檔案

  ![](icons-images/xcassets-iconset-sml.png "AppIcons 內容")

3. 將所有 watchOS 映像，都加入這個螢幕擷取畫面所示：

  [ ![](icons-images/appicons-sml.png "加入所有 watchOS 映像，這個螢幕擷取畫面所示")](icons-images/appicons.png)

  請參閱[Apple 圖示指導方針](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/IconandImageSizes.html)的所需的大小 （維度也會顯示在螢幕）。 請記住，這些圖示會自動裁剪的圓形呈現。

  圖示清單看起來應該像這樣：

  ![](icons-images/xcassets-complete-sml.png "在 [方案總管] 中的圖示清單")

4. 若要確保資產目錄包含應用程式中，加入下列機碼和值加入**監看式應用程式 Info.plist**:

```xml
<key>XSAppIconAssets</key>
<string>Images.xcassets/AppIcons.appiconset</string>
```

您可以確認圖示會藉由檢查設定正確[Apple Watch 設定應用程式](~/ios/watchos/app-fundamentals/settings.md)iphone 模擬器，或者產生[通知](~/ios/watchos/platform/notifications.md)和確認圖示會出現在通知畫面。

> [!NOTE]
> **請注意**： 圖示不能有 alpha 色板 （如果存在 alpha 色板，將應用程式市集提交期間拒絕應用程式）。 您可以檢查 alpha 色板是否存在，並將它移除[預覽應用程式使用 Mac OS X 上](~/ios/watchos/troubleshooting.md#noalpha)。


## <a name="related-links"></a>相關連結

- [Apple 的圖示 （& s） 影像指南](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/IconandImageSizes.html)
