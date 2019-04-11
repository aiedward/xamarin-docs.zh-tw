---
title: 設定 Xamarin.Android 的 Android SDK
description: Visual Studio 包含 Android SDK 管理員，可用來下載 Android SDK 工具、平台，以及開發 Xamarin.Android 應用程式所需的其他元件。
ms.prod: xamarin
ms.assetid: 9A857F52-2EC1-414F-8010-CEE67B60A4B4
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/09/2018
ms.openlocfilehash: f4ff43ea51dc690daa716e69021f690511cdcc97
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2019
ms.locfileid: "58854050"
---
# <a name="setting-up-the-android-sdk-for-xamarinandroid"></a>設定 Xamarin.Android 的 Android SDK

_Visual Studio 包含 Android SDK 管理員，可用來下載 Android SDK 工具、平台，以及開發 Xamarin.Android 應用程式所需的其他元件。_

## <a name="overview"></a>總覽

本指南說明如何在 Visual Studio 和 Visual Studio for Mac 中使用 Xamarin Android SDK 管理員。

> [!NOTE]
> 本指南適用於 Visual Studio 2019、Visual Studio 2017 和 Visual Studio for Mac。  

Xamarin Android SDK 管理員 (與**使用 .NET 的行動開發**工作負載一併安裝) 可協助您下載開發 Xamarin.Android 應用程式所需的最新 Android 元件。 它會取代 Google 已過時的獨立 SDK 管理員。

# [<a name="visual-studio"></a>Visual Studio](#tab/windows)

## <a name="requirements"></a>需求

若要使用 Xamarin Android SDK 管理員，您需要下列項目：

- Visual Studio 2019 Community、Professional 或 Enterprise。

- 或是 Visual Studio 2017 (Community、Professional 或 Enterprise 版本)。 需要 Visual Studio 2017 15.7 版或更新版本。

- Visual Studio Tools for Xamarin 4.10.0 版或更新版本 (隨著**用 .NET 進行行動裝置開發**工作負載安裝)。 

Xamarin Android SDK 管理員也需要 Java 開發套件 (會隨 Xamarin.Android 自動安裝)。 有多個 JDK 替代項目可供選擇：

-   根據預設，Xamarin.Android 會使用 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)，如果您正在針對 API 層級 24 或更高層級進行開發，則需要此項 (JDK 8 也支援早於 24 的 API 層級)。

-   如果您是針對 API 層級 23 或更早的層級進行開發，則可繼續使用 [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)。

-   若您正在使用 Visual Stuidio 15.8 Preview 5 或更新版本，可嘗試使用 [Microsoft 的 Mobile OpenJDK 散發](openjdk.md) (目前處於預覽階段) 來替代 JDK 8。

> [!IMPORTANT]
> Xamarin.Android 不支援 JDK 9。

 
## <a name="sdk-manager"></a>SDK 管理員 

若要在 Visual Studio 中啟動 SDK 管理員，按一下 [工具] > [Android] > [Android SDK 管理員]：

[![L[Android SDK 管理員] 功能表項目的位置](android-sdk-images/win/02-sdk-manager-menu-item-sml.png)](android-sdk-images/win/02-sdk-manager-menu-item.png#lightbox)

Android SDK 管理員會在 [Android SDKs and Tools] \(Android SDK 與工具\) 畫面中開啟。 此畫面有兩個索引標籤 &ndash; [平台] 和 [工具]：

[![SAndroid SDK 管理員在 [平台] 索引標籤中開啟的螢幕擷取畫面](android-sdk-images/win/03-sdk-manager-platforms-sml.png)](android-sdk-images/win/03-sdk-manager-platforms.png#lightbox)

後續各節將更詳細說明 [Android SDK 及工具] 畫面。


### <a name="android-sdk-location"></a>Android SDK 位置

Android SDK 位置可在 [Android SDKs and Tools] \(Android SDK 與工具\) 畫面頂端設定，如先前的螢幕擷取畫面所示。 您必須先正確設定此位置，[平台] 和 [工具] 索引標籤才能正常運作。 您可能會因為下列一或多個原因，而需要設定 Android SDK 的位置：

1. Android SDK 管理員找不到 Android SDK。 

2. 您已將 Android SDK 安裝於替代 (非預設) 位置。 

若要設定 Android SDK 的位置，請按一下 [Android SDK Location] \(Android SDK 位置\) 最右側的省略符號 (&hellip;) 按鈕。 這會開啟 [瀏覽資料夾] 對話方塊，以用來瀏覽至 Android SDK 位置。 在下列螢幕擷取畫面中，會選取位於 **Program Files (x86)\\Android** 下方的 Android SDK：

![尋找 Android SDK 之 Windows [瀏覽資料夾] 對話方塊的螢幕擷取畫面](android-sdk-images/win/05-browse-for-folder.png)

當您按一下 [確定] 時，SDK 管理員將會管理安裝於所選位置的 Android SDK。


### <a name="tools-tab"></a>[Tools] \(工具\) 索引標籤

[工具] 索引標籤會顯示 [工具] 和 [額外項目] 的清單。 使用此索引標籤來安裝 Android SDK 工具、平台工具及建置工具。
此外，您還能安裝 Android Emulator、低階除錯程式 (LLDB)、NDK、HAXM 加速，以及 Google Play 程式庫。


例如，若要下載 Google Android Emulator 套件，請按一下 [Android Emulator] 旁的核取記號，然後按一下 [套用變更] 按鈕：

[![I從 [工具] 索引標籤安裝 Android Emulator](android-sdk-images/win/06-install-emulator-sml.png)](android-sdk-images/win/06-install-emulator.png#lightbox)

可能會顯示具有以下訊息的對話方塊：_下列套件需要您先接受其授權條款，然後才能安裝_：

![[接受授權] 畫面](android-sdk-images/win/07-license-acceptance.png)

如果您接受條款及條件，請按一下 [接受]。 在視窗底部，進度列會指出下載及安裝的進度。 安裝完成之後，[工具] 索引標籤將會顯示已安裝所選取的工具和額外項目。

### <a name="platforms-tab"></a>[Platforms] \(平台\) 索引標籤

[Platforms] \(平台\) 索引標籤會顯示平台 SDK 版本，以及適用於每個平台之其他資源 (例如系統映像) 的清單：

[![S[平台] 窗格的螢幕擷取畫面](android-sdk-images/win/08-platforms-pane-sml.png)](android-sdk-images/win/08-platforms-pane.png#lightbox)

此畫面列出了 Android 版本 (例如 **Android 8.0**)、代號 (**Oreo**)、API 層級 (例如 **26**)，以及該平台的元件大小 (例如 **1 GB**)。 您可使用 [Platforms] \(平台\) 索引標籤來為您想要設為目標的 Android API 層級安裝元件。 如需 Android 版本與 API 層級的詳細資訊，請參閱 [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md)(了解 Android API 層級)。

當平台的所有元件均安裝完成時，平台名稱旁就會出現一個選取記號。 如果尚未安裝平台的所有元件，則會填滿該平台的方塊。 您可以按一下平台左邊的 **+** 方塊，來展開平台來查看其元件 (以及已安裝哪些元件)。
按一下 **-** 來取消展開平台的元件清單。

若要將其他平台新增至 SDK，請按一下平台旁邊的方塊，直到核取符號出現以安裝其所有元件為止，然後按一下 [套用變更]:

[![E將 Android 7.1 Nougat 元件新增至 Android SDK 的範例](android-sdk-images/win/09-adding-a-platform-sml.png)](android-sdk-images/win/09-adding-a-platform.png#lightbox)

若只要安裝特定元件，請按一下平台旁邊的方塊。 您接著可以選取任何所需的個別元件：

[![E新增部分 Android 7.1 元件的範例](android-sdk-images/win/10-adding-some-components-sml.png)](android-sdk-images/win/10-adding-some-components.png#lightbox)

請注意，要安裝的元件數目會出現在 [套用變更] 按鈕旁邊。 按一下 [Apply Changes] \(套用變更\) 按鈕後，您會看到先前顯示的 [License Acceptance] \(授權接受\) 畫面。
如果您接受條款及條件，請按一下 [接受]。 如果有多個要安裝的元件，您可能會多次看到這個對話方塊。 在視窗底部，進度列將會指出下載及安裝的進度。 當下載及安裝程序完成時 (視需要下載的元件數目而定，這可能需要花費幾分鐘)，系統會以核取記號標示已新增的元件，並將其列為 [已安裝]。

### <a name="respository-selection"></a>存放庫選取項目

根據預設，Android SDK 管理員會從 Microsoft 管理的存放庫下載平台元件與工具。 若您需要存取 Microsoft 存放庫中尚無法使用的實驗性 alpha/beta 平台與元件，您可以切換 SDK 管理員為使用 Google 的存放庫。 若要進行此切換，請按一下右下角的齒輪圖示，並選取 [Repository] > [Google (Unsupported)] \([存放庫] > [Google (不支援)]\)：

[![S選取 Google 的存放庫](android-sdk-images/win/11-google-repo-w157-sml.png)](android-sdk-images/win/11-google-repo-w157.png#lightbox)

選取 Google 存放庫時，先前無法使用的其他套件可能會出現在 [Platforms] \(平台\) 索引標籤中。 (在上方的螢幕擷取畫面中，切換至 Google 存放庫後就新增了 **Android SDK Platform 28**。)請記得使用 Google 存放庫並不受支援，因此不建議在平日的開發中使用。

若要切換回支援的平台與工具存放庫，請按一下 Microsoft (Recommended) \(Microsoft (建議)\)。 這會將套件與工具的清單還原至預設選取項目。


# [<a name="visual-studio-for-mac"></a>Visual Studio for Mac](#tab/macos)

## <a name="requirements"></a>需求

若要使用 Xamarin Android SDK 管理員，您需要下列項目：

-   Visual Studio for Mac 7.5 (或更新版本)。

Xamarin Android SDK 管理員也需要 Java 開發套件 (會隨 Xamarin.Android 自動安裝)。 有多個 JDK 替代項目可供選擇：

-   根據預設，Xamarin.Android 會使用 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)，如果您正在針對 API 層級 24 或更高層級進行開發，則需要此項 (JDK 8 也支援早於 24 的 API 層級)。

-   如果您是針對 API 層級 23 或更早的層級進行開發，則可繼續使用 [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)。

-   若您正在使用 Visual Studio for Mac 7.7 或更新版本，可嘗試使用 [Microsoft 的 Mobile OpenJDK 散發](openjdk.md) (目前處於預覽階段) 來替代 JDK 8。

> [!IMPORTANT]
> Xamarin.Android 不支援 JDK 9。
 
## <a name="sdk-manager"></a>SDK 管理員 

若要在 Visual Studio for Mac 中啟動 SDK 管理員，按一下 [工具] > [SDK 管理員]：
 
[![L[Android SDK 管理員] 功能表項目的位置](android-sdk-images/mac/01-sdk-manager-menu-item-m75-sml.png)](android-sdk-images/mac/01-sdk-manager-menu-item-m75.png#lightbox)

**Android SDK 管理員** 會在[喜好設定] 視窗中開啟，該視窗會包含三個索引標籤：[平台]、[工具] 和 [位置]：

[![SAndroid SDK 管理員在 [平台] 索引標籤中開啟的螢幕擷取畫面](android-sdk-images/mac/02-sdk-manager-platforms-m75-sml.png)](android-sdk-images/mac/02-sdk-manager-platforms-m75.png#lightbox)

後續各節將說明 Android SDK 管理員的索引標籤。


### <a name="locations-tab"></a>[Locations] \(位置\) 索引標籤

[位置] 索引標籤有三種設定，可用來設定 Android SDK、Android NDK 及 Java SDK (JDK) 的位置。 您必須先正確設定這些位置，[平台] 和 [工具] 索引標籤才能正常運作。

當 SDK 管理員啟動時，它會自動判斷每個安裝套件的路徑，並藉由在路徑旁邊放置綠色核取記號圖示來指出**已找到**它：

[![S[位置] 索引標籤的螢幕擷取畫面](android-sdk-images/mac/03-locations-tab-m75-sml.png)](android-sdk-images/mac/03-locations-tab-m75.png#lightbox)

按一下 [重設為預設] 按鈕，讓 SDK 管理員在 SDK、NDK 及 JDK 的預設位置上搜尋它們。 

通常，您會使用 [位置] 索引標籤來修改 Android SDK 和/或 Java JDK 的位置。 您不需要安裝 NDK 來開發 Xamarin.Android 應用程式 &ndash; 唯有當您需要使用機器碼語言 (例如 C 和 C++) 來開發部分應用程式時，才需使用 NDK。

### <a name="tools-tab"></a>[Tools] \(工具\) 索引標籤

[工具] 索引標籤會顯示 [工具] 和 [額外項目] 的清單。 使用此索引標籤來安裝 Android SDK 工具、平台工具及建置工具。
此外，您還能安裝 Android Emulator、低階除錯程式 (LLDB)、NDK、HAXM 加速，以及 Google Play 程式庫。

例如，若要下載 Google Android Emulator 套件，請按一下 [Android Emulator] 旁的核取記號，然後按一下 [套用變更] 按鈕：

[![I從 [工具] 索引標籤安裝 Android Emulator](android-sdk-images/mac/04-tools-tab-m75-sml.png)](android-sdk-images/mac/04-tools-tab-m75.png#lightbox)

可能會顯示具有以下訊息的對話方塊：_下列套件需要您先接受其授權條款，然後才能安裝_：

[![L[接受授權] 畫面](android-sdk-images/mac/05-license-acceptance-m75-sml.png)](android-sdk-images/mac/05-license-acceptance-m75.png#lightbox)

如果您接受條款及條件，請按一下 [接受]。 在視窗底部，進度列會指出下載及安裝的進度。 安裝完成之後，[工具] 索引標籤將會顯示已安裝所選取的工具和額外項目。


### <a name="platforms-tab"></a>[Platforms] \(平台\) 索引標籤

[Platforms] \(平台\) 索引標籤會顯示平台 SDK 版本，以及適用於每個平台之其他資源 (例如系統映像) 的清單：

[![S[平台] 窗格的螢幕擷取畫面](android-sdk-images/mac/06-platforms-tab-m75-sml.png)](android-sdk-images/mac/06-platforms-tab-m75.png#lightbox)

此畫面列出了 Android 版本 (例如 **Android 8.1**)、代號 (**Oreo**)、API 層級 (例如 **27**)，以及該平台的元件大小 (例如 **1 GB**)。 您可使用 [Platforms] \(平台\) 索引標籤來為您想要設為目標的 Android API 層級安裝元件。 如需 Android 版本與 API 層級的詳細資訊，請參閱 [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md)(了解 Android API 層級)。

當平台的所有元件均安裝完成時，平台名稱旁就會出現一個選取記號。 如果尚未安裝平台的所有元件，則會填滿該平台的方塊。 您可以按一下平台左邊的**箭頭**，來展開平台來查看其元件 (以及已安裝哪些元件)。
按一下 **向下箭頭** 來取消展開平台的元件清單。

若要將其他平台新增至 SDK，請按一下平台旁邊的方塊，直到核取符號出現以安裝其所有元件為止，然後按一下 [套用變更]:

[![E新增所有平台元件的範例](android-sdk-images/mac/07-install-all-m75-sml.png)](android-sdk-images/mac/07-install-all-m75.png#lightbox)

若只要安裝部分元件，請按一下平台旁邊的方塊。 您接著可以選取任何所需的個別元件：

[![E新增部分元件的範例](android-sdk-images/mac/08-individual-components-m75-sml.png)](android-sdk-images/mac/08-individual-components-m75.png#lightbox)

請注意，要安裝的元件數目會出現在 [套用變更] 按鈕旁邊。 按一下 [Apply Changes] \(套用變更\) 按鈕後，您會看到先前顯示的 [License Acceptance] \(授權接受\) 畫面。
如果您接受條款及條件，請按一下 [接受]。 如果有多個要安裝的元件，您可能會多次看到這個對話方塊。 在視窗底部，進度列將會指出下載及安裝的進度。 當下載及安裝程序完成時 (視需要下載的元件數目而定，這可能需要花費幾分鐘)，系統會以核取記號標示已新增的元件，並將其列為 [已安裝]。

### <a name="respository-selection"></a>存放庫選取項目

根據預設，Android SDK 管理員會從 Microsoft 管理的存放庫下載平台元件與工具。 若您需要存取 Microsoft 存放庫中尚無法使用的實驗性 alpha/beta 平台與元件，您可以切換 SDK 管理員為使用 Google 的存放庫。 若要進行此切換，請按一下右下角的齒輪圖示，並選取 [Repository] > [Google (Unsupported)] \([存放庫] > [Google (不支援)]\)：

[![S選取 Google 的存放庫](android-sdk-images/mac/09-google-repo-m75-sml.png)](android-sdk-images/mac/09-google-repo-m75.png#lightbox)

選取 Google 存放庫時，先前無法使用的其他套件可能會出現在 [Platforms] \(平台\) 索引標籤中。 (在上方的螢幕擷取畫面中，切換至 Google 存放庫後就新增了 **Android SDK Platform 28**。)請記得使用 Google 存放庫並不受支援，因此不建議在平日的開發中使用。

若要切換回支援的平台與工具存放庫，請按一下 Microsoft (Recommended) \(Microsoft (建議)\)。 這會將套件與工具的清單還原至預設選取項目。

-----

 
## <a name="summary"></a>總結

本指南說明如何在 Visual Studio 和 Visual Studio for Mac 中安裝和使用 Xamarin Android SDK 管理員工具。


## <a name="related-links"></a>相關連結

- [了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)
- [對於 Android SDK 工具所做的變更](~/android/troubleshooting/sdk-cli-tooling-changes.md)
