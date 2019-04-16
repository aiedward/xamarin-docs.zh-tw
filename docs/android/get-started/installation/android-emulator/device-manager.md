---
title: 使用 Android Device Manager 管理虛擬裝置
description: 本文將說明如何使用 Android Device Manager 建立和設定可模擬實體 Android 裝置的 Android 虛擬裝置 (AVD)。 您可以使用這些虛擬裝置來執行和測試應用程式，而不必依賴實體裝置。
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: 3dad36dc93f5c23e5d9ef1e05a1b9419b7a724b6
ms.sourcegitcommit: e7f27ba75cae5099ef053b819b84132a77d4f9e7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2019
ms.locfileid: "58855103"
---
# <a name="managing-virtual-devices-with-the-android-device-manager"></a>使用 Android Device Manager 管理虛擬裝置

_本文說明如何使用 Android Device Manager 建立和設定可模擬實體 Android 裝置的 Android 虛擬裝置 (AVD)。您可以使用這些虛擬裝置來執行和測試應用程式，而不必依賴實體裝置。_

當您確認已啟用硬體加速之後 (如[硬體加速以提升模擬器效能](~/android/get-started/installation/android-emulator/hardware-acceleration.md)中所述)，下一個步驟是使用 _Android Device Manager_ (另請參閱 _Xamarin Android Device Manager_) 建立可用於測試和偵錯應用程式的虛擬裝置。

::: zone pivot="windows"

## <a name="android-device-manager-on-windows"></a>Windows 上的 Android Device Manager

本指南會說明如何使用 Android Device Manager 建立、複製、自訂和啟動 Android 虛擬裝置。

[![[裝置] 索引標籤中 Android Device Manager 的螢幕擷取畫面](device-manager-images/win/01-devices-dialog-sml.png)](device-manager-images/win/01-devices-dialog.png#lightbox)

您會使用 Android Device Manager，來建立和設定在 [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) 中執行的 _Android 虛擬裝置_ (AVD)。
每個 AVD 都是可模擬實體 Android 裝置的模擬器組態。 這讓您能夠在模擬不同實體 Android 裝置的各種組態中，執行並測試應用程式。

## <a name="requirements"></a>需求

若要使用 Android Device Manager，您需要下列項目：

- Visual Studio 2019 Community、Professional 或 Enterprise。

- 或者需要 Visual Studio 2017 15.8 版或更新版本。 支援 Visual Studio Community、Professional 和 Enterprise 版本。

- Visual Studio Tools for Xamarin 4.9 版或更新版本。

- 必須安裝 Android SDK (請參閱[設定 Xamarin.Android 的 Android SDK](~/android/get-started/installation/android-sdk.md))。
  務必將 Android SDK 安裝在其預設位置 (若尚未安裝)：**C:\\Program Files (x86)\\Android\\android-sdk**。

- 必須安裝下列套件 (透過 [Android SDK 管理員](~/android/get-started/installation/android-sdk.md))： 
    - **Android SDK Tools 26.1.1 版**或更新版本
    - **Android SDK 平台工具 27.0.1** 或更新版本
    - **Android SDK 建置工具 27.0.3** 或更新版本 
    - **Android Emulator 27.2.7** 或更新版本。 

  這些套件應該會以 [已安裝] 狀態顯示，如下列螢幕擷取畫面所示：

  [![安裝 Android SDK Tools](device-manager-images/win/02-sdk-tools-sml.png)](device-manager-images/win/02-sdk-tools.png#lightbox)


## <a name="launching-the-device-manager"></a>啟動裝置管理員

按一下 [工具] > [Android] > [Android Device Manager]，從 [工具] 功能表啟動 Android Device Manager：

[![從 [工具] 功能表啟動 Device Manager](device-manager-images/win/03-tools-menu-sml.png)](device-manager-images/win/03-tools-menu.png#lightbox)

如果啟動時出現下列錯誤對話方塊，請參閱[疑難排解](#troubleshooting)一節來取得因應措施的指示：

![Android SDK 執行個體錯誤對話方塊](device-manager-images/win/04-sdk-error.png)


## <a name="main-screen"></a>主畫面

當您第一次啟動 Android 裝置管理員時，它所呈現的畫面會顯示所有目前設定的虛擬裝置。 針對每部虛擬裝置，都會顯示**名稱**、**OS** (Android 版本)、**處理器**、**記憶體**大小及螢幕**解析度**：

[![已安裝之裝置及其參數的清單](device-manager-images/win/05-installed-list-sml.png)](device-manager-images/win/05-installed-list.png#lightbox)

當您選取清單中的裝置時，會在右邊出現 [啟動] 按鈕。 您可以按一下 [啟動] 按鈕，使用此虛擬裝置來啟動模擬器：

[![裝置映像的 [啟動] 按鈕](device-manager-images/win/06-start-button-sml.png)](device-manager-images/win/06-start-button.png#lightbox)

當模擬器使用所選取的虛擬裝置啟動之後，[啟動] 按鈕就會變成 [停止] 按鈕，讓您可用來暫止模擬器：

[![適用於執行中裝置的 [停止] 按鈕](device-manager-images/win/07-stop-button-sml.png)](device-manager-images/win/07-stop-button.png#lightbox)

### <a name="new-device"></a>新裝置

若要建立新裝置，按一下 [新增] 按鈕 (位於畫面的右上方區域)：

[![建立新裝置的 [新增] 按鈕](device-manager-images/win/08-new-button-sml.png)](device-manager-images/win/08-new-button.png#lightbox)

按一下 [新增] 會啟動 [新裝置] 畫面：

[![裝置管理員的 [新裝置] 畫面](device-manager-images/win/09-new-device-editor-sml.png)](device-manager-images/win/09-new-device-editor.png#lightbox)

若要在 [新裝置] 畫面中設定新裝置，請使用下列步驟：

1. 指定裝置的新名稱。 在下列範例中，會將新裝置命名為 **Pixel_API_27**：

   [![為新裝置命名](device-manager-images/win/10-device-name-sml.png)](device-manager-images/win/10-device-name.png#lightbox)

2. 按一下 [基底裝置] 下拉式功能表來選取要模擬的實體裝置：

   [![選取要模擬的實體裝置](device-manager-images/win/11-device-menu-sml.png)](device-manager-images/win/11-device-menu.png#lightbox)

3. 按一下 [處理器] 下拉式功能表來選取此虛擬裝置的處理器類型。 選取 **x86** 會提供最佳效能，因為該選項可讓模擬器利用[硬體加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md)功能。
   **x86_64** 選項也會使用硬體加速功能，但執行速度比 **x86** 稍微慢一點 (**x86_64** 通常用於測試 64 位元應用程式)：

   [![選取處理器類型](device-manager-images/win/12-processor-type-menu-sml.png)](device-manager-images/win/12-processor-type-menu.png#lightbox)

4. 按一下 [OS] 下拉式功能表來選取 Android 版本 (API 層級)。 例如，選取 **Oreo 8.1 - API 27** 以建立適用於 API 層級 27 的虛擬裝置：

   [![選取 Android 版本](device-manager-images/win/13-android-version-w158-sml.png)](device-manager-images/win/13-android-version-w158.png#lightbox)

   如果您選取尚未安裝的 Android API 層級，Device Manager 會在畫面底部顯示 [A new device image will be downloaded] \(即將下載新的裝置映像\) 訊息 &ndash; 它會在建立新的虛擬裝置時下載並安裝必要檔案：

   ![即將下載新的裝置映像](device-manager-images/win/14-automatic-download-w158.png)

5. 如果您想要在虛擬裝置中包含 Google Play Services API，請啟用 [Google API] 選項。 若要包含 Google Play 商店應用程式，請啟用 [Google Play 商店] 選項：

   [![選取 Google Play Services 和 Google Play 商店](device-manager-images/win/15-google-play-services-sml.png)](device-manager-images/win/15-google-play-services.png#lightbox)

   請注意，Google Play 商店影像僅適用於某些基底裝置類型，例如 Pixel、Pixel 2、Nexus 5 和 Nexus 5X。

6. 編輯您需要修改的任何屬性。 若要變更內容，請參閱[編輯 Android 虛擬裝置內容](~/android/get-started/installation/android-emulator/device-properties.md)。

7. 新增您需要明確設定的任何其他屬性。 [新裝置] 畫面只會列出最常修改的屬性，但您可以按一下 [新增屬性] 下拉式功能表 (位於底部) 來新增其他屬性：

   [![[新增屬性] 下拉式功能表](device-manager-images/win/16-add-property-menu-sml.png)](device-manager-images/win/16-add-property-menu.png#lightbox)

    您也可以選取屬性清單頂端的 [自訂...] 來定義自訂屬性。

8. 按一下 [建立] 按鈕 (右下角) 來建立新裝置：

   [![[建立] 按鈕](device-manager-images/win/17-create-button-sml.png)](device-manager-images/win/17-create-button.png#lightbox)

9. 您可能會看見 [接受授權] 畫面。 如果您同意授權條款，按一下 [接受]：

   [![[接受授權] 畫面](device-manager-images/win/18-license-acceptance-sml.png)](device-manager-images/win/18-license-acceptance.png#lightbox)

10. Android Device Manager 會將新裝置新增至已安裝的虛擬裝置清單中，並在建立裝置期間顯示 [正在建立] 進度列指示器：

    [![[正在建立] 進度列指示器](device-manager-images/win/19-creating-the-device-sml.png)](device-manager-images/win/19-creating-the-device.png#lightbox)

11. 當建立程序完成時，即會在已安裝的虛擬裝置清單中顯示新裝置，並具有 [啟動] 按鈕，準備好可供啟動：

    [![準備好可供啟動的新建裝置](device-manager-images/win/20-created-device-sml.png)](device-manager-images/win/20-created-device.png#lightbox)


### <a name="edit-device"></a>編輯裝置

若要編輯現有的虛擬裝置，選取裝置，然後按一下 [編輯] 按鈕 (位於畫面右上角)：

[![用於修改裝置的 [編輯] 按鈕](device-manager-images/win/21-edit-button-sml.png)](device-manager-images/win/21-edit-button.png#lightbox)

按一下 [編輯]，會啟動適用於所選取虛擬裝置的裝置編輯器：

[![[裝置編輯器] 畫面](device-manager-images/win/22-device-editor-sml.png)](device-manager-images/win/22-device-editor.png#lightbox)

[裝置編輯器] 畫面會在 [屬性] 欄下方列出虛擬裝置的屬性，其中包含 [值] 欄中每個屬性的對應值。 當您選取屬性時，該屬性的詳細描述就會顯示在右邊。

若要變更屬性，請在 [值] 欄中編輯其值。
例如，在下列螢幕擷取畫面中，正在將 `hw.lcd.density` 屬性從 **480** 變更為 **240**：

[![裝置編輯範例](device-manager-images/win/23-device-editing-sml.png)](device-manager-images/win/23-device-editing.png#lightbox)

當您進行了必要的組態變更之後，按一下 [儲存] 按鈕。
如需變更虛擬裝置內容的詳細資訊，請參閱[編輯 Android 虛擬裝置內容](~/android/get-started/installation/android-emulator/device-properties.md)。


### <a name="additional-options"></a>其他選項

搭配裝置運作的其他選項都可從右上角的 [其他選項] (&hellip;) 下拉式功能表中取得：

[![[其他選項] 功能表的位置](device-manager-images/win/24-overflow-menu-sml.png)](device-manager-images/win/24-overflow-menu.png#lightbox)

[其他選項] 功能表包含下列項目：

- **複製和編輯** &ndash; 複製目前選取的裝置，並使用不同的唯一名稱，在 [新裝置] 畫面中開啟它。 例如，選取 **Pixel_API_27** 並按一下 [複製和編輯]，會將計數器附加到名稱：

  [![[複製和編輯] 畫面](device-manager-images/win/25-dupe-and-edit-sml.png)](device-manager-images/win/25-dupe-and-edit.png#lightbox)

- **在檔案總管中顯示** &ndash; 在保存適用於虛擬裝置之檔案的資料夾中開啟 [Windows 檔案總管] 視窗。 例如，選取 **Pixel_API_27** 並按一下 [在檔案總管中顯示]，會開啟類似下列範例的視窗：

  [![按一下 [在檔案總管中顯示] 的結果](device-manager-images/win/26-reveal-in-explorer-sml.png)](device-manager-images/win/26-reveal-in-explorer.png#lightbox)

- **重設為原廠設定** &ndash; 當裝置正在執行時，將選取的裝置重設為其預設設定，清除對其內部狀態所做的任何使用者變更 (這也會清除目前的[快速開機](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot)快照集，如果有的話)。 這項變更不會改變您在建立和編輯期間對虛擬裝置所做的修改。 隨即會出現一個對話方塊，提醒您此重設是無法復原的。 按一下 [恢復出廠預設值] 以確認重設：

  ![恢復出廠預設值對話方塊](device-manager-images/win/27-factory-reset.png)

- **刪除** &ndash; 永久刪除所選取的虛擬裝置。 隨即會出現一個對話方塊，提醒您刪除裝置是無法復原的。 如果您確定要刪除裝置，按一下 [刪除]。

  ![刪除裝置對話方塊](device-manager-images/win/28-delete-device-w158.png)


::: zone-end
::: zone pivot="macos"

## <a name="android-device-manager-on-macos"></a>macOS 上的 Android Device Manager

本指南會說明如何使用 Android Device Manager 建立、複製、自訂和啟動 Android 虛擬裝置。

[![[裝置] 索引標籤中 Android Device Manager 的螢幕擷取畫面](device-manager-images/mac/01-devices-dialog-sml.png)](device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> 本指南僅適用於 Visual Studio for Mac。
Xamarin Studio 與 Android Device Manager 不相容。

您會使用 Android Device Manager，來建立和設定在 [Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) 中執行的 *Android 虛擬裝置* (AVD)。
每個 AVD 都是可模擬實體 Android 裝置的模擬器組態。 這讓您能夠在模擬不同實體 Android 裝置的各種組態中，執行並測試應用程式。

## <a name="requirements"></a>需求

若要使用 Android Device Manager，您需要下列項目：

- Visual Studio for Mac 7.6 或更新版本。

- 必須安裝 Android SDK (請參閱[設定 Xamarin.Android 的 Android SDK](~/android/get-started/installation/android-sdk.md))。

- 必須安裝下列套件 (透過 [Android SDK 管理員](~/android/get-started/installation/android-sdk.md))： 
    -  **SDK Tools 26.1.1 版** 或更新版本
    -  **Android SDK 平台工具 28.0.1** 或更新版本 
    -  **Android SDK 建置工具 26.0.3** 或更新版本

  這些套件應該會以 [已安裝] 狀態顯示，如下列螢幕擷取畫面所示：

  [![安裝 Android SDK Tools](device-manager-images/mac/02-sdk-tools-sml.png)](device-manager-images/mac/02-sdk-tools.png#lightbox)


## <a name="launching-the-device-manager"></a>啟動裝置管理員

按一下 [工具] > [Device Manager] 啟動 Android Device Manager：

[![從 [工具] 功能表啟動 Device Manager](device-manager-images/mac/03-tools-menu-sml.png)](device-manager-images/mac/03-tools-menu.png#lightbox)

如果啟動時出現下列錯誤對話方塊，請參閱[疑難排解](#troubleshooting)一節來取得因應措施的指示：

![Android SDK 執行個體錯誤對話方塊](device-manager-images/mac/04-sdk-instance-error.png)


## <a name="main-screen"></a>主畫面

當您第一次啟動 Android 裝置管理員時，它所呈現的畫面會顯示所有目前設定的虛擬裝置。 針對每部虛擬裝置，都會顯示**名稱**、**OS** (Android 版本)、**處理器**、**記憶體**大小及螢幕**解析度**：

[![已安裝之裝置及其參數的清單](device-manager-images/mac/05-devices-list-sml.png)](device-manager-images/mac/05-devices-list.png#lightbox)

當您選取清單中的裝置時，會在右邊出現 [播放] 按鈕。 您可以按一下 [播放] 按鈕，使用此虛擬裝置來啟動模擬器：

[![裝置映像的 [播放] 按鈕](device-manager-images/mac/06-start-button-sml.png)](device-manager-images/mac/06-start-button.png#lightbox)

當模擬器使用所選取的虛擬裝置啟動之後，[播放] 按鈕就會變成 [停止] 按鈕，讓您可用來暫止模擬器：

[![適用於執行中裝置的 [停止] 按鈕](device-manager-images/mac/07-stop-button-sml.png)](device-manager-images/mac/07-stop-button.png#lightbox)

當您停止模擬器時，您可能會收到提示，詢問您是否要儲存目前狀態以供下次快速開機使用：

![儲存目前狀態以供快速開機使用對話方塊](device-manager-images/mac/08-save-for-quick-boot-m76.png)

儲存目前狀態可在此虛擬裝置再次啟動時加快模擬器的開機速度。 如需快速開機的詳細資訊，請參閱[快速開機](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot)。

### <a name="new-device"></a>新裝置

若要建立新的裝置，請按一下 [新增裝置] 按鈕 (位於畫面的左上方區域)：

[![建立新裝置的 [新增] 按鈕](device-manager-images/mac/09-new-button-sml.png)](device-manager-images/mac/09-new-button.png#lightbox)

按一下 [新裝置] 會啟動 [新裝置] 畫面：

[![裝置管理員的 [新裝置] 畫面](device-manager-images/mac/10-new-device-editor-sml.png)](device-manager-images/mac/10-new-device-editor.png#lightbox)

使用下列步驟，在 [新裝置] 畫面中設定新裝置：

1. 指定裝置的新名稱。 在下列範例中，會將新裝置命名為 **Pixel_API_27**：

   [![為新裝置命名](device-manager-images/mac/11-device-name-m76-sml.png)](device-manager-images/mac/11-device-name-m76.png#lightbox)

2. 按一下 [基底裝置] 下拉式功能表來選取要模擬的實體裝置：

   [![選取要模擬的實體裝置](device-manager-images/mac/12-device-menu-m76-sml.png)](device-manager-images/mac/12-device-menu-m76.png#lightbox)

3. 按一下 [處理器] 下拉式功能表來選取此虛擬裝置的處理器類型。 選取 **x86** 會提供最佳效能，因為該選項可讓模擬器利用[硬體加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md)功能。
   **x86_64** 選項也會使用硬體加速功能，但執行速度比 **x86** 稍微慢一點 (**x86_64** 通常用於測試 64 位元應用程式)：

   [![選取處理器類型](device-manager-images/mac/13-processor-type-menu-m76-sml.png)](device-manager-images/mac/13-processor-type-menu-m76.png#lightbox)

4. 按一下 [OS] 下拉式功能表來選取 Android 版本 (API 層級)。 例如，選取 **Oreo 8.1 - API 27** 以建立適用於 API 層級 27 的虛擬裝置：

   [![選取 Android 版本](device-manager-images/mac/14-android-screenshot-m76-sml.png)](device-manager-images/mac/14-android-screenshot-m76.png#lightbox)

   如果您選取尚未安裝的 Android API 層級，Device Manager 會在畫面底部顯示 [A new device image will be downloaded] \(即將下載新的裝置映像\) 訊息 &ndash; 它會在建立新的虛擬裝置時下載並安裝必要檔案：

   ![即將下載新的裝置映像](device-manager-images/mac/15-automatic-download-m76.png)

5. 如果您想要在虛擬裝置中包含 Google Play Services API，請啟用 [Google API] 選項。 若要包含 Google Play 商店應用程式，請啟用 [Google Play 商店] 選項：

   [![選取 Google Play Services 和 Google Play 商店](device-manager-images/mac/16-google-play-services-m76-sml.png)](device-manager-images/mac/16-google-play-services-m76.png#lightbox)

   請注意，Google Play 商店影像僅適用於某些基底裝置類型，例如 Pixel、Pixel 2、Nexus 5 和 Nexus 5X。

6. 編輯您需要修改的任何屬性。 若要變更內容，請參閱[編輯 Android 虛擬裝置內容](~/android/get-started/installation/android-emulator/device-properties.md)。

7. 新增您需要明確設定的任何其他屬性。 [新裝置] 畫面只會列出最常修改的屬性，但您可以按一下 [新增屬性] 下拉式功能表 (位於底部) 來新增其他屬性：

   [![[新增屬性] 下拉式功能表](device-manager-images/mac/17-add-property-menu-m76-sml.png)](device-manager-images/mac/17-add-property-menu-m76.png#lightbox)

   您也可以按一下此屬性清單頂端的 [自訂...] 來定義自訂屬性。

8. 按一下 [建立] 按鈕 (右下角) 來建立新裝置：

   ![[建立] 按鈕](device-manager-images/mac/18-create-button-m76.png)

9. Android Device Manager 會將新裝置新增至已安裝的虛擬裝置清單中，並在建立裝置期間顯示 [正在建立] 進度列指示器：

   [![建立進度指標](device-manager-images/mac/19-creating-the-device-m76-sml.png)](device-manager-images/mac/19-creating-the-device-m76.png#lightbox)

10. 當建立程序完成時，即會在已安裝的虛擬裝置清單中顯示新裝置，並具有 [啟動] 按鈕，準備好可供啟動：

    [![準備好可供啟動的新建裝置](device-manager-images/mac/20-created-device-m76-sml.png)](device-manager-images/mac/20-created-device-m76.png#lightbox)


### <a name="edit-device"></a>編輯裝置

若要編輯現有的虛擬裝置，選取 [其他選項] 下拉式功能表 (齒輪圖示)，然後選取 [編輯]：

[![用於修改新裝置的 [編輯] 功能表選取項目](device-manager-images/mac/21-edit-button-m76-sml.png)](device-manager-images/mac/21-edit-button-m76.png#lightbox)

按一下 [編輯]，會啟動適用於所選取虛擬裝置的裝置編輯器：

[![[裝置編輯器] 畫面](device-manager-images/mac/22-device-editor-sml.png)](device-manager-images/mac/22-device-editor.png#lightbox)

[裝置編輯器] 畫面會在 [屬性] 欄下方列出虛擬裝置的屬性，其中包含 [值] 欄中每個屬性的對應值。 當您選取屬性時，該屬性的詳細描述就會顯示在右邊。

若要變更屬性，請在 [值] 欄中編輯其值。
例如，在下列螢幕擷取畫面中，正在將 `hw.lcd.density` 屬性從 **480** 變更為 **240**：

[![裝置編輯範例](device-manager-images/mac/23-device-editing-sml.png)](device-manager-images/mac/23-device-editing.png#lightbox)

當您進行了必要的組態變更之後，按一下 [儲存] 按鈕。
如需變更虛擬裝置內容的詳細資訊，請參閱[編輯 Android 虛擬裝置內容](~/android/get-started/installation/android-emulator/device-properties.md)。


### <a name="additional-options"></a>其他選項

搭配裝置運作的其他選項都可從位於 [播放] 按鈕左邊的下拉式功能表中取得：

[![[其他選項] 功能表的位置](device-manager-images/mac/24-overflow-menu-sml.png)](device-manager-images/mac/24-overflow-menu.png#lightbox)

[其他選項] 功能表包含下列項目：

- **編輯** &ndash; 在裝置編輯器中開啟目前選取的裝置，如前文所述。

- **複製和編輯** &ndash; 複製目前選取的裝置，並使用不同的唯一名稱，在 [新裝置] 畫面中開啟它。 例如，選取 **Pixel 2 API 28** 並按一下 [複製和編輯]，會將計數器附加到名稱：

  [![[複製和編輯] 畫面](device-manager-images/mac/25-dupe-and-edit-sml.png)](device-manager-images/mac/25-dupe-and-edit.png#lightbox)

- **在 Finder 中顯示** &ndash; 在保存適用於虛擬裝置之檔案的資料夾中開啟 macOS Finder 視窗。 例如，選取 **Pixel 2 API 28** 並按一下 [在 Finder 中顯示]，會開啟類似下列範例的視窗：

  [![按一下 [在 Finder 中顯示] 的結果](device-manager-images/mac/26-reveal-in-finder-sml.png)](device-manager-images/mac/26-reveal-in-finder.png#lightbox)

- **重設為原廠設定** &ndash; 當裝置正在執行時，將選取的裝置重設為其預設設定，清除對其內部狀態所做的任何使用者變更 (這也會清除目前的[快速開機](~/android/deploy-test/debugging/debug-on-emulator.md#quick-boot)快照集，如果有的話)。 這項變更不會改變您在建立和編輯期間對虛擬裝置所做的修改。 隨即會出現一個對話方塊，提醒您此重設是無法復原的。 按一下 [恢復出廠預設值] 以確認重設。

  ![恢復出廠預設值對話方塊](device-manager-images/mac/27-factory-reset-m76.png)

- **刪除** &ndash; 永久刪除所選取的虛擬裝置。 隨即會出現一個對話方塊，提醒您刪除裝置是無法復原的。 如果您確定要刪除裝置，按一下 [刪除]。

  ![刪除裝置對話方塊](device-manager-images/mac/28-delete-device-m76.png)

-----


<a name="troubleshooting" />

## <a name="troubleshooting"></a>疑難排解

下列章節說明如何診斷和因應使用 Android Device Manager 設定虛擬裝置時可能發生的問題。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="android-sdk-in-non-standard-location"></a>Android SDK 不在標準位置上

Android SDK 通常會安裝於下列位置：

**C:\\Program Files (x86)\\Android\\android-sdk**

如果 SDK 不是安裝在此位置，您在啟動 Android Device Manager 時可能會收到這個錯誤：

![Android SDK 執行個體錯誤](device-manager-images/win/29-sdk-error.png)

若要解決這個問題，請使用下列步驟：

1. 從 Windows 桌面，瀏覽至 **C:\\Users\\*username*\\AppData\\Roaming\\XamarinDeviceManager**：

   ![Android Device Manager 記錄檔位置](device-manager-images/win/30-log-files.png)

2. 按兩下以開啟其中一個記錄檔，並找出 **Config file path**。 例如：

   [![記錄檔中的 Config file path](device-manager-images/win/31-config-file-path-sml.png)](device-manager-images/win/31-config-file-path.png#lightbox)

3. 瀏覽至此位置，然後按兩下 **user.config** 加以開啟。

4. 在 **user.config** 中，找出 `<UserSettings>` 項目，並在其中新增 **AndroidSdkPath** 屬性。 將此屬性設定為電腦上安裝 Android SDK 的路徑，並儲存檔案。 例如，如果 Android SDK 安裝於 **C:\\Programs\\Android\\SDK**，則 `<UserSettings>` 看起來如下：

   ```xml
   <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:ProgramsAndroidSDK" />
   ```

完成對 **user.config** 的變更後，您應該就能啟動 Android Device Manager。


### <a name="wrong-version-of-android-sdk-tools"></a>Android SDK Tools 的版本錯誤

如果未安裝 Android SDK 工具 26.1.1 或更新版本，您會在啟動時看到此錯誤對話方塊：

![Android SDK 執行個體錯誤對話方塊](device-manager-images/win/32-sdk-instance-error.png)

如果您看到此錯誤對話方塊，請按一下 [開啟 Android SDK Manager] 以開啟Android SDK Manager。 在 Android SDK 管理員中，按一下 [工具] 索引標籤，然後安裝下列套件：

- **Android SDK Tools 26.1.1** 或更新版本
- **Android SDK 平台工具 27.0.1** 或更新版本
- **Android SDK 建置工具 27.0.3** 或更新版本


### <a name="snapshot-disables-wifi-on-android-oreo"></a>Android Oreo 上的快照功能會停用 WiFi

如果您有針對具有模擬 Wi-Fi 存取的 Android Oreo 設定 AVD，在使用快照功能後重新啟動 AVD 可能會造成 Wi-Fi 存取被停用。

若要解決這個問題：

1. 在 Android Device Manager 中選取 AVD。

2. 在其他選項功能表中，按一下 [在檔案總管中顯示]。

3. 瀏覽至 [快照] > [default_boot]。

4. 刪除 **snapshot.pb** 檔案：

   ![snapshot.pb 檔案的位置](device-manager-images/win/33-delete-snapshot.png)

5. 重新啟動 AVD。

做出這些變更之後，AVD 將會重新啟動為允許 Wi-Fi 再次運作的狀態。


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

### <a name="wrong-version-of-android-sdk-tools"></a>Android SDK Tools 的版本錯誤

如果未安裝 Android SDK 工具 26.1.1 或更新版本，您會在啟動時看到此錯誤對話方塊：

![Android SDK 執行個體錯誤對話方塊](device-manager-images/mac/29-sdk-instance-error.png)

如果您看到此錯誤對話方塊，按一下 [確定] 以開啟 Android SDK 管理員。 在 Android SDK 管理員中，按一下 [工具] 索引標籤，然後安裝下列套件：

- **Android SDK Tools 26.1.1** 或更新版本
- **Android SDK 平台工具 28.0.1** 或更新版本
- **Android SDK 建置工具 26.0.3** 或更新版本

### <a name="snapshot-disables-wifi-on-android-oreo"></a>Android Oreo 上的快照功能會停用 WiFi

如果您有針對具有模擬 Wi-Fi 存取的 Android Oreo 設定 AVD，在使用快照功能後重新啟動 AVD 可能會造成 Wi-Fi 存取被停用。

若要解決這個問題：

1. 在 Android Device Manager 中選取 AVD。

2. 在其他選項功能表中，按一下 [在尋找工具中顯示]。

3. 瀏覽至 [快照] > [default_boot]。

4. 刪除 **snapshot.pb** 檔案：

   [![snapshot.pb 檔案的位置](device-manager-images/mac/30-delete-snapshot-sml.png)](device-manager-images/mac/30-delete-snapshot.png#lightbox)

5. 重新啟動 AVD。

做出這些變更之後，AVD 將會重新啟動為允許 Wi-Fi 再次運作的狀態。

-----

### <a name="generating-a-bug-report"></a>產生問題報告

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

如果您發現 Android Device Manager 的問題且無法使用上述疑難排解提示來解決，請以滑鼠右鍵按一下標題列，然後選取 [產生 Bug 報告] 來提出問題報告：

[![建檔 Bug 報告的功能表項目位置](device-manager-images/win/34-bug-report-sml.png)](device-manager-images/win/34-bug-report.png#lightbox)


# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

如果您發現 Android Device Manager 的問題且無法使用上述疑難排解祕訣來解決，請按一下 [說明] > [回報問題] 來提出問題報告：

[![建檔 Bug 報告的功能表項目位置](device-manager-images/mac/31-bug-report-sml.png)](device-manager-images/mac/31-bug-report.png#lightbox)

::: zone-end

## <a name="summary"></a>總結

此指南介紹適用於 Xamarin 的 Visual Studio Tools 以及適用於 Mac 的 Visual Studio 所提供的 Android Device Manager。 其中說明了必要的功能，例如，啟動和停止 Android 模擬器、選取要執行的 Android 虛擬裝置 (AVD)、建立新的虛擬裝置，以及如何編輯虛擬裝置。 它說明如何編輯設定檔硬體內容，以進一步自訂，並提供常見問題的疑難排解的秘訣。


## <a name="related-links"></a>相關連結

- [對 Android SDK 工具所做的變更](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [Run Apps on the Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md) (在 Android Emulator 上執行應用程式)
- [SDK 工具版本資訊 (Google)](https://developer.android.com/studio/releases/sdk-tools)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/How-to-Create-and-Manage-Your-Own-Android-Emulators/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
