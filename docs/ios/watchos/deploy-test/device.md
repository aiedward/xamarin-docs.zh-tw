---
title: 在 Apple Watch 裝置上進行測試
description: 本檔說明如何部署以 Xamarin 建立的 watchOS 應用程式，以在實際 Apple Watch 上進行測試。 其中討論裝置、布建設定檔、測試，並提供一些疑難排解秘訣。
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: a1ff70349ca7d7b97431b2e3d07aa4c8028c38c0
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86996353"
---
# <a name="testing-on-apple-watch-devices"></a>在 Apple Watch 裝置上進行測試

依照[部署步驟](~/ios/watchos/deploy-test/index.md)來建立應用程式識別碼和應用程式群組（如有必要）之後，請使用此頁面上的指示來執行下列動作：

- 在 Apple 開發人員中心[設定您的裝置](#devices)，以及
- [建立開發提供設定檔](#profiles)，然後
- 在 Apple Watch 上[部署和測試](#testing)。

<a name="devices"></a>

## <a name="devices"></a>裝置

在實際的 iPhone 或 iPad 上測試 iOS 應用程式，一律需要在開發人員中心註冊裝置。 裝置清單看起來像這樣（按一下加號以新增 **+** 裝置）：

![裝置清單看起來像這樣](device-images/devices-sml.png)

監看式並無不同-您現在需要先新增您的 Apple Watch 裝置，然後再將應用程式部署到其中。 使用**Xcode**尋找監看式的 UDID （**Windows > 裝置**清單）。 當配對的電話連接時，也會顯示監看式的資訊：

[![配對的監看資訊](device-images/xcode-devices-sml.png)](device-images/xcode-devices.png#lightbox)

當您知道監看式的 UDID 時，請將它新增至開發人員中心的裝置清單：

![裝置清單中的監看式 UDID](device-images/devices-watch-sml.png)

新增 Watch 裝置之後，請確定已在您所建立的任何新的或現有的開發或臨機操作布建設定檔中選取它：

![可用的裝置清單](device-images/devices-provisioning.png)

如果您編輯現有的布建設定檔以下載並重新安裝，請別忘了！

<a name="profiles"></a>

## <a name="development-provisioning-profiles"></a>開發布建設定檔

若要在您的裝置上進行測試，您必須為解決方案中的每個應用程式識別碼建立開發布建**設定檔**。

如果您有萬用字元應用程式識別碼，則*只需要一個布建設定檔*;但如果您的每個專案都有個別的應用程式識別碼，則您需要每個應用程式識別碼的布建設定檔：

![開發布建設定檔](device-images/provisioningprofile-development.png)

一旦您建立了這三個設定檔，它們就會出現在清單中。 請記得下載並安裝每一個：

![可用的開發布建設定檔](device-images/provisioningprofiles.png)

您可以選取 [**組建 > [iOS**套件組合簽署] 畫面，然後選取 [**發行**] 或 [ **Debug] IPhone**設定，以確認**專案選項**中的布建設定檔。

[布建**設定檔**] 清單會顯示所有相符的設定檔，您應該會看到您在此下拉式清單中建立的相符設定檔：

![布建配置檔案清單](device-images/options-selectprofile.png)

<a name="testing"></a>

## <a name="testing-on-a-watch-device"></a>在監看式裝置上測試

一旦您設定裝置、應用程式識別碼和布建設定檔之後，就可以開始進行測試。

1. 請確定您的 iPhone 已插入，且 Watch 已與 iPhone 配對。

2. 請確定設定為 [**發行**] 或 [ **Debug**]。

3. 確定已在 [目標] 清單中選取 [已連線的 iPhone 裝置]。

4. 以滑鼠右鍵按一下 iOS 應用程式專案（不是 [監看式] 或 [延伸模組]），然後選擇 [**設定為啟始專案**]。

5. 按一下 [**執行**] 按鈕（或從 [**執行**] 功能表中選擇 [**啟動**] 選項）。

6. 解決方案將會建立，且 iOS 應用程式將會部署到 iPhone。
  如果未正確設定 iOS 應用程式或監看延伸模組布建，則部署至 iPhone 將會失敗。

7. 如果部署順利完成，iPhone 會自動嘗試將監看式應用程式傳送至配對的監看式。 您的應用程式圖示會出現在 [監看式] 畫面上，並有迴圈*安裝*進度指示器。

8. 如果順利安裝 watch 應用程式，圖示將會保留在 [監看式] 畫面上，並將其觸控以開始測試您的應用程式！

## <a name="troubleshooting"></a>疑難排解

如果部署期間發生錯誤，請使用**View > pad > 裝置記錄**檔，以查看錯誤的詳細資訊。 某些錯誤及其原因如下所列：

### <a name="error-mt3001-could-not-aot-the-assembly"></a>錯誤 MT3001：無法對元件進行 AOT

在以 DEBUG 模式建立以部署至 Apple Watch 裝置時，可能會發生這種情況。

若要*暫時*解決此問題，請停用 [監看式擴充功能專案選項] 中的 [累加**組建**] **> 組建 > watchOS 組建**] 視窗：

[![[增量組建] 核取方塊](device-images/disable-incremental-sml.png)](device-images/disable-incremental.png#lightbox)

這會在未來的版本中修正，之後可以重新啟用累加式組建，以利用更快速的組建時間。

### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>在裝置上進行偵錯工具時，監看式應用程式無法啟動

嘗試在實體裝置上進行監看式應用程式的偵測時，只會顯示 & 載入] 微調按鈕的圖示（最後是超時）。 這會在未來的版本中解決;因應措施是執行發行組建（這不允許進行調試）。

### <a name="invalid-application-executable-or-application-verification-failed"></a>不正確應用程式可執行檔或應用程式驗證失敗

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![應用程式可執行檔警示無效](device-images/invalid-application-executable.png)

如果在應用程式嘗試安裝之後，這些訊息會出現*在監看式畫面上*，可能會有幾個問題：

- Watch 裝置本身尚未新增為 Apple 開發人員中心上的裝置。 請遵循指示來[正確設定裝置](#devices)。

- 用於測試的開發布建設定檔未包含監看裝置;或者，將 Watch 新增至布建設定檔之後，就不會重新下載並重新安裝它們。 請遵循指示來[正確設定布建設定檔](#profiles)。

- 如果**IOS 裝置記錄**檔包含， `The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3` 則監看式應用程式的**Info. Plist**具有錯誤的**MinimumOSVersion**值。
  這應該是**8.2** -如果您已安裝 Xcode 6.3，可能需要手動編輯來源以將其設定為8.2。

- Watch 應用程式的**權利。 plist**不正確地啟用了不應具備的權利（例如應用程式群組）。

- Watch 應用程式的**應用程式識別碼**不正確地在開發人員中心中啟用了權利（例如應用程式群組）。

### <a name="install-never-finished"></a>安裝從未完成

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

此錯誤可能表示監看式應用程式的**plist**檔案中不必要（也無效）金鑰。 您不應該在監看式應用程式中包含適用于 iOS 應用程式或監看式擴充功能的金鑰。

<!--eg. NSLocationAlwaysUsageDescription -->

### <a name="waiting-for-debugger-to-connect"></a>「正在等候偵錯工具連接」

如果 [**應用程式輸出**] 視窗停滯顯示

```csharp
waiting for debugger to connect
```

檢查您的專案中是否有任何已包含的 Nuget 相依于**Microsoft. Bcl. 建立**。 這會自動新增一些 Microsoft 發行的程式庫，包括熱門的[Microsoft Http 用戶端程式庫](https://www.nuget.org/packages/Microsoft.Net.Http/)。

新增至 **.csproj**的 **.targets**檔案可能會在部署期間干擾 iOS 延伸模組的封裝。 您可以追蹤[bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912)。
可能的因應措施是編輯 .csproj 檔案，並以手動方式將 **.targets**移動為最後一個元素。
