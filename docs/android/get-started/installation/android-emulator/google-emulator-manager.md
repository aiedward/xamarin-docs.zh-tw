---
title: Google 模擬器管理員
description: 如何使用 Google 模擬器管理員來建立和管理 Android 虛擬裝置
ms.prod: xamarin
ms.assetid: 0C0BBEC0-C84A-4558-B905-4EF81FCD62F9
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: c42ebdca44e47e29ac74a263f0d11d4d4c120586
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="google-emulator-manager"></a>Google 模擬器管理員

當您確認已啟用硬體加速之後 (如 [Android Emulator 硬體加速](~/android/get-started/installation/android-emulator/hardware-acceleration.md)中所述)，下一個步驟是建立要用於測試應用程式和針對應用程式進行偵錯的虛擬裝置。 您可以使用舊版 Google 模擬器管理員 (也稱為「Android 虛擬裝置 (AVD) 管理員」)，來建立供 Google Android 模擬器使用的虛擬裝置。

> [!NOTE]
> 如果您的目標是 Android 8.0 Oreo，則必須使用 [Xamarin Android 裝置管理員](~/android/get-started/installation/android-emulator/xamarin-device-manager.md)來建立和設定虛擬裝置。


## <a name="installing-system-images"></a>安裝系統映像

根據您要當做目標的 Android API 層級而定，您必須下載並安裝 API 層級特定的系統映像，以供 Android SDK 模擬器使用。 針對每個 Android API 層級，有一組 **x86** 系統映像，您必須加以下載並安裝，才能建立虛擬裝置。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要安裝所需的系統映像，啟動 Android SDK 管理員 ([工具] > [Android] > [Android SDK 管理員])，然後捲動至您想要支援的 API 層級。 針對每個 API 層級，啟用下列系統映像旁邊的核取記號：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要安裝所需的系統映像，啟動 Android SDK 管理員 ([工具] > [SDK 管理員])，然後捲動至您想要支援的 API 層級。 針對每個 API 層級，啟用下列系統映像旁邊的核取記號：

-----

-   **Intel x86 Atom 系統映像**
-   **Google APIs Intel x86 Atom 系統映像**

後者的系統映像會將 Google API (例如 Google 地圖 API) 新增至虛擬裝置。 

在下列螢幕擷取畫面中，將安裝 **Intel x86 Atom** 映像，如此便可建立執行 Android 6.0 的虛擬裝置：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![針對 Android Emulator 選取 Android 6.0 x86 系統映像](google-emulator-manager-images/win/03-select-x86-images-sml.png)](google-emulator-manager-images/win/03-select-x86-images.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![針對 Android Emulator 選取 Android 6.0 x86 系統映像](google-emulator-manager-images/mac/02-select-x86-images-sml.png)](google-emulator-manager-images/mac/02-select-x86-images.png#lightbox)

-----

如果您正在開發 64 位元應用程式，請改為安裝下列系統映像：

-   **Intel x86 Atom_64 系統映像**
-   **Google APIs Intel x86 Atom_64 系統映像**

您可以使用這些 64 位元系統映像來執行 32 位元應用程式。不過，32 位元 **Intel x86 Atom 系統映像**在 Android SDK 模擬器中的執行速度會稍微快一點。

如果您正在開發適用於 Android Wear 的應用程式，請安裝下列系統映像：

-   **Android Wear Intel x86 Atom 系統映像**
-   **Google APIs Intel x86 Atom 系統映像**

安裝這些系統映像之後，您可以在設定虛擬裝置設定過程中選取適當的 API 層級和 CPU/ABI 選項 (後續將說明這一點)，以建立 **x86** 型 Android 虛擬裝置。


## <a name="configuring-virtual-devices"></a>設定虛擬裝置

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

虛擬裝置會透過 **Android Emulator 管理員** (也稱為「Android 虛擬裝置管理員」或 _AVD Manager_) 來設定。 若要從 Visual Studio 啟動「Android Emulator 管理員」，按一下工具列中的 [Android Emulator 管理員] 圖示：

[![AVD 圖示位置](google-emulator-manager-images/win/04-avd-icon-sml.png)](google-emulator-manager-images/win/04-avd-icon.png#lightbox)

您也可以從功能表列選取 [工具] > [Android] > [Android Emulator 管理員] 來啟動「Android Emulator 管理員」：

[![[Android Emulator 管理員] 功能表項目位置](google-emulator-manager-images/win/05-avd-manager-menu-item-sml.png)](google-emulator-manager-images/win/05-avd-manager-menu-item.png#lightbox)

[Android 虛擬裝置 (AVD) 管理員] 對話方塊會顯示現有 Android 虛擬裝置的清單：

[![Android 虛擬裝置管理員](google-emulator-manager-images/win/06-virtual-device-manager-sml.png)](google-emulator-manager-images/win/06-virtual-device-manager.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

虛擬裝置會透過 **Android Emulator 管理員** (也稱為「Android 虛擬裝置管理員」或 _AVD Manager_) 來設定。 

您可以從功能表列選取 [工具] > [Google 模擬器管理員] 來啟動 Android Emulator 管理員：

[![[Android Emulator 管理員] 功能表項目位置](google-emulator-manager-images/mac/03-avd-manager-menu-item-sml.png)](google-emulator-manager-images/mac/03-avd-manager-menu-item.png#lightbox)

[Android 虛擬裝置 (AVD) 管理員] 對話方塊會顯示現有 Android 虛擬裝置的清單：

[![Android 虛擬裝置管理員](google-emulator-manager-images/mac/05-virtual-device-manager-sml.png)](google-emulator-manager-images/mac/05-virtual-device-manager.png#lightbox)

-----

您可以使用不同的裝置特性和 API 層級來建立新的虛擬裝置映像 &ndash; 下節將說明如何建立自訂裝置定義和虛擬裝置。


### <a name="creating-a-custom-device-definition"></a>建立自訂裝置定義

若要建立自訂裝置定義，按一下 [Android 虛擬裝置 (AVD) 管理員] 中的 [建立]。 這會開啟 [Create new Android Virtual Device (AVD)] \(建立新的 Android 虛擬裝置 (AVD)\) 對話方塊：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![以 Nexus 6 為基礎的自訂裝置定義](google-emulator-manager-images/win/07-custom-device-sml.png)](google-emulator-manager-images/win/07-custom-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![以 Nexus 6 為基礎的自訂裝置定義](google-emulator-manager-images/mac/06-custom-device-sml.png)](google-emulator-manager-images/mac/06-custom-device.png#lightbox)

-----

在此對話方塊中，設定下列選項：

-   **AVD Name (AVD 名稱)** &ndash; 裝置定義的唯一名稱。 在上面的範例螢幕擷取畫面中，名稱是設定為 **MyNexus**。 請注意，AVD 名稱不能包含空格 &ndash; 如果您嘗試在 AVD 名稱中使用空格，將停用 [確定] 按鈕。

-   **Device (裝置)** &ndash; 選取您要模擬的硬體設定檔 (例如 **Nexus 5** 或 **Nexus 6**)。

-   **Target (目標)** &ndash; 選取虛擬裝置的 Android API 層級。 此設定應該大於或等於應用程式的最小 Android 版本。

-   **CPU/ABI** &ndash; 選取 [Google APIs Intel Atom (x86)]，如此就能在您的裝置定義中使用 Google API。

-   **Skin (面板)** &ndash; 選取虛擬裝置的外觀。 在上面的範例螢幕擷取畫面中，已選取選取 [HVGA] 面板 (本文結尾處的模擬器螢幕擷取畫面是 **HVGA** 面板的範例)。

-   **Memory Options (記憶體選項)** &ndash; 預設的 RAM 設定通常太高，因而會在 Windows 中引發警告：**emulating RAM greater than 768M may fail (模擬大於 768M 的 RAM 可能會失敗)**。 針對大多數使用者，建議將 RAM 設定為 768MB (如上面的螢幕擷取畫面所示)。 較大的 RAM 值會使得模擬器變慢。

-   **Use Host GPU (使用主機 GPU)** &ndash; 這個選項會讓模擬器使用主機電腦的圖形處理器 (GPU) 來執行圖形作業。 我們建議您啟用此選項，以進一步提高模擬器的效能。 如需 [Emulation Options] \(模擬選項\) 區段的詳細資訊，請參閱[快照和使用主機 GPU 模擬選項的用途為何？](https://android.stackexchange.com/questions/51739/what-is-snapshot-and-use-host-gpu-emulation-options-for) \(英文\)


其餘的選項則可保留為預設設定。 當您準備好時，按一下 [確定] 以建立新的虛擬裝置。 下一個對話方塊中會詳細說明新虛擬裝置設定的結果：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![建立新 AVD 之後的結果對話方塊](google-emulator-manager-images/win/08-create-results.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![建立新 AVD 之後的結果對話方塊](google-emulator-manager-images/mac/07-create-results.png)

-----

如需這個對話方塊中列出的設定屬性詳細說明，請參閱[硬體設定檔屬性](https://developer.android.com/studio/run/managing-avds.html#hpproperties) \(英文\)。
當您按一下 [確定] 之後，新的裝置設定就會顯示在現有 Android 虛擬裝置的清單中。 在下列螢幕擷取畫面中，已將 **MyNexus** 新增至清單：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![已將 MyNexus 新增至裝置清單](google-emulator-manager-images/win/09-added-to-list-sml.png)](google-emulator-manager-images/win/09-added-to-list.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![已將 MyNexus 新增至裝置清單](google-emulator-manager-images/mac/08-added-to-list-sml.png)](google-emulator-manager-images/mac/08-added-to-list.png#lightbox)

-----

新的自訂虛擬裝置也會新增至裝置下拉式功能表：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![已將 MyNexus 新增至裝置下拉式功能表](google-emulator-manager-images/win/10-available-custom-device-sml.png)](google-emulator-manager-images/win/10-available-custom-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![已將 MyNexus 新增至裝置下拉式功能表](google-emulator-manager-images/mac/09-available-custom-device-sml.png)](google-emulator-manager-images/mac/09-available-custom-device.png#lightbox)

-----



### <a name="cloning-a-device-definition"></a>複製裝置定義

您可以選取現有的裝置定義並加以「複製」，以建立新的自訂裝置定義。 這是個可在現有裝置定義只需進行少數微小調整，即可符合您的需求時使用的好策略。 [Android Virtual Device (AVD) Manager] \(Android 虛擬裝置 (AVD) 管理員\) 中的 [Device Definitions] \(裝置定義\) 索引標籤會列出所有可用的裝置定義：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![可用裝置定義的清單](google-emulator-manager-images/win/11-device-definitions-sml.png)](google-emulator-manager-images/win/11-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![可用裝置定義的清單](google-emulator-manager-images/mac/10-device-definitions-sml.png)](google-emulator-manager-images/mac/10-device-definitions.png#lightbox)

-----

您無法修改此清單中預先設定的裝置 &ndash; 只能編輯使用者建立的虛擬裝置。 您可以透過選取裝置定義，然後按一下 [複製]，從預先設定的裝置定義衍生新的裝置定義。 例如，選取 [Nexus 5] 定義，然後按一下 [複製]，會顯示下列對話方塊：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![[Clone device] \(複製裝置\) 對話方塊](google-emulator-manager-images/win/12-clone-device-sml.png)](google-emulator-manager-images/win/12-clone-device.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![[Clone device] \(複製裝置\) 對話方塊](google-emulator-manager-images/mac/11-clone-device-sml.png)](google-emulator-manager-images/mac/11-clone-device.png#lightbox)

-----

在下一個螢幕擷取畫面中，名稱已變更為 **Nexus 5 Custom**，而且裝置參數已修改以建立新的自訂裝置定義：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![自訂 Nexus 5 AVD](google-emulator-manager-images/win/13-custom-nexus.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![自訂 Nexus 5 AVD](google-emulator-manager-images/mac/12-custom-nexus-sml.png)](google-emulator-manager-images/mac/12-custom-nexus.png#lightbox)

-----

按一下 [Clone Device] \(複製裝置\) 會建立新的裝置定義，它現在會出現在 [Device Definitions] \(裝置定義\) 清單中：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Nexus 5 Custom 會顯示為新的使用者裝置定義](google-emulator-manager-images/win/14-new-definition-sml.png)](google-emulator-manager-images/win/14-new-definition.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![Nexus 5 Custom 會顯示為新的使用者裝置定義](google-emulator-manager-images/mac/13-new-definition-sml.png)](google-emulator-manager-images/mac/13-new-definition.png#lightbox)

-----

請注意，每個使用者建立的裝置定義都會與綠色圖示一起顯示，如上所示。 只要選取定義，然後按一下 [建立 AVD]，就可以使用這個新的裝置定義來建立新的 AVD。 這會顯示 [Create new Android Virtual Device (AVD)] \(建立新的 Android 虛擬裝置 (AVD)\) 對話方塊。 在下列範例中，已針對新的虛擬裝置自動產生名稱 **AVD\_for\_Nexus\_5\_Custom**：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![從 Nexus 5 Custom 使用者裝置定義建立 AVD](google-emulator-manager-images/win/15-create-avd-sml.png)](google-emulator-manager-images/win/15-create-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![從 Nexus 5 Custom 使用者裝置定義建立 AVD](google-emulator-manager-images/mac/14-create-avd-sml.png)](google-emulator-manager-images/mac/14-create-avd.png#lightbox)

-----

按一下 [確定] 之後，自訂裝置設定就會顯示在現有 Android 虛擬裝置的清單中。 此外，也會將它新增至裝置下拉式功能表：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![已將新的自訂 AVD 新增至裝置下拉式功能表](google-emulator-manager-images/win/16-new-avd-sml.png)](google-emulator-manager-images/win/16-new-avd.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![已將新的自訂 AVD 新增至裝置下拉式功能表](google-emulator-manager-images/mac/15-new-avd-sml.png)](google-emulator-manager-images/mac/15-new-avd.png#lightbox)

-----

