---
title: WatchOS 應用程式部署的應用程式市集
description: 本文件說明如何部署 watchOS 建置 xamarin 應用程式市集的應用程式。 花在看發佈佈建設定檔和 iTunes Connect，並且提供疑難排解秘訣。
ms.prod: xamarin
ms.assetid: DBE16040-70D2-4F61-B5F3-C8D213DBC754
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 70613c79c2ec0c81f1dbdc218b747f809f859767
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790980"
---
# <a name="deploying-watchos-apps-to-the-app-store"></a>WatchOS 應用程式部署的應用程式市集

> [!IMPORTANT]
> 請務必檢閱[Apple Watch 套件提交指南](https://developer.apple.com/app-store/watch/)，並查看[疑難排解](#Troubleshooting)> 一節，您可能會有任何問題。

- 請確定您具有：
  - [**發佈佈建的設定檔**](#provisioning)為您的專案建立的。
  - **部署目標**(`MinimumOSVersion`) 針對 iOS 父應用程式設定為**8.2**或更早版本 （不支援 8.3）。

- 在[ **iTunes Connect**](#iTunes_Connect):

  - 建立您的 iOS 應用程式項目 (或新增**新版**現有應用程式)。
  - 加入監看式 圖示和螢幕擷取畫面。

- 接著在[Visual Studio for Mac](#xamarin_studio) （Visual Studio 目前不支援）：

  - 以滑鼠右鍵按一下 iOS 應用程式，然後選擇 **設定為啟始專案**。
  - 若要變更**App Store**組態。
  - 使用**封存**功能建立應用程式封存檔。

- 最後，切換至[Xcode 6.2 +](#xcode)

  - 移至**視窗 > 組合管理**選擇**封存**。
  - 從清單中選取的應用程式和封存。
  - （選擇性）**驗證...** 封存。
  - **提交...** 封存和後續的步驟，將上傳至 iTunes 連接供審查與核准。

讀取特定以下這些項目與相關的提示。 請參閱[疑難排解](#Troubleshooting)區段中如果有問題。

<a name="provisioning" />

## <a name="distribution-provisioning-profiles"></a>發佈佈建設定檔

若要建置應用程式市集部署，您必須建立**發佈佈建設定檔**方案中每個應用程式識別碼。

如果您有應用程式識別碼，萬用字元*只能有一個佈建設定檔將需要*; 但如果您有個別的應用程式識別碼，每個專案，則您必須針對每個應用程式識別碼佈建設定檔：

![](appstore-images/provisioningprofile-distribution-sml.png "應用程式市集發佈設定檔")

一旦您已建立所有的三個設定檔，即會顯示在清單中。 請記得要下載並安裝每個 （藉由按兩下）：

![](appstore-images/provisioningprofiles-sml.png "可用的設定檔清單")

您可以在佈建設定檔來確認**專案選項**選取**建置 > iOS 套件組合簽署**螢幕，然後選取**AppStore | iPhone**組態設定。

**佈建設定檔**清單會顯示所有相符的設定檔-您應該會看到您已建立此下拉式清單中的比對設定檔。

![](appstore-images/options-selectprofile-sml.png "IOS 套件組合簽署 對話方塊")

<a name="iTunes_Connect"/>

## <a name="itunes-connect"></a>iTunes Connect

請遵循[應用程式發佈概觀](~/ios/deploy-test/app-distribution/index.md)，特別是：

- [在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [發行至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)

當在 iTunes Connect 中設定應用程式，別忘了加入監看式 圖示和螢幕擷取畫面：

![](appstore-images/itunesconnect-watch-sml.png "監看式 圖示，在 iTunes Connect 中的螢幕擷取畫面")

圖示檔應為 1024 x 1024 像素，而且必須循環的遮罩時就會顯示套用到它。 圖示不應該有 alpha 色板。

需要至少一個螢幕擷取畫面，可以提交最多五個。
它們應該是 312 x 390 像素為單位，也示範動作中的應用程式監看式。
您可以使用 42 公釐監看式模擬器擷取螢幕擷取畫面，這個大小。


<a name="xamarin_studio" />

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

1. 請確定啟動專案為 iOS 應用程式。 如果沒有，以滑鼠右鍵按一下將它設定：

  ![](appstore-images/xs-startup.png "設定啟始專案")

2. 選擇**AppStore**建置組態：

  ![](appstore-images/xs-appstore.png "AppStore 組建組態")

3. 選擇**建置 > 封存**功能表項目以啟動封存程序：

  ![](appstore-images/xs-archive.png "[建置] 功能表")

您也可以選擇**檢視 > 封存...** 功能表項目，請參閱先前建立的封存。

  ![](appstore-images/xs-archives-sml.png "封存檢視")

<a name="xcode" />

## <a name="xcode"></a>Xcode

Xcode 會自動顯示封存建立在 Visual Studio for mac。

1. 啟動 Xcode，並選擇**視窗 > 組合管理**:

  ![](appstore-images/xc-organizer.png "視窗功能表")

2. 切換至**封存**索引標籤並選取 使用 Visual Studio 建立適用於 Mac 的封存：

  ![](appstore-images/xc-archives.png "[封存] 索引標籤")

3. 選擇性地**驗證...** 封存，然後選擇 **送出...** iTunes Connect 來上傳應用程式。

4. 選擇開發團隊] （如果您隸屬於多個），然後確認 [送出：

  ![](appstore-images/xc-submit1.png "開發小組的區段")

5. 請瀏覽 iTunes Connect 一次，以查看已上傳二進位檔。 移至您的應用程式設定 頁面，然後選擇**發行前版本**從頂端功能表中看到**建置**清單：

  [![](appstore-images/itc-prerelease-sml.png "在 iTunes Connect 中的 [應用程式組態] 頁面")](appstore-images/itc-prerelease.png#lightbox)

您就可以提交核准的應用程式上**版本**頁面。 請參閱[iOS 應用程式發佈概觀](~/ios/deploy-test/app-distribution/index.md)如需詳細資訊。


## <a name="troubleshooting"></a>疑難排解

以下是一些時送出應用程式市集，並加以修正時可採取的步驟可能會遇到的錯誤。

### <a name="archive-menu-option-is-not-visible-in-visual-studio-for-mac"></a>適用於 Mac 的 Visual Studio 中看不到 [封存] 功能表選項

請遵循[上述步驟](#xamarin_studio)設定封存方案。 如果您無法正確地設定啟始專案，請確定組建組態先設為偵錯或發行之前嘗試變更啟始專案。 然後設定組建組態回**AppStore**。

### <a name="invalid-icon"></a>無效圖示

```csharp
Invalid Icon - The watch application '...watchkitextension.appex/WatchApp.app'
contains an icon file '...watchkitextension.appex/WatchApp.app/AppIcons27.5x27.5@2x.png'
with an alpha channel. Icons should not have an alpha channel.
```

請遵循[指示移除 alpha 色板](~/ios/watchos/troubleshooting.md)從您的圖示。

### <a name="cfbundleversion-mismatch"></a>CFBundleVersion 不相符

```csharp
CFBundleVersion Mismatch. The CFBundleVersion value '1' of watch application
'...watchkitextension.appex/WatchApp.app' does not match the CFBundleVersion
value '1.0' of its containing iOS application `YouriOS.app`.
```

IOS 應用程式、 Watch 擴充功能和監看式應用程式為您方案中的所有專案都應該都使用相同的版本號碼。 編輯每**Info.plist**檔案，所以完全符合的版本號碼。

### <a name="missing-icons"></a>缺少圖示

```csharp
Missing Icons. No icons found for watch application '...watchkitextension.appex/WatchApp.app'.
Please make sure that its Info.plist file includes entries for CFBundleIconFiles.
```

請依照下列中的指示[使用圖示](~/ios/watchos/app-fundamentals/icons.md)監看式應用程式專案加入所有必要的映像。

### <a name="missing-icon"></a>遺失的圖示

```csharp
Missing Icon. The watch application '...watchkitextension.appex/WatchApp.app'
is missing icon with name pattern '*44x44@2x.png' (Home Screen 42mm).
```

請確定您有最新版本的 Visual Studio for Mac，而且您**AppIcons.appiconset**包含一組完整的映像。 如果您仍然看到此錯誤，檢視的來源**Contents.json**以確認它包含所有必要的映像的項目。 或者，一旦您已確保您使用 Xamarin 的最新版本，刪除並重新建立**AppIcons.appiconset**。

> [!IMPORTANT]
> 沒有已知的錯誤在 Visual Studio 中適用於 Mac 的監看式 圖示的支援： 它會預期 88 x 88 像素影像**29x29@3x**映像 （這應該是 87 x 87 像素為單位）。


您無法在 Visual Studio 中修正此問題適用於 Mac 的影像資產在 Xcode 中編輯，或是手動編輯**Contents.json**檔案 (以符合[這個範例](https://github.com/xamarin/monotouch-samples/blob/master/WatchKit/WatchKitCatalog/WatchApp/Resources/Images.xcassets/AppIcons.appiconset/Contents.json#L126-L132))。



### <a name="invalid-watchkit-support"></a>無效的 WatchKit 支援

```csharp
Invalid WatchKit Support - The bundle contains an invalid implementation of WatchKit.
The app may have been built or signed with non-compliant or pre-release tools.
```

此訊息可能出現看似成功上傳之後，從 iTunes Connect 期間驗證和送出，或在自動化的電子郵件。
<!--
Ensure you are using the latest version of Xcode and Xamarin's tools.
-->
> [!IMPORTANT]
> 您必須**封存**Visual Studio for Mac 後再切換至 Xcode 6.2 + 驗證並上傳至 iTunes Connect 中的應用程式。


使用穩定 Xamarin 通道和 Xcode 6.2 +。



### <a name="invalid-provisioning-profile"></a>無效的佈建設定檔

```csharp
Invalid Provisioning Profile. The provisioning profile included in the bundle
...iOSWatchApp.watchkitapp [iOSWatchApp.app/PlugIns/...iOSWatchApp.watchkitextension.appex/WatchApp.app]
is invalid. [Missing code-signing certificate.]
```

**發佈佈建的設定檔**必須提供監看式應用程式方案中的所有三個專案： iOS 應用程式、 監看式延伸模組，並監看式應用程式中-任一明確 （三個設定檔） 或透過單一萬用字元設定檔。 請檢查佈建設定檔是否存在於 iOS 開發人員中心，以及您已下載並新增至您的 mac

### <a name="invalid-code-signing-entitlements"></a>無效的程式碼簽署權利

```csharp
ITMS-90046: Invalid Code Signing Entitlements. Your application bundle's signature contains
code signing entitlements that are not supported on iOS. Specifically, value
'...watchkitextension' for key 'application-identifier' in '...watchkitextension'
is not supported. The value should be a string startign with your TEAMID, followed
by a dot '.' followed by the bundle identifier.
```

確定您的佈建設定檔會在 Apple 開發人員中心中上正確, 安裝，且您已下載並安裝它們。 也請檢查 Visual Studio 中設定每個專案 Mac 的 [屬性] 視窗。

### <a name="invalid-architecture"></a>不正確的結構

```csharp
Invalid architecture: Apps that include an app extension
and framework must support arm64.
```

您只能加入監看式應用程式[統一的 API （64 位元）](~/cross-platform/macios/unified/index.md) Xamarin.iOS 應用程式。
在 iOS 應用程式的專案上按一下滑鼠右鍵，然後移至**選項 > 建置 > iOS 組建 > 進階 索引標籤**，並確定**支援架構**AppStore iphone 組態包括**ARM64** （例如。 **ARMv7 + ARM64**)。

### <a name="this-bundle-is-invalid"></a>這個套件組合無效。

```csharp
ITMS-90068: This bundle is invalid. The value provided for the key
MinimumOSVersion '8.3' is not acceptable.
```

父 iOS 應用程式必須設定為 '8.2' 或更早 MinimumOSVersion。

### <a name="non-public-api-usage"></a>非公用 API 使用方式

```csharp
Your app contains non-public API usage.
Please review the errors, and resubmit your application.
```

確認您使用最新版本 Xcode 和 Xamarin 的工具。
您的程式碼不應該存取任何非公用 Api。

### <a name="build-error-mt5309"></a>建置錯誤 MT5309

```csharp
Error MT5309: Native linking error: clang: error: no such file or directory:
```

這個錯誤可能是您需要重新命名您的 Xcode 安裝從結果**Xcode.app**。 比方說，如果您重新命名您的安裝以將會發生此錯誤**XCode 6.2.app**。



## <a name="related-links"></a>相關連結

- [Apple WatchKit 提交指南](https://developer.apple.com/app-store/watch/)
