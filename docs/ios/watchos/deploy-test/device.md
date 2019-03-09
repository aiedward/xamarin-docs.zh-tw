---
title: 在 Apple Watch 裝置上測試
description: 本文件說明如何部署在實際的 Apple Watch 上進行測試以 Xamarin 建置 watchOS 應用程式。 它討論佈建設定檔，測試的裝置，並提供疑難排解秘訣。
ms.prod: xamarin
ms.assetid: A72A7D38-FAE8-4DD2-843D-54B74C5078D7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 9c15e9205b96a02caa182e47b71c6d36c8bff1aa
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671048"
---
# <a name="testing-on-apple-watch-devices"></a>在 Apple Watch 裝置上測試

您已遵循之後[部署步驟](~/ios/watchos/deploy-test/index.md)若要建立應用程式識別碼和應用程式群組 （如有必要），請使用此頁面上的指示：

- [設定您的裝置](#devices)Apple 開發人員中心，以及
- [建立佈建設定檔的開發](#profiles)，然後
- [部署和測試](#testing)Apple Watch 上。

<a name="devices" />

## <a name="devices"></a>裝置

測試的實際的 iPhone 或 iPad 的 iOS 應用程式時，一律需要開發人員中心註冊裝置。 裝置清單中看起來像這樣 (按一下加號**+** 以新增新的裝置):

![](device-images/devices-sml.png "裝置清單中看起來像這樣")

監看式並無不同-您現在需要之前要加入您的 Apple Watch 裝置應用程式部署到它。 尋找使用 監看的 UDID **Xcode** (**Windows > 裝置**清單)。 配對的行動電話連線時也會顯示監看式的資訊：

[![](device-images/xcode-devices-sml.png "配對的監看式資訊")](device-images/xcode-devices.png#lightbox)

當您知道手錶的 UDID，將它新增至裝置清單中，開發人員中心：

![](device-images/devices-watch-sml.png "監看式的裝置清單中的 UDID")

一旦新增監看式裝置之後，請確定已選取任何新的或現有的開發或您所建立的臨機操作佈建設定檔中：

![](device-images/devices-provisioning.png "可用的裝置清單")

別忘了是否您編輯現有的佈建設定檔下載，並重新安裝它 ！

<a name="profiles" />

## <a name="development-provisioning-profiles"></a>開發佈建設定檔

若要建置在您要建立的裝置上進行測試**開發的佈建設定檔**方案中每個應用程式識別碼。

如果您有萬用字元應用程式識別碼*只有一個的佈建設定檔必須*; 但如果您有個別的應用程式識別碼，針對每個專案，則您必須針對每個應用程式識別碼佈建設定檔：

![](device-images/provisioningprofile-development.png "開發佈建設定檔")

一旦您已建立所有的三個設定檔，即會顯示在清單中。 請務必下載並安裝每一項：

![](device-images/provisioningprofiles.png "可用的開發佈建設定檔")

您可以在佈建設定檔，確認**專案選項**藉由選取**建置 > iOS 套件組合簽署**畫面，然後選取**版本**或**偵錯 iPhone**組態。

**佈建設定檔**清單會顯示所有相符的設定檔-您應該會看到您已在此下拉式清單中的比對設定檔：

![](device-images/options-selectprofile.png "佈建設定檔清單")


<a name="testing" />

## <a name="testing-on-a-watch-device"></a>在 監看式裝置上測試

一旦您已設定您的裝置、 應用程式識別碼和佈建設定檔，您就可以開始測試。

1. 請確定您的 iPhone 已經插入電腦，並監看式已經配對的 iPhone 與。

2. 請確定組態設為**Release**或是**偵錯**。

3. 請確定 [目標] 清單中選取連線的 iPhone 裝置。

4. 以滑鼠右鍵按一下 iOS 應用程式專案 （而非從監看式或擴充功能），然後選擇 **設定為啟始專案**。

5. 按一下 [**執行**] 按鈕 (或選擇**開始**選項**執行**功能表)。

6. 解決方案會建置和 iOS 應用程式將會部署至 iPhone。
  如果 iOS 應用程式或監看式延伸模組佈建未正確設定為 iPhone 的部署會失敗。

7. 如果順利完成部署，iPhone 會自動嘗試將 watch 應用程式傳送至配對的監看式。 您的應用程式圖示會出現在 [監看式] 畫面使用循環*安裝*進度列指示器。

8. Watch 應用程式已成功安裝，如果圖示仍然會留在 [監看式] 畫面中-觸控以開始測試您的應用程式 ！


## <a name="troubleshooting"></a>疑難排解

如果部署使用期間發生錯誤**檢視 > 板 > 裝置記錄檔**以查看錯誤的詳細資訊。 以下列出一些錯誤和其原因：

### <a name="error-mt3001-could-not-aot-the-assembly"></a>錯誤 MT3001:無法 AOT 的組件

若要部署到 Apple Watch 裝置的偵錯模式中建置時，也可能會發生。

若要*暫時*暫時解決此問題，請停用**累加建置**監看式延伸模組中**專案選項 > 建置 > watchOS 組建**視窗：

[![](device-images/disable-incremental-sml.png "累加建置 核取方塊")](device-images/disable-incremental.png#lightbox)

這將會在未來的版本中之後, 累加建置可以重新啟用，以利用更快速的組建時間修正。


### <a name="watch-app-fails-to-start-while-debugging-on-device"></a>Watch 應用程式無法在裝置上偵錯時啟動

當嘗試將偵錯僅圖示及載入進度環在實體裝置上的監看式應用程式會出現 （和最終逾時）。 這將會解決在未來的版本;解決方法是執行 （它不允許偵錯） 的發行組建。


### <a name="invalid-application-executable-or-application-verification-failed"></a>無效的應用程式可執行檔或應用程式驗證失敗

```csharp
Failed to install [APPNAME]
Invalid executable/Application Verification Failed
```

![](device-images/invalid-application-executable.png "無效的應用程式可執行檔警示")

如果這些訊息會出現*監看畫面上*應用程式已嘗試安裝之後，可能有幾個問題：

- 為 Apple 開發人員中心上的裝置尚未加入監看式裝置本身。 請依照下列指示來[正確設定裝置](#devices)。

- 用於測試的開發佈建設定檔沒有包含; 監看式裝置或監看式新增至佈建設定檔之後就沒有重新下載，並重新安裝。 請依照下列指示來[正確設定佈建設定檔](#profiles)。

- 如果**iOS 裝置記錄檔**包含`The system version is lower than the minimum OS version specified for bundle...Have 8.2; need 8.3`然後監看式應用程式的**Info.plist**錯誤**MinimumOSVersion**值。
  這應該是**8.2** -如果您已安裝 Xcode 6.3，您可能需要手動編輯來源，以插入將它設定為 8.2。

- Watch 應用程式**Entitlements.plist**不當權利啟用 （例如應用程式群組），就不能。

- Watch 應用程式**應用程式識別碼**錯誤就不能在開發人員中心中已啟用 （例如應用程式群組） 的權利。



### <a name="install-never-finished"></a>永遠不會安裝已完成

```csharp
SPErrorGizmoInstallNeverFinishedErrorMessage
```

此錯誤可能表示監看式應用程式中的不必要的 （和無效的） 索引鍵**Info.plist**檔案。 您不應包含適用於 iOS 應用程式或監看式延伸模組，監看式應用程式中的索引鍵。

<!--eg. NSLocationAlwaysUsageDescription -->


### <a name="waiting-for-debugger-to-connect"></a>「 正在等候偵錯工具連線 」

如果**應用程式輸出**視窗卡住時顯示

```csharp
waiting for debugger to connect
```

檢查是否有任何的專案中所包含的 Nuget 的相依性**Microsoft.Bcl.Build**。 這會自動新增某些 Microsoft 發佈的程式庫包括熱門[Microsoft Http Client Libraries](https://www.nuget.org/packages/Microsoft.Net.Http/)。

**Microsoft.Bcl.Build.targets**檔案加入至 **.csproj** iOS 延伸模組的封裝，在部署期間可能會妨礙。 您可以追蹤[bug](https://bugzilla.xamarin.com/show_bug.cgi?id=29912)。
可能的解決方法是編輯.csproj 檔案，並以手動方式移動**Microsoft.Bcl.Build.targets**是最後一個項目。

