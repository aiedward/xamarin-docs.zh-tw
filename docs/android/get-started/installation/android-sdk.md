---
title: 設定 Xamarin.Android 的 Android SDK
description: Visual Studio 包含會取代 Google 獨立 SDK 管理員的 Android SDK 管理員。 本指南將說明如何使用 SDK 管理員來下載 Android SDK 工具、平台，以及開發 Xamarin.Android 應用程式所需的其他元件。
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/22/2018
ms.openlocfilehash: 6a3f3f79e81339cc903d85081ca173a7ac707f6a
ms.sourcegitcommit: 26033c087f49873243751deded8037d2da701655
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/25/2018
ms.locfileid: "36935473"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>設定 Xamarin.Android 的 Android SDK

_Visual Studio 包含會取代 Google 獨立 SDK 管理員的 Android SDK 管理員。本指南將說明如何使用 SDK 管理員來下載 Android SDK 工具、平台，以及開發 Xamarin.Android 應用程式所需的其他元件。_


## <a name="overview"></a>總覽

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

本指南將說明如何安裝和使用適用於 Windows (或[適用於 Mac](?tabs=vsmac)) 之 Visual Studio 的 Xamarin Android SDK 管理員。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

本指南將說明如何安裝和使用適用於 Mac (或[適用於 Windows](?tabs=vswin)) 之 Visual Studio 的 Xamarin Android SDK 管理員。

> [!NOTE]
> 本指南僅適用於 Visual Studio 2017 和 Visual Studio for Mac。  

-----

Xamarin Android SDK 管理員可協助您下載開發 Xamarin.Android 應用程式所需的最新 Android 元件。
它會取代 Google 已過時的獨立 SDK 管理員。

使用 Xamarin Android SDK 管理員，而非 Android SDK 隨附之 SDK 管理員的理由為何？ 在 Android SDK 工具套件的 25.2.3 版中，Google 導入了新工具來維護 Android SDK。 這項新工具 (**[sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)** \(英文\)) 是一個命令列公用程式，會取代適用於 Android SDK 的獨立 UI 管理員。 因此，如果您已更新為 SDK 工具 26.0.1 版 (Android 8.0 所需的版本) 或更新版本，而且想要繼續透過 UI 介面管理 Android SDK，則您必須使用 Xamarin Android SDK 管理員。

## <a name="requirements"></a>需求

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要使用 Xamarin Android SDK 管理員，您需要下列項目：

- Visual Studio 2017 (Community、Professional 或 Enterprise 版本)。 需要 Visual Studio 2017 15.5 版或更新版本。

- Visual Studio Tools for Xamarin 4.5.0 版或更新版本。 

Xamarin Android SDK 管理員不相容於 Visual Studio
2015. Visual Studio 2015 的使用者應該使用由 Google 在 Android SDK 中所提供的 SDK 管理員工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

-   Visual Studio for Mac 7.0.0.3146 (或更新版本)。

-----

Xamarin Android SDK 管理員也需要 Java 開發套件 (會隨 Xamarin.Android 自動安裝)。
Xamarin.Android 會使用 [JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) \(英文\)，如果您正在針對 API 層級 24 或更高層級進行開發，則需要此項 (JDK 8 也支援早於 24 的 API 層級)。 如果您是針對 API 層級 23 或更早的層級進行開發，則可繼續使用 [JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)。

> [!IMPORTANT]
> Xamarin.Android 不支援 JDK 9。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="installation"></a>安裝

Xamarin SDK 管理員可以在安裝期間新增至 Visual Studio 2017。 當您安裝 Visual Studio 時，按一下 [個別元件]，然後向下捲動至 [開發活動] 區段。
啟用 [Xamarin SDK 管理員] (如果尚未選取)：

![從 [個別元件] 啟用 Xamarin SDK 管理員](android-sdk-images/win/01-sdk-manager-install.png)

如果您已經安裝 Visual Studio 2017，請參閱[修改 Visual Studio 2017](https://docs.microsoft.com/en-us/visualstudio/install/modify-visual-studio) 以取得如何修改 Visual Studio 的指示，然後遵循上述程序來啟用 Xamarin SDK 管理員。 如果您看到更新 SDK 管理員的提示，您可以使用這個相同程序來安裝 Xamarin SDK 管理員。

當您按一下 [工具] > [Android] > [Android SDK 管理員] (如下所述) 時，將會啟動 Xamarin Android SDK 管理員，而非 Google Android SDK 管理員。 如果您使用支援 Google 獨立 Android SDK 管理員的舊版 Android SDK，安裝 Xamarin Android SDK 管理員將不會產生衝突 &ndash; 您仍可從 Visual Studio 外部啟動獨立的 Google SDK 管理員來管理 Android SDK。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)
 
-----

 
## <a name="sdk-manager"></a>SDK 管理員 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

若要在 Visual Studio 中啟動 SDK 管理員，按一下 [工具] > [Android] > [Android SDK 管理員]：

[![[Android SDK 管理員] 功能表項目的位置](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

[Xamarin Android SDK 管理員] 會在 [Android SDK 及工具] 畫面中開啟。 此畫面有兩個索引標籤 &ndash; [平台] 和 [工具]：

[![Android SDK 管理員在 [平台] 索引標籤中開啟的螢幕擷取畫面](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

後續各節將更詳細說明 [Android SDK 及工具] 畫面。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

若要在 Visual Studio for Mac 中啟動 SDK 管理員，按一下 [工具] > [SDK 管理員]：
 
![[Android SDK 管理員] 功能表項目的位置](android-sdk-images/mac/sdkmanager-01.png )

**Android SDK 管理員** 會在[喜好設定] 視窗中開啟，該視窗會包含三個索引標籤：[平台]、[工具] 和 [位置]：

![Android SDK 管理員在 [平台] 索引標籤中開啟的螢幕擷取畫面](android-sdk-images/mac/sdkmanager-02.png)

後續各節將說明 Xamarin Android SDK 管理員的索引標籤。

-----



# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

### <a name="android-sdk-location"></a>Android SDK 位置

Android SDK 位置是在 [Android SDK 及工具] 畫面頂端設定，如上圖所示。 您必須先正確設定此位置，[平台] 和 [工具] 索引標籤才能正常運作。 您可能會因為下列一或多個原因，而需要設定 Android SDK 的位置：

1. Xamarin SDK 管理員找不到 Android SDK。 

2. 您已將 Android SDK 安裝於替代 (非預設) 位置。 

若要設定 Android SDK 的位置，請按一下 [Android SDK 位置] 最右側的 &hellip; 按鈕。 這會開啟 [瀏覽資料夾] 對話方塊，以用來瀏覽至 Android SDK 位置。 在下列螢幕擷取畫面中，會選取位於 **Program Files (x86)\\Android** 下方的 Android SDK：

![尋找 Android SDK 之 Windows [瀏覽資料夾] 對話方塊的螢幕擷取畫面](android-sdk-images/win/05-browse-for-folder.png)

當您按一下 [確定] 時，Xamarin Android SDK 管理員將會管理安裝於所選取位置的 Android SDK。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

### <a name="locations-tab"></a>[位置] 索引標籤

[位置] 索引標籤有三種設定，可用來設定 Android SDK、Android NDK 及 Java SDK (JDK) 的位置。 您必須先正確設定這些位置，[平台] 和 [工具] 索引標籤才能正常運作。

當 SDK 管理員啟動時，它會自動判斷每個安裝套件的路徑，並藉由在路徑旁邊放置綠色核取記號圖示來指出**已找到**它：

![[位置] 索引標籤的螢幕擷取畫面](android-sdk-images/mac/sdkmanager-03.png)

按一下 [重設為預設] 按鈕，讓 SDK 管理員在 SDK、NDK 及 JDK 的預設位置上搜尋它們。 

通常，您會使用 [位置] 索引標籤來修改 Android SDK 和/或 Java JDK 的位置。 您不需要安裝 NDK 來開發 Xamarin.Android 應用程式 &ndash; 唯有當您需要使用機器碼語言 (例如 C 和 C++) 來開發部分應用程式時，才需使用 NDK。

-----


### <a name="tools-tab"></a>[工具] 索引標籤

[工具] 索引標籤會顯示 [工具] 和 [額外項目] 的清單。 使用此索引標籤來安裝 Android SDK 工具、平台工具及建置工具。
此外，您還能安裝 Android Emulator、低階除錯程式 (LLDB)、NDK、HAXM 加速，以及 Google Play 程式庫。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

例如，若要下載 Google Android Emulator 套件，請按一下 [Android Emulator] 旁的核取記號，然後按一下 [套用變更] 按鈕：

[![從 [工具] 索引標籤安裝 Android Emulator](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

例如，若要下載 Google Android Emulator 套件，請按一下 [Android Emulator] 旁的核取記號，然後按一下 [安裝更新] 按鈕：

![從 [工具] 索引標籤安裝 Android Emulator](android-sdk-images/mac/sdkmanager-08.png)

-----


系統可能會顯示對話方塊並含有下列訊息：「有可更新的元件。是否要立即更新它們？」 按一下 [ **是**]。 接下來會顯示 [接受授權] 對話方塊：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![[接受授權] 畫面](android-sdk-images/win/07-license-acceptance.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![[接受授權] 畫面](android-sdk-images/mac/sdkmanager-09.png)

-----

如果您接受條款及條件，請按一下 [接受]。 在視窗底部，進度列會指出下載及安裝的進度。 安裝完成之後，[工具] 索引標籤將會顯示已安裝所選取的工具和額外項目。



### <a name="platforms-tab"></a>[平台] 索引標籤

[平台] 索引標籤會顯示平台 SDK 版本，以及適用於每個平台之其他資源 (例如系統映像) 的清單。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![[平台] 窗格的螢幕擷取畫面](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![[平台] 窗格的螢幕擷取畫面](android-sdk-images/mac/sdkmanager-11.png)

-----

這個畫面會列出 Android 版本 (例如 **Android 7.0**)、代號 (**Nougat**)、API 層級 (例如 **24**) 及狀態 (若已安裝平台則為 [已安裝])。 您會使用 [平台] 索引標籤來安裝目標 Android API 層級的元件 (如需 Android 版本和 API 層級的詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md))。

如果已安裝平台的所有元件，平台名稱旁就會出現一個核取記號。 如果尚未安裝平台的所有元件，則會填滿該平台的方塊。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

您可以按一下平台左邊的 **+** 方塊，來展開平台來查看其元件 (以及已安裝哪些元件)。
按一下 **-** 來取消展開平台的元件清單。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

您可以按一下平台左邊的**箭頭**，來展開平台來查看其元件 (以及已安裝哪些元件)。
按一下 **向下箭頭** 來取消展開平台的元件清單。

-----

若要將其他平台新增至 SDK，請按一下平台旁邊的方塊，直到核取符號出現以安裝其所有元件為止，然後按一下 [套用變更]:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![將 Android 7.1 Nougat 元件新增至 Android SDK 的範例](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![將 Android 4.4 元件新增至 Android SDK 的範例](android-sdk-images/mac/sdkmanager-12.png)

-----

若只要安裝 SDK，請按一下平台旁邊的方塊一次。 您接著可以選取任何所需的個別元件：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![新增部分 Android 7.1 元件的範例](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![新增部分 Android 4.4 元件的範例](android-sdk-images/mac/sdkmanager-13.png)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

請注意，要安裝的元件數目會出現在 [套用變更] 按鈕旁邊。 在上述範例中，已準備好安裝六個元件。 按一下 [套用變更] 按鈕之後，您將會看到 [接受授權] 畫面：

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

請注意，要安裝的元件數目會出現在 [套用變更] 按鈕旁邊。 按一下 [套用變更] 按鈕之後，您將會看到 [接受授權] 畫面：

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![[平台] 索引標籤的 [接受授權] 對話方塊](android-sdk-images/win/11-license-screen.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![[平台] 索引標籤的 [接受授權] 對話方塊](android-sdk-images/mac/sdkmanager-14.png)

-----

如果您接受條款及條件，請按一下 [接受]。 如果有多個要安裝的元件，您可能會多次看到這個對話方塊。 在視窗底部，進度列將會指出下載及安裝的進度。 當下載及安裝程序完成時 (視需要下載的元件數目而定，這可能需要花費幾分鐘)，系統會以核取記號標示已新增的元件，並將其列為 [已安裝]。

現在您已經準備好開發適用於最新、最大 Android API 層級的應用程式！


 
## <a name="summary"></a>總結

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

本指南已說明如何在 Visual Studio 中安裝和使用 Xamarin Android SDK 管理員工具。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

本指南已說明如何在 Visual Studio for Mac 中使用 Xamarin Android SDK 管理員工具。

-----


## <a name="related-links"></a>相關連結

- [對 Android SDK 工具所做的變更](~/android/troubleshooting/sdk-cli-tooling-changes.md)
- [了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)
- [sdkmanager](https://developer.android.com/studio/command-line/sdkmanager.html)
- [avdmanager](https://developer.android.com/studio/command-line/avdmanager.html)
