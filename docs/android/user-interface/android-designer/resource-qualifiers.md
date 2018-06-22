---
title: 資源限定詞和視覺效果選項
description: 本主題說明如何定義某些辨識符號值進行比對時，才會使用的資源。 簡單的範例是語言限定字串資源。 與其他替代資源定義要用於其他語言，可以做為預設值，定義字串資源。 可以限定的所有資源類型，包括版面配置本身。
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 01/29/2018
ms.openlocfilehash: bc9eb145b6d9ed7bd441d625f533c5cbbd87fccd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30771888"
---
# <a name="resource-qualifiers-and-visualization-options"></a>資源限定詞和視覺效果選項

_本主題說明如何定義某些辨識符號值進行比對時，才會使用的資源。簡單的範例是語言限定字串資源。與其他替代資源定義要用於其他語言，可以做為預設值，定義字串資源。可以限定的所有資源類型，包括版面配置本身。_


## <a name="custom-device-configurations"></a>自訂裝置設定

Android 裝置和螢幕解析度的功能。
為了設計使用者介面的許多裝置為目標，在設計工具隨附於各種不同的內建的裝置組態。 它也支援加入其他裝置組態。這些設定根據*限定詞*您指定區分從另一個裝置設定。 有許多不同類型的限定詞。 如需有關這些資源類型的詳細資訊，請參閱[Android 資源](~/android/app-fundamentals/resources-in-android/index.md)。

底部的裝置選取器功能表是**自訂**選項如下所示：


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![裝置選取器功能表](resource-qualifiers-images/vs/01-device-selector-sml.png)](resource-qualifiers-images/vs/01-device-selector.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![裝置選取器功能表](resource-qualifiers-images/xs/01-device-selector-sml.png)](resource-qualifiers-images/xs/01-device-selector.png#lightbox)

-----


選取**自訂**會顯示一個對話方塊，您可以使用瀏覽可用的裝置組態。 當您按一下**裝置定義** 索引標籤會顯示所有已知的裝置定義的清單：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![AVD Manager](resource-qualifiers-images/vs/02-device-definitions-sml.png)](resource-qualifiers-images/vs/02-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![AVD Manager](resource-qualifiers-images/xs/02-device-definitions-sml.png)](resource-qualifiers-images/xs/02-device-definitions.png#lightbox)

-----


無法修改設計工具中預先設定的裝置。 不過，您可以按一下**建立裝置...** 定義自訂裝置定義。 或者，您可以選取現有的定義，然後按一下**複製...** 以作為新定義的起始點。
例如，選取**Nexus 5**定義，然後按一下**複製...** 顯示下列對話方塊：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![複製裝置](resource-qualifiers-images/vs/03-clone-sml.png)](resource-qualifiers-images/vs/03-clone.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![複製裝置](resource-qualifiers-images/xs/03-clone-sml.png)](resource-qualifiers-images/xs/03-clone.png#lightbox)

-----


在下一個螢幕擷取畫面中，將名稱變更為**Nexus 5 自訂**，若要建立新的自訂裝置定義做修改之裝置參數。 在此範例中，**縱向**使裝置定義為僅限橫向已停用：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![自訂的裝置](resource-qualifiers-images/vs/04-custom-sml.png)](resource-qualifiers-images/vs/04-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![自訂的裝置](resource-qualifiers-images/xs/04-custom-sml.png)](resource-qualifiers-images/xs/04-custom.png#lightbox)

-----


按一下 [Clone Device] \(複製裝置\) 會建立新的裝置定義，它現在會出現在 [Device Definitions] \(裝置定義\) 清單中：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![定義更新的裝置](resource-qualifiers-images/vs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/vs/05-updated-device-definitions.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![定義更新的裝置](resource-qualifiers-images/xs/05-updated-device-definitions-sml.png)](resource-qualifiers-images/xs/05-updated-device-definitions.png#lightbox)

-----


請注意每個使用者建立裝置定義會顯示有綠色圖示，如上所示。 當您返回**裝置**選取器功能表上，新的自訂裝置定義中會顯示清單的最頂部區段 （如果看不到自訂裝置組態設定，此清單中，請嘗試重新啟動 IDE）：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![自訂的裝置會出現在裝置清單](resource-qualifiers-images/vs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/vs/06-nexus-5-custom.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![自訂的裝置會出現在裝置清單](resource-qualifiers-images/xs/06-nexus-5-custom-sml.png)](resource-qualifiers-images/xs/06-nexus-5-custom.png#lightbox)

-----


選取此裝置的設定會修改以符合稍早 （在此種情況下，僅限橫向模式） 所建立的自訂版面配置：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在使用自訂的裝置](resource-qualifiers-images/vs/07-custom-in-use-sml.png)](resource-qualifiers-images/vs/07-custom-in-use.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在使用自訂的裝置](resource-qualifiers-images/xs/07-custom-in-use-sml.png)](resource-qualifiers-images/xs/07-custom-in-use.png#lightbox)

-----



## <a name="resource-qualifier-options"></a>資源限定詞選項

**資源限定詞選項**可以按一下右邊的向下三角形圖示，即可存取**裝置組態**選項：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![資源限定詞選項](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![資源限定詞選項](resource-qualifiers-images/xs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt.png#lightbox)

-----


這個對話方塊會出現下拉功能表下的資源限定詞：

-   **語言**&ndash;顯示可用的語言資源，並提供一個選項以加入新的語言/地區資源。

-   **UI 模式下**&ndash;清單顯示模式 (例如**汽車停駐**和**服務台停駐**) 以及版面配置方向。

每個這些下拉功能表隨即開啟新對話方塊中，您可以在其中選取並設定資源限定詞 （如下所述旁邊）。



### <a name="language"></a>語言

**語言**下拉式功能表列出只有具有資源定義的語言 (或**所有語言**，預設值)。 不過，另外還有**新增語言/地區...** 選項，可讓您將新的語言新增至清單：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![新增語言/地區](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![新增語言/地區](resource-qualifiers-images/xs/09-add-language-region-sml.png)](resource-qualifiers-images/xs/09-add-language-region.png#lightbox)

-----


當您按一下**新增語言/地區...**、**選取語言**對話方塊會開啟以顯示可用的語言和區域的下拉式清單：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![語言清單](resource-qualifiers-images/vs/10-languages.png "的語言清單")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![語言的清單](resource-qualifiers-images/xs/10-languages-sml.png)](resource-qualifiers-images/xs/10-languages.png#lightbox)

-----


在此範例中，我們選擇**fr （法文）** 語言和**相當**（比利時） 為法文的地區的方言。 請注意，**區域**因為許多語言可以指定特定地區，則不管是選擇性欄位。 當**語言**下拉功能表再次開啟時，便會顯示新增的語言/地區資源：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![語言和地區選擇](resource-qualifiers-images/vs/11-language-region-added.png "語言和地區的選擇")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![語言和地區選擇](resource-qualifiers-images/xs/11-language-region-added-sml.png)](resource-qualifiers-images/xs/11-language-region-added.png#lightbox)

-----


請注意，是否您新增新的語言，但並不會建立新資源對其加入的語言將不再顯示在下一次您開啟專案。



### <a name="ui-mode"></a>UI 模式下

當您按一下**UI 模式下**顯示的下拉式功能表，模式的清單，例如**一般**，**汽車停駐**，**服務台停駐**， **電視**，**應用裝置**，和**監看式**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![UI 模式 功能表](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

下面這份清單是晚上模式**不晚**和**晚上**，後面接著的版面配置指示**由左到右**和**由右至左**（適用於資訊**由左到右**和**由右至左**選項，請參閱[LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/)。
中的最後一個項目**資源限定詞選項**對話方塊是**四捨五入螢幕**（適用於 Android 戴上） 或**捨入螢幕**(有關循和非循畫面，請參閱[配置](https://developer.android.com/training/wearables/ui/layouts.html))。
如需 Android UI 模式的詳細資訊，請參閱[UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![UI 模式 功能表](resource-qualifiers-images/xs/12-ui-mode-sml.png)](resource-qualifiers-images/xs/12-ui-mode.png#lightbox)

下面這份清單是晚上模式**不晚**和**晚上**，後面接著的版面配置指示**由左到右**和**由右至左**。 如需 Android UI 模式的詳細資訊，請參閱[UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/)。
如需有關資訊**由左到右**和**由右至左**選項，請參閱[LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/)。

### <a name="round-screen"></a>Round 螢幕

中的最後一個項目**資源限定詞選項**對話是**Round 螢幕**功能表。 此功能表可讓您選取 **四捨五入螢幕**（適用於 Android 戴上） 或**矩形螢幕**:

[![Round 螢幕功能表](resource-qualifiers-images/xs/13-round-screen-sml.png)](resource-qualifiers-images/xs/13-round-screen.png#lightbox)

-----



## <a name="action-bar-settings"></a>動作列設定

**動作列設定**（佈景主題編輯器） 的畫刷圖示左邊的圖示可：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![動作列設定](resource-qualifiers-images/vs/14-action-bar.png "動作列設定")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![動作列設定](resource-qualifiers-images/xs/13b-action-bar-sml.png)](resource-qualifiers-images/xs/13b-action-bar.png#lightbox)

-----


此圖示會開啟對話方塊 popover 提供方式選取其中三個動作列模式：

-   **標準**&ndash;任一個有選擇性的子標題標誌或圖示和標題文字所組成。

-   **清單**&ndash;清單瀏覽模式。 而不是靜態的標題文字，此模式會顯示在活動內的瀏覽的清單功能表 （也就是它可以向使用者顯示為下拉式清單）。

-   **索引標籤** &ndash;  索引標籤瀏覽模式。 而不是靜態的標題文字，此模式會提供一系列活動內的瀏覽 索引標籤。



## <a name="themes"></a>主題

**佈景主題**下拉式功能表會顯示所有專案中定義的主題。 選取**多主題**會開啟一個對話方塊可從已安裝的 Android SDK，所有佈景主題的清單，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![其他主題清單](resource-qualifiers-images/vs/15-theme-menu-sml.png "多主題清單")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![其他主題清單](resource-qualifiers-images/xs/14-theme-menu-sml.png)](resource-qualifiers-images/xs/14-theme-menu.png#lightbox)

-----


選取的佈景主題時，設計介面會更新以顯示新的佈景主題的效果。 請注意這項變更為了永久才**確定**中按下按鈕**佈景主題**對話方塊。 一旦選取的佈景主題，它將會包含在**佈景主題**下拉式選單，如所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![淺色佈景主題現已提供](resource-qualifiers-images/vs/16-light-theme.png "淺色佈景主題目前為可用")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![淺色佈景主題目前為可用](resource-qualifiers-images/xs/15-light-theme-sml.png)](resource-qualifiers-images/xs/15-light-theme.png#lightbox)

-----



## <a name="android-version"></a>Android 版本

在 Android**版本**選取器設定的 Android 版本，用來呈現在設計工具中的配置。 選取器會顯示與專案的目標 framework 版本相容的所有版本：


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Android 版本清單](resource-qualifiers-images/vs/17-android-version.png "Android 清單版本")

可設定 下的專案設定中的目標 framework 版本**屬性 > 應用程式 > 使用 Android 版本編譯**。 如需目標 framework 版本的詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。

工具箱中可用的 widget 集合取決於專案的目標 framework 版本。 這也適用於中可用的屬性**屬性 視窗**。 可用的 widget 清單是*不*中選取值來決定**版本**工具列的選取器。 比方說，如果您設定專案的目標版本為 Android 4.4，您仍可以選取 Android 6.0 中工具列版本選取器，以查看專案在 Android 6.0 中的外觀，但無法新增小工具，專屬於 Android 6.0 &ndash; 此外，您仍會限制為 Android 4.4 中可用的 widget。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![Android 版本清單](resource-qualifiers-images/xs/16-android-version-sml.png)](resource-qualifiers-images/xs/16-android-version.png#lightbox)

可設定 下的專案設定中的目標 framework 版本**專案選項 > 建置 > 一般**> 一節。 如需目標 framework 版本的詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。

工具箱中可用的 widget 集合取決於專案的目標 framework 版本。 這也適用於中可用的屬性**屬性板**。 可用的 widget 清單是*不*中選取值來決定**版本**工具列的選取器。 比方說，如果您設定專案的目標版本為 Android 4.4，您仍可以選取 Android 6.0 中工具列版本選取器，以查看專案在 Android 6.0 中的外觀，但無法新增小工具，專屬於 Android 6.0 &ndash; 此外，您仍會限制為 Android 4.4 中可用的 widget。

-----
