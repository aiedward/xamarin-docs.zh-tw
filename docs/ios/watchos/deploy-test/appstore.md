---
title: WatchOS 應用程式部署至 App Store
description: 本文件說明如何部署至 App Store 的 xamarin 建置的 watchOS 應用程式。 它探討發佈佈建設定檔和 iTunes Connect，並也提供疑難排解秘訣。
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 90058f5074759fdded5d259004cb40c0cb7ea212
ms.sourcegitcommit: ffb0f3dbf77b5f244b195618316bbd8964541e42
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39276063"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>WatchOS 應用程式部署至 App Store

> [!IMPORTANT]
> 請務必檢閱[Apple Watch 套件提交指南](https://developer.apple.com/app-store/watch/)，並查看[疑難排解](#Troubleshooting)區段，您可能會有任何問題。

- 請確定您已：
  - [**發佈佈建設定檔**](#provisioning)為您的專案建立的。
  - **部署目標**(`MinimumOSVersion`) 的 iOS 父應用程式設定為**8.2**或更早版本 （不支援 8.3）。

- 在  [ **iTunes Connect**](#iTunes_Connect):

  - 建立您的 iOS 應用程式項目 (或新增**新版**現有的應用程式)。
  - 新增監看式 圖示和螢幕擷取畫面。

- 然後在[Visual Studio for Mac](#xamarin_studio) （Visual Studio 目前不支援）：

  - 以滑鼠右鍵按一下 iOS 應用程式，然後選擇 **設定為啟始專案**。
  - 若要變更**App Store**組態。
  - 使用**封存**功能建立應用程式封存檔。

- 最後，切換至[Xcode 6.2 +](#xcode)

  - 移至**視窗中 > 組合管理**，然後選擇**封存**。
  - 從清單中選取的應用程式和封存。
  - （選擇性）**驗證...** 封存。
  - **提交...** 封存和後續的步驟，將上傳至 iTunes Connect 進行審查與核准。

請閱讀以下這些項目與相關的特定提示。 請參閱[疑難排解](#Troubleshooting)區段如果您有問題。

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>發佈佈建設定檔

若要建置應用程式市集部署，您必須建立**發佈佈建設定檔**方案中每個應用程式識別碼。

如果您有萬用字元應用程式識別碼*只有一個的佈建設定檔必須*; 但如果您有個別的應用程式識別碼，針對每個專案，則您必須針對每個應用程式識別碼佈建設定檔：

![](appstore-images/provisioningprofile-distribution-sml.png "App Store 散發設定檔")

一旦您已建立所有的三個設定檔，即會顯示在清單中。 請務必下載並安裝每個 （藉由按兩下）：

![](appstore-images/provisioningprofiles-sml.png "可用的設定檔清單")

您可以在佈建設定檔，確認**專案選項**藉由選取**建置 > iOS 套件組合簽署**畫面，然後選取**AppStore | iPhone**組態設定。

**佈建設定檔**清單會顯示所有相符的設定檔-您應該會看到您已在此下拉式清單中的比對設定檔。

![](appstore-images/options-selectprofile-sml.png "[IOS 套件組合簽署] 對話方塊")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

請遵循[應用程式散發概觀](~/ios/deploy-test/app-distribution/index.md)，特別是：

- [在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [發行至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

當在 iTunes Connect 中設定應用程式，別忘了將監看式 圖示和螢幕擷取畫面：

![](appstore-images/itunesconnect-watch-sml.png "監看式 圖示，在 iTunes Connect 中的螢幕擷取畫面")

圖示檔應該是 1024 x 1024 像素，並將會有循環的遮罩出現時套用。 圖示不應該有 alpha 色頻。

至少一個螢幕擷取畫面是必要的可能會提交最多五個。
它們應該是 312 x 390 像素，並示範 Watch 應用程式中的動作。
您可以使用 42 mm 監看式模擬器，以便在這種大小的螢幕擷取畫面。


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 請確定啟動專案為 iOS 應用程式。 如果沒有，以滑鼠右鍵按一下 設定它：

  ![](appstore-images/xs-startup.png "設定啟始專案")

2. 選擇**AppStore**建置組態：

  ![](appstore-images/xs-appstore.png "AppStore 組建組態")

3. 選擇**建置 > 封存**功能表項目，以啟動 封存處理序：

  ![](appstore-images/xs-archive.png "[建置] 功能表")

您也可以選擇**檢視 > 封存...** 功能表項目，請參閱先前建立的封存。

  ![](appstore-images/xs-archives-sml.png "[封存] 檢視")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode 會自動顯示在 Visual Studio for mac 中建立的封存

1. 啟動 Xcode，然後選擇**視窗中 > 組合管理**:

  ![](appstore-images/xc-organizer.png "[視窗] 功能表")

2. 若要切換**封存**索引標籤，然後選取 使用 Visual Studio for Mac 中建立的封存：

  ![](appstore-images/xc-archives.png "[封存] 索引標籤")

3. 選擇性地**驗證...** 封存，然後選擇 **提交...** 來上傳至 iTunes Connect 的應用程式。

4. （如果您隸屬於多個），請選擇開發小組，然後確認 送出作業：

  ![](appstore-images/xc-submit1.png "開發小組區段")

5. 請瀏覽 iTunes Connect，以查看已上傳二進位檔。 移至您的應用程式設定 頁面，然後選擇**發行前版本**從上方的功能表，以查看**建置**清單：

  [![](appstore-images/itc-prerelease-sml.png "在 iTunes Connect 中的 [應用程式組態] 頁面")](appstore-images/itc-prerelease.png#lightbox)

然後，您可以將核准的應用程式提交上**版本**頁面。 請參閱[iOS 應用程式散發概觀](~/ios/deploy-test/app-distribution/index.md)如需詳細資訊。


## <a name="troubleshooting"></a>疑難排解

以下是一些在提交至 App Store，並加以修正，您可以採取的步驟時可能會遇到的錯誤。

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>Visual Studio for Mac 中看不到 [封存] 功能表選項

請遵循[上述步驟](#xamarin_studio)若要設定之解決方案的封存。 如果您不能正確地設定啟始專案，請確定組建組態先設為偵錯或發行之前嘗試變更啟始專案。 然後將設定組建組態回**AppStore**。

### <a name="invalid-icon"></a>無效圖示

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

請遵循[指示移除 alpha 色頻](~/ios/watchos/troubleshooting.md)從您的圖示。

### <a name="cfbundleversion-mismatch"></a>CFBundleVersion 不相符

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

-IOS 應用程式、 監看式延伸模組，以及監看式應用程式-您方案中的所有專案都應該都使用相同的版本號碼。 編輯每個**Info.plist**檔案，以便完全符合的版本號碼。

### <a name="missing-icons"></a>缺少圖示

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

請依照下列中的指示[使用圖示](~/ios/watchos/app-fundamentals/icons.md)Watch 應用程式專案中加入所需的影像。

### <a name="missing-icon"></a>缺少圖示

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

請確定您有最新版本的 Visual Studio for Mac，且您**AppIcon.appiconset**包含一組完整的映像。 如果您仍然看到此錯誤，檢視的來源**Contents.json**以確認它包含所有必要的映像的項目。 或者，確定您使用最新版的 Xamarin，一旦刪除並重新建立**AppIcon.appiconset**。

> [!IMPORTANT]
> 沒有已知的 bug 在 Visual Studio for Mac 的監看式 圖示支援： 預期應為 88 x 88 像素映像**29x29@3x**映像 （這應該是 87 x 87 像素為單位）。


您無法修正此問題在 Visual Studio for Mac-影像資產在 Xcode 中編輯，或以手動方式編輯**Contents.json**檔案 (比對[本例](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132))。



### <a name="invalid-watchkit-support"></a>無效的 WatchKit 支援

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

此訊息可能出現顯然成功上傳之後，從 iTunes Connect 期間驗證和提交，或在自動化的電子郵件。
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> 您必須**封存**Visual Studio for Mac 然後再切換到 Xcode 6.2 + 來驗證和上傳至 iTunes Connect 中的應用程式。


使用 Xamarin 穩定通道和 Xcode 6.2 +。



### <a name="invalid-provisioning-profile"></a>無效的佈建設定檔

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**發佈佈建設定檔**必須提供可以在監看式應用程式方案中的所有三個專案： iOS 應用程式、 監看式延伸模組，以及監看式應用程式-可能是明確 （三個設定檔） 或透過單一萬用字元設定檔。 請檢查佈建設定檔是否存在於 iOS 開發人員中心和您已下載，並將資料新增到您的 mac。

### <a name="invalid-code-signing-entitlements"></a>無效的程式碼簽署權利

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

請確定您的佈建設定檔會在 Apple 開發人員中心上正確設定，且您已下載並安裝它們。 也請檢查 Visual Studio 中設定的每個專案的 Mac 的 [屬性] 視窗。

### <a name="invalid-architecture"></a>無效的架構

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

您只能新增監看式應用程式[統一的 API （64 位元）](~/cross-platform/macios/unified/index.md) Xamarin.iOS 應用程式。
以滑鼠右鍵按一下 iOS 應用程式專案，然後移至**選項 > 建置 > iOS 組建 > 進階 索引標籤**，並確定**支援的架構**AppStore iphone 組態包括**ARM64** （例如。 **ARMv7 + ARM64**)。

### <a name="this-bundle-is-invalid"></a>此搭售方案無效。

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

父代的 iOS 應用程式必須設定 '8.2' 或更早 MinimumOSVersion。

### <a name="non-public-api-usage"></a>非公用 API 使用方式

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

請確定您使用最新版的 Xcode 及 Xamarin 的工具。
您的程式碼不應該存取的任何非公用 Api。

### <a name="build-error-mt5309"></a>建置錯誤 MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

此錯誤可能是因為您需要重新命名您的 Xcode 安裝從**Xcode.app**。 比方說，如果您重新命名您的安裝以將會發生此錯誤**XCode 6.2.app**。



## <a name="related-links"></a>相關連結

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
