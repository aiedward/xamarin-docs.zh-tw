---
title: 資源限定詞和視覺效果選項
description: 本主題說明如何定義只有在一些限定詞的值進行比對時，才會使用的資源。 簡單的範例是語言限定字串資源。 與其他替代的資源，定義要用於其他語言，可以為預設值，定義的字串資源。 所有資源類型可以都限定，包括本身的版面配置。
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 9d99e6a59b57b59d585b32befdadc0890d41448c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60950609"
---
# <a name="resource-qualifiers-and-visualization-options"></a>資源限定詞和視覺效果選項

_本主題說明如何定義只有在一些限定詞的值進行比對時，才會使用的資源。簡單的範例是語言限定字串資源。與其他替代的資源，定義要用於其他語言，可以為預設值，定義的字串資源。所有資源類型可以都限定，包括本身的版面配置。_


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="resource-qualifier-options"></a>資源限定詞選項

**資源限定詞選項**可以按一下右邊的省略符號圖示來存取**橫向**模式按鈕：

[![資源限定詞選項](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

此對話方塊會出現下拉功能表下的資源限定詞：

-   **語言**&ndash;顯示可用的語言資源，並提供選項來加入新的語言/地區資源。

-   **UI 模式**&ndash;清單顯示模式 (例如**汽車擴充座**並**桌面擴充座**) 以及版面配置方向。

每個這些下拉功能表隨即開啟新對話方塊一樣，您可以在其中選取並設定資源限定詞 （如下所述）。

### <a name="language"></a>語言

**語言**下拉功能表列出只有在已定義資源的語言 (或**所有語言**，這是預設值)。 不過，另外還有**新增語言/地區...** 選項，可讓您將新的語言新增至清單：

[![新增語言/地區](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

當您按一下**新增語言/地區...**，則**選取語言**對話方塊隨即開啟，顯示的可用語言和地區的下拉式清單：

![語言的清單](resource-qualifiers-images/vs/10-languages.png "的語言清單")

在此範例中，我們已選擇**fr （法文）** 語言並**BE** （比利時） 為法文的地區方言。 請注意，**地區**欄位是選擇性的因為可以指定許多語言，而不必考慮特定區域。 當**語言**下拉式功能表會再次開啟，它會顯示新增語言/地區資源：

![語言和地區選擇](resource-qualifiers-images/vs/11-language-region-added.png "語言和地區的選擇")

請注意，是否您新增新的語言，但您不要建立新的資源，為它加入的語言將不再顯示在下一次您開啟專案。

### <a name="ui-mode"></a>UI 模式

當您按一下  **UI 模式下**顯示的下拉式功能表，模式的清單，例如**Normal**，**汽車擴充座**，**桌面擴充座**， **電視**，**設備**，和**監看式**:


[![UI 模式 功能表](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

此清單下方是 夜間模式**不晚**並**晚上**，後面接著的版面配置指示**由左到右**和**由右至左**（適用於相關資訊**由左到右**並**由右至左**選項，請參閱[LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/))。
中的最後一個項目**資源限定詞選項** 對話方塊會**四捨五入畫面**（適用於 Android Wear） 或**非圓螢幕**。
Round 和非循畫面的相關資訊，請參閱[版面配置](https://developer.android.com/training/wearables/ui/layouts.html)。
如需 Android UI 模式的詳細資訊，請參閱[UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/)。

## <a name="action-bar-settings"></a>動作列設定

**動作列設定**圖示位於左側的 （佈景主題編輯器） 的畫刷圖示：

![動作列設定](resource-qualifiers-images/vs/14-action-bar.png "動作列設定")

此圖示會開啟對話方塊 popover，可用來選取其中三個動作列模式：

-   **標準**&ndash;標誌或選擇性的子標題圖示和標題的文字所組成。

-   **清單**&ndash;清單瀏覽模式。 而不是靜態的標題文字，此模式會顯示在活動內的瀏覽清單功能表 （也就是它可以向使用者顯示為下拉式清單）。

-   **索引標籤** &ndash;  索引標籤瀏覽模式。 而不是靜態的標題文字，此模式會顯示一系列的活動內的瀏覽 索引標籤。

## <a name="themes"></a>主題

**佈景主題**下拉式功能表會顯示所有的專案中定義的佈景主題。 選取**更佈景主題**一份所有的佈景主題，可從已安裝的 Android SDK，會開啟一個對話方塊，如下所示：

[![更多的佈景主題清單](resource-qualifiers-images/vs/15-theme-menu-sml.png "更佈景主題清單")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

選取的佈景主題時，在設計介面會更新以顯示新的佈景主題的效果。 請注意這項變更就會變成永久才 **[確定]** ，就會引發**佈景主題**對話方塊。 一旦選取的佈景主題，它將會包含在**佈景主題**下拉式選單，如下所示：

![淺色佈景主題現已推出](resource-qualifiers-images/vs/16-light-theme.png "淺色佈景主題現已推出")

## <a name="android-version"></a>Android 版本

Android**版本**選取器設定用來呈現在設計工具中配置的 Android 版本。 選取器會顯示與專案的目標 framework 版本相容的所有版本：

![Android 版本的清單](resource-qualifiers-images/vs/17-android-version.png "清單執行的 Android 版本")

下的 [專案設定] 中，可以設定目標 framework 版本**屬性 > 應用程式 > 使用 Android 版本編譯**。 如需目標 framework 版本的詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。

小工具的工具箱中可用的設定取決於專案的目標 framework 版本。 這也適用於中可用的屬性**屬性 視窗**。 可用的小工具清單已*不*中所選取的值所決定**版本**工具列的選取器。 比方說，如果您設定專案的目標版本為 Android 4.4 時，您仍然可以選取 Android 6.0 中工具列版本選擇器，以查看在 Android 6.0 中，專案外觀，但您將無法新增小工具，專為 Android 6.0 &ndash; 此外，您仍會限制為小工具，可在 Android 4.4。

如需資源類型的詳細資訊，請參閱[Android 資源](~/android/app-fundamentals/resources-in-android/index.md)。



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="resource-qualifier-options"></a>資源限定詞選項

**資源限定詞選項**可以按一下右邊的省略符號圖示來存取**橫向**模式按鈕：

[![資源限定詞選項](resource-qualifiers-images/xs/08-resource-qual-opt-m75-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt-m75.png#lightbox)

此對話方塊會出現下拉功能表下的資源限定詞：

-   **語言**&ndash;顯示可用的語言資源，並提供選項來加入新的語言/地區資源。

-   **UI 模式**&ndash;清單顯示模式 (例如**汽車擴充座**並**桌面擴充座**) 以及版面配置方向。

每個這些下拉功能表隨即開啟新對話方塊一樣，您可以在其中選取並設定資源限定詞 （如下所述）。

### <a name="language"></a>語言

**語言**下拉功能表列出只有在已定義資源的語言 (或**所有語言**，這是預設值)。 不過，另外還有**新增語言/地區...** 選項，可讓您將新的語言新增至清單：

[![新增語言/地區](resource-qualifiers-images/xs/09-add-language-region-m75-sml.png)](resource-qualifiers-images/xs/09-add-language-region-m75.png#lightbox)

當您按一下**新增語言/地區...**，則**選取語言**對話方塊隨即開啟，顯示的可用語言和地區的下拉式清單：

[![語言的清單](resource-qualifiers-images/xs/10-languages-m75-sml.png)](resource-qualifiers-images/xs/10-languages-m75.png#lightbox)

在此範例中，我們已選擇**fr （法文）** 語言並**BE** （比利時） 為法文的地區方言。 請注意，**地區**欄位是選擇性的因為可以指定許多語言，而不必考慮特定區域。 當**語言**下拉式功能表會再次開啟，它會顯示新增語言/地區資源：

[![語言和地區的選擇](resource-qualifiers-images/xs/11-language-region-added-m75-sml.png)](resource-qualifiers-images/xs/11-language-region-added-m75.png#lightbox)

請注意，是否您新增新的語言，但您不要建立新的資源，為它加入的語言將不再顯示在下一次您開啟專案。

### <a name="ui-mode"></a>UI 模式

當您按一下  **UI 模式下**顯示的下拉式功能表，模式的清單，例如**Normal**，**汽車擴充座**，**桌面擴充座**， **電視**，**設備**，和**監看式**:

[![UI 模式 功能表](resource-qualifiers-images/xs/12-ui-mode-m75-sml.png)](resource-qualifiers-images/xs/12-ui-mode-m75.png#lightbox)

此清單下方是 夜間模式**不晚**並**晚上**，後面接著的版面配置指示**由左到右**和**由右至左**。 選項的最後一組可讓您選取其中一個**捨入螢幕**或是**矩形螢幕**（適用於 Android Wear 裝置）。

如需 Android UI 模式的詳細資訊，請參閱[UiModeManager](https://developer.xamarin.com/api/type/Android.App.UiModeManager/)。
如需**由左到右**並**由右至左**選項，請參閱[LayoutDirection](https://developer.xamarin.com/api/type/Android.Util.LayoutDirection/)。


## <a name="action-bar-settings"></a>動作列設定

**動作列設定**圖示位於左側的 （佈景主題編輯器） 的畫刷圖示：

[![動作列設定](resource-qualifiers-images/xs/13-action-bar-m75-sml.png)](resource-qualifiers-images/xs/13-action-bar-m75.png#lightbox)

此圖示會開啟對話方塊 popover，可用來選取其中三個動作列模式：

-   **標準**&ndash;可能是具有選擇性的子標題的商標或圖示和標題文字所組成。

-   **清單**&ndash;清單瀏覽模式。 而不是靜態的標題文字，此模式會顯示在活動內的瀏覽清單功能表 （也就是它可以向使用者顯示為下拉式清單）。

-   **索引標籤** &ndash;  索引標籤瀏覽模式。 而不是靜態的標題文字，此模式會顯示一系列的活動內的瀏覽 索引標籤。

## <a name="themes"></a>主題

**佈景主題**下拉式功能表會顯示所有的專案中定義的佈景主題。 選取**更佈景主題**一份所有的佈景主題，可從已安裝的 Android SDK，會開啟一個對話方塊，如下所示：

[![更多的佈景主題清單](resource-qualifiers-images/xs/14-theme-menu-m75-sml.png)](resource-qualifiers-images/xs/14-theme-menu-m75.png#lightbox)

選取的佈景主題時，在設計介面會更新以顯示新的佈景主題的效果。 請注意這項變更就會變成永久才 **[確定]** ，就會引發**佈景主題**對話方塊。 一旦選取的佈景主題，它將會包含在**佈景主題**下拉式選單，如下所示：

[![淺色佈景主題現已推出](resource-qualifiers-images/xs/15-light-theme-m75-sml.png)](resource-qualifiers-images/xs/15-light-theme-m75.png#lightbox)

## <a name="android-version"></a>Android 版本

Android**版本**選取器設定用來呈現在設計工具中配置的 Android 版本。 選取器會顯示與專案的目標 framework 版本相容的所有版本：

[![Android 版本的清單](resource-qualifiers-images/xs/16-android-version-m75-sml.png)](resource-qualifiers-images/xs/16-android-version-m75.png#lightbox)

下的 [專案設定] 中，可以設定目標 framework 版本**專案選項 > 建置 > 一般**一節。 如需目標 framework 版本的詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。

小工具的工具箱中可用的設定取決於專案的目標 framework 版本。 這也適用於中可用的屬性**屬性輸入板**。 可用的小工具清單已*不*中所選取的值所決定**版本**工具列的選取器。 比方說，如果您設定專案的目標版本為 Android 4.4 時，您仍然可以選取 Android 6.0 中工具列版本選擇器，以查看在 Android 6.0 中，專案外觀，但您將無法新增小工具，專為 Android 6.0 &ndash; 此外，您仍會限制為小工具，可在 Android 4.4。

如需資源類型的詳細資訊，請參閱[Android 資源](~/android/app-fundamentals/resources-in-android/index.md)。

-----
