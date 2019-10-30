---
title: 將 watchOS 應用程式部署至 App Store
description: 本檔說明如何將以 Xamarin 建立的 watchOS 應用程式部署至 App Store。 它會探討發佈布建設定檔和 iTunes Connect，同時也提供一些疑難排解秘訣。
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: bbf580007f4d149501efe424f0e36178a49f6aa5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028375"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>將 watchOS 應用程式部署至 App Store

> [!IMPORTANT]
> 請務必參閱[Apple 的監看式套件提交指南](https://developer.apple.com/app-store/watch/)，並查看[疑難排解](#troubleshooting)一節中您可能會遇到的任何問題。

- 請確定您有：
  - 為您的專案建立的發佈布建[**設定檔**](#provisioning)。
  - IOS 父系應用程式的**部署目標**（`MinimumOSVersion`）已設定為**8.2**或更早版本（不支援8.3）。

- 在[**ITunes Connect**](#iTunes_Connect)中：

  - 建立您的 iOS 應用程式專案（或將**新版本新增**至現有的應用程式）。
  - 新增監看式圖示和螢幕擷取畫面。

- 然後在[Visual Studio for Mac](#xamarin_studio) （Visual Studio 目前不受支援）：

  - 以滑鼠右鍵按一下 iOS 應用程式，然後選擇 [**設定為啟始專案**]。
  - 變更為**App Store**設定。
  - 使用封存**功能建立**應用程式封存。

- 最後，切換至[Xcode 6.2 +](#xcode)

  - 移至 [ **> 召集人] 視窗**，**然後選擇 [** 封存]。
  - 從清單中選取應用程式和封存。
  - 也**驗證 ...** 封存。
  - **提交 ...** 封存並遵循上傳至 iTunes Connect 的步驟進行審查和核准。

閱讀下列與下列專案相關的特定提示。 如果您有任何問題，請參閱[疑難排解](#troubleshooting)一節。

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>發佈布建設定檔

若要建立 App Store 部署，您需要為解決方案中的每個應用程式識別碼建立發佈布建**設定檔**。

如果您有萬用字元應用程式識別碼，則*只需要一個布建設定檔*;但如果您的每個專案都有個別的應用程式識別碼，則您需要每個應用程式識別碼的布建設定檔：

![](appstore-images/provisioningprofile-distribution-sml.png "The App Store Distribution profile")

一旦您建立了這三個設定檔，它們就會出現在清單中。 請記得下載並安裝每一個檔案（藉由在上面按兩下）：

![](appstore-images/provisioningprofiles-sml.png "The list of available profiles")

您可以選取 **組建 > iOS**套件組合簽署 畫面，然後選取  **AppStore | iPhone**設定，以確認**專案選項**中的布建設定檔。

[布建**設定檔**] 清單會顯示所有相符的設定檔，您應該會看到您在此下拉式清單中建立的相符設定檔。

![](appstore-images/options-selectprofile-sml.png "The iOS Bundle Signing dialog")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

請遵循[應用程式散發總覽](~/ios/deploy-test/app-distribution/index.md)，特別是：

- [在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [發行至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

在 iTunes Connect 中設定應用程式時，別忘了新增監看式圖示和螢幕擷取畫面：

![](appstore-images/itunesconnect-watch-sml.png "The Watch icon and screenshots in iTunes Connect")

圖示檔案應該是1024x1024 圖元，而且會在顯示時套用迴圈遮罩。 圖示不應該有 Alpha 色板。

至少需要一個螢幕擷取畫面，最多可以提交五個。
它們應該是312x390 圖元，並示範監看式應用程式的實際運作。
您可以使用 42mm watch 模擬器，以這種大小來拍螢幕擷取畫面。

<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 確認 iOS 應用程式是啟始專案。 如果沒有，請按一下滑鼠右鍵加以設定：

   ![](appstore-images/xs-startup.png "Setting the startup project")

2. 選擇 [ **AppStore**組建設定]：

   ![](appstore-images/xs-appstore.png "The AppStore build configuration")

3. 選擇 [**組建 >** 封存] 功能表項目，以啟動封存程式：

   ![](appstore-images/xs-archive.png "The Build menu")

您也可以選擇 [ **View > 封存 ...** ] 功能表項目，以查看先前建立的封存。

  ![](appstore-images/xs-archives-sml.png "The Archives view")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode 會自動顯示 Visual Studio for Mac 中建立的封存。

1. 啟動 Xcode，然後選擇 [ **Window > 召集人]** ：

   ![](appstore-images/xc-organizer.png "The Window menu")

2. 切換至 [**保存] 索引標籤，然後**選取使用 Visual Studio for Mac 建立的封存：

   ![](appstore-images/xc-archives.png "The Archives tab")

3. 選擇性地**驗證 ...** 封存，然後選擇 [**提交**]，將應用程式上傳至 iTunes Connect。

4. 選擇 [開發小組] （如果您屬於一個以上），然後確認提交：

   ![](appstore-images/xc-submit1.png "The development team section")

5. 再次流覽 iTunes Connect 以查看已上傳的二進位檔。 移至您應用程式的 [設定] 頁面，然後從頂端功能表中選擇 [**發行**前版本]，以查看**組建**清單：

   [![](appstore-images/itc-prerelease-sml.png "The apps configuration page in iTunes Connect")](appstore-images/itc-prerelease.png#lightbox)

然後，您可以在 [**版本**] 頁面上提交要核准的應用程式。 如需詳細資訊，請參閱[iOS 應用程式散發總覽](~/ios/deploy-test/app-distribution/index.md)。

## <a name="troubleshooting"></a>疑難排解

以下是您在提交至 App Store 時可能會遇到的一些錯誤，以及您可以採取的步驟來修正它們。

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>[封存] 功能表選項不會顯示在 Visual Studio for Mac

請遵循[上述步驟](#xamarin_studio)來設定封存的解決方案。 如果您無法正確設定啟始專案，請確定組建設定在嘗試變更啟始專案之前，先設為 [Debug] 或 [Release]。 然後將組建設定設回**AppStore**。

### <a name="invalid-icon"></a>無效圖示

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcon27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

請依照[指示，](~/ios/watchos/troubleshooting.md)從您的圖示中移除 Alpha 色板。

### <a name="cfbundleversion-mismatch"></a>CFBundleVersion 不符

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

解決方案中的所有專案（iOS 應用程式、監看式擴充功能和監看式應用程式）都應使用相同的版本號碼。 編輯每個**plist**檔案，讓版本號碼完全相符。

### <a name="missing-icons"></a>遺漏圖示

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

遵循[使用圖示](~/ios/watchos/app-fundamentals/icons.md)中的指示，將所有必要的影像新增至 Watch 應用程式專案。

### <a name="missing-icon"></a>遺失圖示

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

請確定您有最新版本的 Visual Studio for Mac，而且您的**appicons.appiconset**包含一組完整的影像。 如果您仍然看到此錯誤，請查看**內容**的來源，以確認它包含所有必要映射的專案。 或者，一旦確定您使用的是最新版本的 Xamarin，請刪除並重新建立**appicons.appiconset**。

> [!IMPORTANT]
> Visual Studio for Mac 的監看式圖示支援中有已知的錯誤：它需要 **29x29@3x** 影像的88x88 圖元影像（應該是87x87 圖元）。

您無法在 Visual Studio for Mac 中修正此問題-請在 Xcode 中編輯映射資產，或手動編輯**內容 json**檔案（以符合[此範例](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132)）。

### <a name="invalid-watchkit-support"></a>不正確 WatchKit 支援

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

這則訊息可能會在驗證和提交期間出現，或在顯然成功上傳之後，從 iTunes Connect 的自動化電子郵件中顯示。
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> 您必須在 Visual Studio for Mac 中封存**您的應用**程式，然後切換至 Xcode 6.2 + 以驗證並上傳至 iTunes Connect。

使用穩定的 Xamarin 通道和 Xcode 6.2 +。

### <a name="invalid-provisioning-profile"></a>布建設定檔無效

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

在監看式應用程式解決方案中，所有三個專案都必須提供發佈布建**設定檔**： IOS 應用程式、監看式擴充功能和監看式應用程式-明確（三個設定檔）或透過單一萬用字元設定檔。 檢查布建設定檔是否存在於 iOS 開發人員中心，並已下載並新增至您的 Mac。

### <a name="invalid-code-signing-entitlements"></a>不正確程式碼簽署權利

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

請確定您已在 Apple 開發人員中心正確設定您的布建設定檔，並已下載並安裝它們。 此外，也請檢查它們是否已在 Visual Studio for Mac 的 [屬性] 視窗中針對每個專案設定。

### <a name="invalid-architecture"></a>不正確架構

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

您只能新增監看式應用程式[Unified API （64位）](~/cross-platform/macios/unified/index.md) Xamarin iOS 應用程式。
以滑鼠右鍵按一下 iOS 應用程式專案，然後移至 [**選項] > 組建 > IOS 組建 > [Advanced]** 索引標籤，並確定 AppStore-IPhone 設定**支援的架構**包含**ARM64** （例如 **ARMv7 + ARM64**）。

### <a name="this-bundle-is-invalid"></a>此組合無效。

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

您的父系 iOS 應用程式必須將 MinimumOSVersion 設為 ' 8.2 ' 或更早的版本。

### <a name="non-public-api-usage"></a>非公用 API 使用方式

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

請確定您使用的是最新版本的 Xcode 和 Xamarin 工具。
您的程式碼不應存取任何非公用 Api。

### <a name="build-error-mt5309"></a>組建錯誤 MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

此錯誤可能是因為您已從**Xcode**重新命名 Xcode 安裝所導致。 比方說，如果您將安裝重新命名為**XCode 6.2. 應用程式**，就會發生此錯誤。

## <a name="related-links"></a>相關連結

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
