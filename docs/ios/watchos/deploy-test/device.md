---
title: 在 Apple Watch 裝置上測試
description: 本文件說明如何部署使用 Xamarin 建置實際的 Apple Watch 上測試 watchOS 應用程式。 它討論佈建設定檔，測試的裝置，並提供疑難排解秘訣。
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: a960d81d41ff127fa3316e6190dfbf4881305c02
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34790857"
---
# <a name="testing-on-apple-watch-devices"></a>在 Apple Watch 裝置上測試

瀏覽過之後[部署步驟](~/ios/watchos/deploy-test/index.md)若要建立應用程式識別碼和應用程式群組 （如有必要），請使用此頁面上的指示：

- [設定您的裝置](#devices)Apple 開發人員中心，並
- [建立佈建設定檔開發](#profiles)，然後
- [部署和測試](#testing)Apple Watch 上。

<a name="devices" />

## <a name="devices"></a>裝置

測試上實際的 iPhone 或 iPad 的 iOS 應用程式永遠要求開發人員中心註冊裝置。 裝置清單中看起來像這樣 (按一下加號**+** 新增新的裝置):

![](device-images/devices-sml.png "裝置清單中看起來像這樣")

監看並無不同-您現在需要加入您的 Apple Watch 裝置再將應用程式部署至它。 尋找 監看式 UDID 使用**Xcode** (**Windows > 裝置**清單)。 配對的電話連線時也會顯示監看式的資訊：

[![](device-images/xcode-devices-sml.png "配對的監看式資訊")](device-images/xcode-devices.png#lightbox)

如果您知道監看式 UDID，將它新增至裝置清單中，開發人員中心：

![](device-images/devices-watch-sml.png "監看式 UDID 在裝置清單")

一旦加入監看式裝置，請確定選取的任何新的或現有的程式開發或臨機操作您所建立的佈建設定檔中：

![](device-images/devices-provisioning.png "可用的裝置清單")

別忘了是否您編輯現有的佈建設定檔下載並重新安裝它 ！

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>開發佈建設定檔

若要進行測試，您必須建立在裝置上建置**開發佈建設定檔**方案中每個應用程式識別碼。

如果您有應用程式識別碼，萬用字元*只能有一個佈建設定檔將需要*; 但如果您有個別的應用程式識別碼，每個專案，則您必須針對每個應用程式識別碼佈建設定檔：

![](device-images/provisioningprofile-development.png "開發佈建設定檔")

一旦您已建立所有的三個設定檔，即會顯示在清單中。 請記得要下載並安裝每個：

![](device-images/provisioningprofiles.png "可用開發佈建的設定檔")

您可以在佈建設定檔來確認**專案選項**選取**建置 > iOS 套件組合簽署**螢幕，然後選取**發行**或**偵錯 iPhone**組態。

**佈建設定檔**清單會顯示所有相符的設定檔-您應該會看到您已建立此下拉式清單中的比對設定檔：

![](device-images/options-selectprofile.png "佈建設定檔清單")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>在 監看式裝置上測試

一旦您已設定您的裝置、 應用程式識別碼，並佈建的設定檔，您已準備進行測試。

1. 請確定您的 iPhone 已經插入電腦，且監看式已經與 iPhone 配對。

2. 請確定組態設為**發行**或**偵錯**。

3. 確定已連接的 iPhone 裝置已在目標清單中選取。

4. 在 iOS 應用程式專案 （而非從監看式或延伸模組） 上按一下滑鼠右鍵，然後選擇 **設定為啟始專案**。

5. 按一下**執行**按鈕 (或選擇**啟動**選項**執行**功能表)。

6. 將建立方案和 iOS 應用程式將會部署至 iPhone。
  如果 iOS 應用程式或監看式佈建擴充功能未正確設定在 iPhone 的部署將會失敗。

7. 如果順利完成部署之後，在 iPhone 會自動嘗試將監看式應用程式傳送至配對的監看式。 您的應用程式的圖示會出現含有循環的 [監看式] 畫面上*安裝*進度列指示器。

8. 監看式應用程式已成功安裝，如果圖示仍然會留在 [監看式] 畫面上的觸控它開始測試您的應用程式 ！


## <a name="troubleshooting"></a>疑難排解

如果在部署使用期間發生錯誤**檢視 > 填補 > 裝置記錄**查看有關錯誤的詳細資訊。 某些錯誤和其原因如下：

### <a name="error-mt3001-could-not-aot-the-assembly"></a>錯誤 MT3001： 無法 AOT 組件

建置偵錯模式，將部署至 Apple Watch 裝置時，也可能會發生。

若要*暫時*暫時解決此問題，請停用**累加建置**Watch 擴充功能中**專案選項 > 建置 > watchOS 組建**視窗：

[![](device-images/disable-incremental-sml.png "累加建置的核取方塊")](device-images/disable-incremental.png#lightbox)

這將在未來的版本中，其後累加建置可以重新啟用利用建置時間更短的修正。


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>監看式應用程式無法在裝置上偵錯時啟動

當嘗試進行偵錯只圖示 （& s） 載入微調在實體裝置上的監看式應用程式會出現 （與最終逾時）。 這將會解決未來的版本;因應措施是執行 （而不會允許偵錯） 的發行組建。


### <a name="invalid-application-executable-or-application-verification-failed"></a>無效的應用程式可執行檔或應用程式驗證失敗

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "無效的應用程式可執行檔警示")

如果會出現這些訊息*監看畫面上*應用程式已嘗試安裝之後，可能有幾個問題：

- 尚未以 Apple 開發人員中心上的裝置加入監看式裝置本身。 依照指示[正確設定裝置](#devices)。

- 用於測試的開發佈建設定檔沒有包含; 監看式裝置或者，監看式已加入佈建設定檔之後未重新下載，重新安裝。 依照指示[正確設定佈建設定檔](#profiles)。

- 如果**iOS 裝置記錄**包含`The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3`然後監看式應用程式的**Info.plist**錯誤**MinimumOSVersion**值。
  這應該是**8.2** -如果您已安裝 Xcode 6.3，您可能需要手動編輯來源插入將它設定為 8.2。

- 監看式應用程式的**Entitlements.plist**不正確的權限啟用 （例如應用程式群組） 不應該有。

- 監看式應用程式的**應用程式識別碼**不正確，它不應該有開發人員中心有權利 （例如應用程式群組） 已啟用。



### <a name="install-never-finished"></a>永遠不會安裝已完成

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

這個錯誤可能表示不需要 （及無效） 的監看式應用程式中的索引鍵**Info.plist**檔案。 您不應該包含適用於 iOS 的應用程式或監看式擴充功能，監看式應用程式中的索引鍵。

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>「 正在等候偵錯工具要連接 」

如果**應用程式輸出**視窗遇顯示

```csharp
waiting for debugger to connect
```

檢查是否 NuGets 已包含在專案中的任何有相依性**Microsoft.Bcl.Build**。 這會自動加入一些 Microsoft 發行的程式庫包括熱門[Microsoft Http Client Libraries](http://www.nuget.org/packages/Microsoft.Net.Http/)。

**Microsoft.Bcl.Build.targets**檔案加入至 **.csproj** iOS 擴充功能的封裝，部署期間可能會妨礙。 您可以追蹤[bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912)。
可能的解決方法是編輯.csproj 檔案，並以手動方式移動**Microsoft.Bcl.Build.targets**是最後一個項目。

