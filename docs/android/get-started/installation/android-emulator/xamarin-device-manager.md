---
title: 使用 Xamarin Android 裝置管理員管理 Android 模擬器
description: 本指南將說明如何使用 Xamarin Android 裝置管理員，來建立和設定可模擬 Android 裝置的 Android 虛擬裝置 (AVD)。 您可以使用這些虛擬裝置來執行和測試應用程式，而不必依賴實體裝置。
ms.prod: xamarin
ms.assetid: ECB327F3-FF1C-45CC-9FA6-9C11032BD5EF
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/03/2018
ms.openlocfilehash: 420ffc905659c6fd6245dc8cc3bdae4cb9401a63
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="xamarin-android-device-manager"></a>Xamarin Android 裝置管理員

_本指南將說明如何使用 Xamarin Android 裝置管理員，來建立和設定可模擬 Android 裝置的 Android 虛擬裝置 (AVD)。您可以使用這些虛擬裝置來執行和測試應用程式，而不必依賴實體裝置。_

## <a name="overview"></a>總覽

當您確認已啟用硬體加速之後 (如[硬體加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md)中所述)，下一個步驟是使用 Xamarin Android Device Manager 建立要用於測試應用程式和針對應用程式進行偵錯的虛擬裝置。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

本指南說明如何使用適用於 Windows (或[適用於 Mac](?tabs=vsmac)) 之 Visual Studio 的 Xamarin Android 裝置管理員：

[![[裝置] 索引標籤中 Xamarin Android 裝置管理員的螢幕擷取畫面](xamarin-device-manager-images/win/01-devices-dialog-sml.png)](xamarin-device-manager-images/win/01-devices-dialog.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

本指南說明如何使用適用於 Mac (或[適用於 Windows](?tabs=vswin)) 之 Visual Studio 的 Xamarin Android 裝置管理員：

[![[裝置] 索引標籤中 Xamarin Android 裝置管理員的螢幕擷取畫面](xamarin-device-manager-images/mac/01-devices-dialog-sml.png)](xamarin-device-manager-images/mac/01-devices-dialog.png#lightbox)

> [!NOTE]
> 本指南僅適用於 Visual Studio for Mac。
Xamarin Studio 與 Xamarin Android 裝置管理員不相容。

-----

您會使用 Xamarin Android 裝置管理員，來建立和設定可在 [Android SDK 模擬器](~/android/deploy-test/debugging/android-sdk-emulator/index.md)中執行的「Android 虛擬裝置」(AVD)。
每個 AVD 都是可模擬實體 Android 裝置的模擬器組態。 這讓您能夠在模擬不同實體 Android 裝置的各種組態中，執行並測試應用程式。 Xamarin Android 裝置管理員會取代 Google 的獨立 AVD Manager (已過時)。

在本指南中，您會了解如何使用 Android Device Manager 建立、複製、自訂和啟動虛擬裝置。 本指南也會說明如何設定每個虛擬裝置的屬性 (例如，API 層級、CPU、記憶體和解析度)，來啟用/停用模擬的感應器 (例如，加速計、GPS、方向及光感應器)，並設定該虛擬裝置所使用的硬體加速類型。

## <a name="requirements"></a>需求

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要使用 Xamarin Android 裝置管理員，您需要下列項目：

- 需要 Visual Studio 2017 15.7 版或更新版本。 支援 Visual Studio Community Edition 和更新版本。

- Xamarin for Visual Studio 4.9 版或更新版本。 如需更新 Xamarin 的相關資訊，請參閱[變更更新通道](https://developer.xamarin.com/recipes/cross-platform/ide/change_updates_channel/)。

- **Android SDK** &ndash; 必須安裝 Android SDK (請參閱 [Android SDK 安裝](~/android/get-started/installation/android-sdk.md))，而且必須安裝 SDK 工具 26.0 版或更新版本，如下一節中所述。 確定會將 Android SDK 安裝於下列位置 (如果尚未安裝)：**C:\\Program Files (x86)\\Android\\android-sdk**。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

- Visual Studio for Mac 7.5 版或更新版本。

- **Android SDK** &ndash; 必須透過 SDK 管理員安裝 Android SDK 8.0 (API 26) 或更新版本。

-----

## <a name="launching-the-device-manager"></a>啟動裝置管理員

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

按一下 [工具] > [Android 模擬器管理員]，從 [工具] 功能表啟動 Xamarin Android Device Manager：

[![正在從 [工具] 功能表啟動](xamarin-device-manager-images/win/04-tools-menu-sml.png)](xamarin-device-manager-images/win/04-tools-menu.png#lightbox)

如果您在啟動時看到下列錯誤對話方塊，請參閱[疑難排解](#troubleshooting)一節來取得因應措施的指示：

![Android SDK 執行個體錯誤](xamarin-device-manager-images/win/32-sdk-error.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

在 Visual Studio for Mac 中，選取 [工具] > [模擬器管理員] 來啟動 Xamarin Android Device Manager：

[![正在從 [工具] 功能表啟動](xamarin-device-manager-images/mac/16-tools-menu-sml.png)](xamarin-device-manager-images/mac/16-tools-menu.png#lightbox)

-----

您必須先安裝 Android SDK 工具 26.0.0 版或更新版本，才能使用 Android 裝置管理員。 如果未安裝 Android SDK 工具 26.0.0 或更新版本，您將會在啟動時看到此錯誤對話方塊：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Android SDK 執行個體錯誤對話方塊](xamarin-device-manager-images/win/02-sdk-instance-error.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![Android SDK 執行個體錯誤對話方塊](xamarin-device-manager-images/mac/02-sdk-instance-error.png)

-----

如果您看到此錯誤對話方塊，按一下 [確定] 以開啟 Android SDK 管理員。 在 Android SDK 管理員中，按一下 [工具] 索引標籤，然後安裝 **Android SDK 工具 26.0.2** 或更新版本、**Android SDK 平台工具 26.0.0** 或更新版本，以及 **Android SDK 建置工具 26.0.0** (或更新版本)：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![正在安裝 Android SDK 工具 26.0](xamarin-device-manager-images/win/03-sdk-tools-sml.png)](xamarin-device-manager-images/win/03-sdk-tools.png#lightbox)

安裝這些套件之後，您可以關閉 SDK 管理員，然後重新啟動 Android 裝置管理員。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![正在安裝 Android SDK 工具 26.0](xamarin-device-manager-images/mac/03-sdk-tools-sml.png)](xamarin-device-manager-images/mac/03-sdk-tools.png#lightbox)

-----

## <a name="main-screen"></a>主畫面

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

當您第一次啟動 Android 裝置管理員時，它所呈現的畫面會顯示所有目前設定的虛擬裝置。 針對每個裝置，都會顯示**名稱**、**作業系統** (Android API 層級)、**CPU**、**記憶體**大小及螢幕解析度：

[![已安裝之裝置及其參數的清單](xamarin-device-manager-images/win/05-installed-list-sml.png)](xamarin-device-manager-images/win/05-installed-list.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

當您第一次啟動 Android 裝置管理員時，它所呈現的畫面會顯示所有目前設定的虛擬裝置。 針對每個裝置，都會顯示**名稱**、**系統映像** (Android API 層級)、**CPU**、**記憶體**大小及螢幕解析度：

[![已安裝之裝置及其參數的清單](xamarin-device-manager-images/mac/05-devices-list-sml.png)](xamarin-device-manager-images/mac/05-devices-list.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

當您按一下清單中的裝置時，會在右邊出現 [啟動] 按鈕。 您可以按一下 [啟動] 按鈕，使用此虛擬裝置來啟動模擬器：

[![裝置映像的 [啟動] 按鈕](xamarin-device-manager-images/win/06-start-button-sml.png)](xamarin-device-manager-images/win/06-start-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

按一下 [播放] 按鈕，使用您所選擇的虛擬裝置來啟動模擬器：

[![裝置映像的 [啟動] 按鈕](xamarin-device-manager-images/mac/06-start-button-sml.png)](xamarin-device-manager-images/mac/06-start-button.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

當模擬器使用所選取的虛擬裝置啟動之後，[啟動] 按鈕就會變成 [停止] 按鈕，讓您可用來暫止模擬器：

[![適用於執行中裝置的 [停止] 按鈕](xamarin-device-manager-images/win/07-stop-button-sml.png)](xamarin-device-manager-images/win/07-stop-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

當模擬器使用所選取的虛擬裝置啟動之後，[播放] 按鈕就會變成 [停止] 按鈕，讓您可用來暫止模擬器：

[![適用於執行中裝置的 [停止] 按鈕](xamarin-device-manager-images/mac/07-stop-button-sml.png)](xamarin-device-manager-images/mac/07-stop-button.png#lightbox)

-----

### <a name="new-device"></a>新裝置

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要建立新裝置，按一下 [新增] 按鈕 (位於畫面的右上方區域)：

[![建立新裝置的 [新增] 按鈕](xamarin-device-manager-images/win/08-new-button-sml.png)](xamarin-device-manager-images/win/08-new-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要建立新裝置，按一下 [新裝置] 按鈕 (位於畫面的右上方區域)：

[![建立新裝置的 [新增] 按鈕](xamarin-device-manager-images/mac/08-new-button-sml.png)](xamarin-device-manager-images/mac/08-new-button.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

按一下 [新增] 會啟動 [新裝置] 畫面：

[![裝置管理員的 [新裝置] 畫面](xamarin-device-manager-images/win/09-new-device-editor-sml.png)](xamarin-device-manager-images/win/09-new-device-editor.png#lightbox)

若要在 [新裝置] 畫面中設定新裝置，請使用下列步驟：

1. 按一下 [裝置] 下拉式功能表來選取要模擬的實體裝置：

    [![[裝置] 下拉式功能表](xamarin-device-manager-images/win/10-device-menu-sml.png)](xamarin-device-manager-images/win/10-device-menu.png#lightbox)

2. 按一下 [系統映像] 下拉式功能表，來選取要與此虛擬裝置搭配使用的系統映像。 這個功能表會在 [已安裝] 下方列出已安裝的系統映像。 [下載] 區段會列出目前無法在您的開發電腦上取得，但可自動安裝的系統映像：

    [![[系統映像] 下拉式功能表](xamarin-device-manager-images/win/11-system-image-menu-sml.png)](xamarin-device-manager-images/win/11-system-image-menu.png#lightbox)

3. 指定裝置的新名稱。 在下列範例中，會將新裝置命名為 **Nexus 5 API 25**：

    [![為新裝置命名](xamarin-device-manager-images/win/12-device-name-sml.png)](xamarin-device-manager-images/win/12-device-name.png#lightbox)

4. 編輯您需要修改的任何屬性。 若要變更屬性，請參閱本指南稍後的[設定檔屬性](#properties)。

5. 新增您需要明確設定的任何其他屬性。 [新裝置] 畫面只會列出最常修改的屬性，但是您可以按一下 [新增屬性] 下拉式功能表 (位於左下角) 來新增其他屬性。 在下列範例中，會新增 `hw.lcd.backlight` 屬性：

    [![[新增屬性] 下拉式功能表](xamarin-device-manager-images/win/13-add-property-menu-sml.png)](xamarin-device-manager-images/win/13-add-property-menu.png#lightbox)

6. 按一下 [建立] 按鈕 (右下角) 來建立新裝置：

    ![[建立] 按鈕](xamarin-device-manager-images/win/14-create-button.png)

7. 您可能會看見 [接受授權] 畫面。 如果您同意授權條款，按一下 [接受]：

    ![[接受授權] 畫面](xamarin-device-manager-images/win/15-license-acceptance.png)

8. Android 裝置管理員會在建立該裝置時，將新裝置新增至已安裝虛擬裝置的清單，並顯示 [正在建立] 進度列指示器：

    [![[正在建立] 進度列指示器](xamarin-device-manager-images/win/16-creating-the-device-sml.png)](xamarin-device-manager-images/win/16-creating-the-device.png#lightbox)

9. 當建立程序完成時，即會在已安裝的虛擬裝置清單中顯示新裝置，並具有 [啟動] 按鈕，準備好可供啟動：

   [![準備好可供啟動的新建裝置](xamarin-device-manager-images/win/17-created-device-sml.png)](xamarin-device-manager-images/win/17-created-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

按一下 [新裝置] 會啟動 [新裝置] 畫面：

[![裝置管理員的 [新裝置] 畫面](xamarin-device-manager-images/mac/09-new-device-editor-sml.png)](xamarin-device-manager-images/mac/09-new-device-editor.png#lightbox)

使用下列步驟，在 [新裝置] 畫面中設定新裝置：

1. 按一下 [裝置] 下拉式功能表來選取要模擬的實體裝置：

    [![[裝置] 下拉式功能表](xamarin-device-manager-images/mac/10-device-menu-sml.png)](xamarin-device-manager-images/mac/10-device-menu.png#lightbox)

2. 按一下 [系統映像] 下拉式功能表，來選取要與此虛擬裝置搭配使用的系統映像。 這個功能表會在 [已安裝] 下方列出已安裝的系統映像。 [下載] 區段 (如果顯示) 會列出目前無法在您的開發電腦上取得，但可自動安裝的系統映像：

    [![[系統映像] 下拉式功能表](xamarin-device-manager-images/mac/11-system-image-menu-sml.png)](xamarin-device-manager-images/mac/11-system-image-menu.png#lightbox)

3. 指定裝置的新名稱。 在下列範例中，會將新裝置命名為 **Nexus 5X API 25**：

    [![為新裝置命名](xamarin-device-manager-images/mac/12-device-name-sml.png)](xamarin-device-manager-images/mac/12-device-name.png#lightbox)

4. 編輯您需要修改的任何屬性。 若要變更屬性，請參閱本指南稍後的[設定檔屬性](#properties)。

5. 新增您需要明確設定的任何其他屬性。 [新裝置] 畫面只會列出最常修改的屬性，但是您可以按一下 [新增屬性] 下拉式功能表 (位於左下角) 來新增其他屬性：

    [![[新增屬性] 下拉式功能表](xamarin-device-manager-images/mac/13-add-property-menu-sml.png)](xamarin-device-manager-images/mac/13-add-property-menu.png#lightbox)

6. 您也可以按一下 [自訂] 來定義裝置的新屬性：

    ![[建立] 按鈕](xamarin-device-manager-images/mac/14-custom-button.png)

7. 按一下 [建立] 按鈕 (右下角) 來建立新裝置：

    ![[建立] 按鈕](xamarin-device-manager-images/mac/15-create-button.png)

8. 您可能會看見 [接受授權] 畫面。 如果您同意授權條款，按一下 [接受]。

9. Android 裝置管理員會在建立裝置時，將新裝置新增至裝置清單，並顯示 [正在建立] 進度列指示器：

    [![[正在建立] 進度列指示器](xamarin-device-manager-images/mac/17-creating-the-device-sml.png)](xamarin-device-manager-images/mac/17-creating-the-device.png#lightbox)

10. 當建立程序完成時，即會在虛擬裝置清單中顯示新裝置，並具有 [播放] 按鈕，準備好可供啟動：

   [![準備好可供啟動的新建裝置](xamarin-device-manager-images/mac/18-created-device-sml.png)](xamarin-device-manager-images/mac/18-created-device.png#lightbox)

-----


<a name="device-edit" />


### <a name="edit-device"></a>編輯裝置

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要編輯現有的虛擬裝置，選取裝置，然後按一下 [編輯] 按鈕 (位於畫面右上角)：

[![用於修改新裝置的 [編輯] 按鈕](xamarin-device-manager-images/win/19-edit-button-sml.png)](xamarin-device-manager-images/win/19-edit-button.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要編輯現有的虛擬裝置，選取 [其他選項] 下拉式功能表 (齒輪圖示)，然後選取 [編輯]：
 
[![用於修改新裝置的 [編輯] 功能表選取項目](xamarin-device-manager-images/mac/19-edit-button-sml.png)](xamarin-device-manager-images/mac/19-edit-button.png#lightbox)
 
-----

按一下 [編輯]，會啟動適用於所選取虛擬裝置的裝置編輯器：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![[裝置編輯器] 畫面](xamarin-device-manager-images/win/20-device-editor-sml.png)](xamarin-device-manager-images/win/20-device-editor.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
 
[![[裝置編輯器] 畫面](xamarin-device-manager-images/mac/20-device-editor-sml.png)](xamarin-device-manager-images/mac/20-device-editor.png#lightbox)
 
-----

[裝置編輯器] 畫面會在第一欄中列出虛擬裝置的屬性，並在第二欄中列出每個屬性的對應值。 當您選取屬性時，該屬性的詳細描述就會顯示在右邊。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

例如，在下列螢幕擷取畫面中，正在將 `hw.lcd.density` 屬性從 **420** 變更為 **240**：

[![裝置編輯範例](xamarin-device-manager-images/win/21-device-editing-sml.png)](xamarin-device-manager-images/win/21-device-editing.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

例如，在下列螢幕擷取畫面中，已將 `hw.lcd.density` 屬性從 **320** 變更為 **240**，並將 `hw.ramSize` 屬性變更為 **768**：
 
[![裝置編輯範例](xamarin-device-manager-images/mac/21-device-editing-sml.png)](xamarin-device-manager-images/mac/21-device-editing.png#lightbox)
 
-----

當您進行了必要的組態變更之後，按一下 [儲存] 按鈕。
如需變更虛擬裝置屬性的詳細資訊，請參閱本指南稍後的[設定檔屬性](#properties)。


 
### <a name="additional-options"></a>其他選項

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

搭配裝置運作的其他選項都可從右上角的 &hellip; 功能表中取得：

[![[其他選項] 功能表的位置](xamarin-device-manager-images/win/22-overflow-menu-sml.png)](xamarin-device-manager-images/win/22-overflow-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

搭配裝置運作的其他選項都可從位於 [播放] 按鈕左邊的下拉式功能表中取得：

[![[其他選項] 功能表的位置](xamarin-device-manager-images/mac/22-overflow-menu-sml.png)](xamarin-device-manager-images/mac/22-overflow-menu.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[其他選項] 功能表包含下列項目：

-   **複製和編輯** &ndash; 複製目前選取的裝置，並使用不同的唯一名稱，在 [新裝置] 畫面中開啟它。 例如，選取 **VisualStudio_android-23_x86_phone** 並按一下 [複製和編輯]，會將計數器附加到名稱：

    [![[複製和編輯] 畫面](xamarin-device-manager-images/win/23-dupe-and-edit-sml.png)](xamarin-device-manager-images/win/23-dupe-and-edit.png#lightbox)

-   **在檔案總管中顯示** &ndash; 在保存適用於虛擬裝置之檔案的資料夾中開啟 [Windows 檔案總管] 視窗。 例如，選取 **Nexus 5 X API 25** 並按一下 [在檔案總管中顯示]，會開啟如下的視窗：

    [![按一下 [在檔案總管中顯示] 的結果](xamarin-device-manager-images/win/24-reveal-in-explorer-sml.png)](xamarin-device-manager-images/win/24-reveal-in-explorer.png#lightbox)

-   **重設成出廠預設值** &ndash; 當裝置正在執行時，將選取的裝置重設為其預設設定，清除對其內部狀態所做的任何使用者變更。 這項變更不會改變您在建立和編輯期間對虛擬裝置所做的修改。 隨即會出現一個對話方塊，提醒您此重設是無法復原的。 按一下 [清除使用者資料] 以確認重設。

-   **刪除** &ndash; 永久刪除所選取的虛擬裝置。
    隨即會出現一個對話方塊，提醒您刪除裝置是無法復原的。 如果您確定要刪除裝置，按一下 [刪除]。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[其他選項] 功能表包含下列項目：

-   **編輯** &ndash; 在裝置編輯器中開啟目前選取的裝置，如稍早在[編輯裝置](#device-edit)中所述。

-   **複製和編輯** &ndash; 複製目前選取的裝置，並使用不同的唯一名稱，在 [新裝置] 畫面中開啟它。
    例如，選取 **Nexus 5X API 25** 並按一下 [複製和編輯]，會將計數器附加到名稱：

    [![[複製和編輯] 畫面](xamarin-device-manager-images/mac/23-dupe-and-edit-sml.png)](xamarin-device-manager-images/mac/23-dupe-and-edit.png#lightbox)

-   **在 Finder 中顯示** &ndash; 在保存適用於虛擬裝置之檔案的資料夾中開啟 macOS Finder 視窗。 例如，選取 **Nexus 5 X API 25** 並按一下 [在 Finder 中顯示]，會開啟如下的視窗：

    [![按一下 [在檔案總管中顯示] 的結果](xamarin-device-manager-images/mac/24-reveal-in-finder-sml.png)](xamarin-device-manager-images/mac/24-reveal-in-finder.png#lightbox)

-   **重設成出廠預設值** &ndash; 當裝置正在執行時，將選取的裝置重設為其預設設定，清除對其內部狀態所做的任何使用者變更。 這項變更不會改變您在建立和編輯期間對虛擬裝置所做的修改。 隨即會出現一個對話方塊，提醒您此重設是無法復原的。 按一下 [清除使用者資料] 以確認重設。

-   **刪除** &ndash; 永久刪除所選取的虛擬裝置。
    隨即會出現一個對話方塊，提醒您刪除裝置是無法復原的。" 如果您確定要刪除裝置，按一下 [刪除]。

-----

<a name="properties" />
 

## <a name="profile-properties"></a>設定檔屬性

[新裝置] 和 [裝置編輯] 畫面會在第一欄中列出虛擬裝置的屬性，並在第二欄中列出每個屬性的對應值。 當您選取屬性時，該屬性的詳細描述就會顯示在右邊。 您可以修改其「硬體設定檔屬性」及其「AVD 屬性」。
硬體設定檔屬性 (例如 `hw.ramSize` 和 `hw.accelerometer`) 會說明模擬裝置的實體特性。 這些特性包括螢幕大小、可用的 RAM 數量、加速計是否存在。 AVD 屬性會在 AVD 執行時指定其作業。 例如，您可以設定 AVD 屬性，來指定 AVD 如何使用您開發電腦的圖形卡進行轉譯。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

您可以使用下列指導方針來變更屬性：

-   若要變更布林值屬性，按一下布林值屬性右邊的核取記號：

    ![變更布林值屬性](xamarin-device-manager-images/win/25-boolean-value.png)

-   若要變更 *enum* (列舉) 屬性，按一下屬性右邊的向下箭頭，然後選擇新值。

    ![變更列舉屬性](xamarin-device-manager-images/win/27-enum-value.png)

-   若要變更字串或整數屬性，在 [值] 欄中按兩下目前的字串或整數設定，然後輸入新值。

    ![變更整數屬性](xamarin-device-manager-images/win/26-integer-value.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

您可以使用下列指導方針來變更屬性：

-   若要變更布林值屬性，按一下布林值屬性右邊的核取記號：

    ![變更布林值屬性](xamarin-device-manager-images/mac/25-boolean-value.png)

-   若要變更 *enum* (列舉) 屬性，按一下屬性右邊的下拉式功能表，然後選擇新值。

    ![變更列舉屬性](xamarin-device-manager-images/mac/27-enum-value.png)

-   若要變更字串或整數屬性，在 [值] 欄中按兩下目前的字串或整數設定，然後輸入新值。

    ![變更整數屬性](xamarin-device-manager-images/mac/26-integer-value.png)


-----

下表提供 [新裝置] 和 [裝置編輯器] 畫面中所列屬性的詳細說明：

[!include[](~/android/includes/emulator-properties.md)]

如需這些屬性的詳細資訊，請參閱[硬體設定檔屬性](https://developer.android.com/studio/run/managing-avds.html#hpproperties) \(英文\)。


<a name="troubleshooting" />

## <a name="troubleshooting"></a>疑難排解

以下說明一般的 Xamarin Android 裝置管理員問題和因應措施：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-in-non-standard-location"></a>Android SDK 不在標準位置上

Android SDK 通常會安裝於下列位置：

**C:\\Program Files (x86)\\Android\\android-sdk**

如果 SDK 未安裝於此位置，您可能會在啟動時收到這個錯誤：

![Android SDK 執行個體錯誤](xamarin-device-manager-images/win/32-sdk-error.png)

為了因應此問題，請執行下列動作：

1. 從 Windows 桌面，瀏覽至 **C:\\Users\\*username*\\AppData\\Roaming\\XamarinDeviceManager**：

    ![Xamarin 裝置管理員記錄檔位置](xamarin-device-manager-images/win/33-log-files.png)

2. 按兩下以開啟其中一個記錄檔，並找出 **Config file path**。 例如: 

    [![記錄檔中的 Config file path](xamarin-device-manager-images/win/34-config-file-path-sml.png)](xamarin-device-manager-images/win/34-config-file-path.png#lightbox)

3. 瀏覽至此位置，然後按兩下 **user.config** 加以開啟。 

4. 在 **user.config** 中，找出 **&lt;UserSettings&gt;** 元素，並在其中新增 **AndroidSdkPath** 屬性。 將此屬性設定為電腦上安裝 Android SDK 的路徑，並儲存檔案。 例如，如果 Android SDK 安裝於 **C:\\Programs\\Android\\SDK**，則 **&lt;UserSettings&gt;** 看起來如下：
        
    ```xml
    <UserSettings SdkLibLastWriteTimeUtcTicks="636409365200000000" AndroidSdkPath="C:\Programs\Android\SDK" />
    ```

對 **user.config** 進行此變更之後，您應該就能啟動 Xamarin Android 裝置管理員。

### <a name="snapshot-disables-wifi-on-android-oreo"></a>Android Oreo 上的快照功能會停用 WiFi

如果您有針對具有模擬 Wi-Fi 存取的 Android Oreo 設定 AVD，在使用快照功能後重新啟動 AVD 可能會造成 Wi-Fi 存取被停用。

若要解決這個問題：

1. 在 Xamarin 裝置管理員中選取 AVD。

2. 在其他選項功能表中，按一下 [在檔案總管中顯示]。

3. 瀏覽至 [快照] > [default_boot]。

4. 刪除 **snapshot.pb** 檔案：

    [![snapshot.pb 檔案的位置](xamarin-device-manager-images/win/36-delete-snapshot-sml.png)](xamarin-device-manager-images/win/36-delete-snapshot.png#lightbox)

5. 重新啟動 AVD。 

做出這些變更之後，AVD 將會重新啟動為允許 Wi-Fi 再次運作的狀態。


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

### <a name="snapshot-disables-wifi-on-android-oreo"></a>Android Oreo 上的快照功能會停用 WiFi

如果您有針對具有模擬 Wi-Fi 存取的 Android Oreo 設定 AVD，在使用快照功能後重新啟動 AVD 可能會造成 Wi-Fi 存取被停用。

若要解決這個問題：

1. 在 Xamarin 裝置管理員中選取 AVD。

2. 在其他選項功能表中，按一下 [在尋找工具中顯示]。

3. 瀏覽至 [快照] > [default_boot]。

4. 刪除 **snapshot.pb** 檔案：

    [![snapshot.pb 檔案的位置](xamarin-device-manager-images/mac/36-delete-snapshot-sml.png)](xamarin-device-manager-images/mac/36-delete-snapshot.png#lightbox)

5. 重新啟動 AVD。 

做出這些變更之後，AVD 將會重新啟動為允許 Wi-Fi 再次運作的狀態。

-----


### <a name="generating-a-bug-report"></a>產生問題報告

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

如果您發現 Xamarin Android 裝置管理員的問題且無法使用上述疑難排解提示來解決，請以滑鼠右鍵按一下標題列，然後選取 [Generate Bug Report] \(產生問題報告\) 來提出問題報告：

![用來提出問題報告的功能表項目位置](xamarin-device-manager-images/win/35-bug-report.png)


# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

如果您發現 Xamarin Android 裝置管理員的問題且無法使用上述疑難排解提示來解決，請按一下 [說明] > [產生問題報告] 來提出問題報告：

![用來提出問題報告的功能表項目位置](xamarin-device-manager-images/mac/35-bug-report.png)

-----

 
 
## <a name="summary"></a>總結

本指南引進了適用於 Visual Studio for Mac 和 Xamarin for Visual Studio 的 Xamarin Android 裝置管理員。 其中說明了必要的功能，例如，啟動和停止 Android 模擬器、選取要執行的 Android 虛擬裝置 (AVD)、建立新的虛擬裝置，以及如何編輯虛擬裝置。 它也會說明如何編輯設定檔硬體屬性以進行進一步自訂。


## <a name="related-links"></a>相關連結

- [對 Android SDK 工具所做的變更](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [使用 Android SDK 模擬器進行偵錯](~/android/deploy-test/debugging/android-sdk-emulator/index.md)
- [SDK 工具版本資訊 (Google)](https://developer.android.com/studiohttps://developer.xamarin.com/releases/sdk-tools.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
