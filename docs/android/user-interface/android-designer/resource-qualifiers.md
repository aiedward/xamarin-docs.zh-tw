---
title: 資源限定詞和視覺效果選項
description: 本主題說明如何定義只有在符合某些限定詞值時才會使用的資源。 一個簡單的範例是一種語言限定的字串資源。 您可以將字串資源定義為預設值, 並將其他替代資源定義為用於其他語言。 所有的資源類型都可以限定, 包括版面配置本身。
ms.prod: xamarin
ms.assetid: 2111C18A-3EDA-3787-25E1-3869FF4BE441
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 750cf801d8ae9dfe63f9b2259d4a3f6a386a4404
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523231"
---
# <a name="resource-qualifiers-and-visualization-options"></a>資源限定詞和視覺效果選項

_本主題說明如何定義只有在符合某些限定詞值時才會使用的資源。一個簡單的範例是一種語言限定的字串資源。您可以將字串資源定義為預設值, 並將其他替代資源定義為用於其他語言。所有的資源類型都可以限定, 包括版面配置本身。_


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="resource-qualifier-options"></a>資源辨識符號選項

按一下 [**橫向**模式] 按鈕右邊的省略號圖示, 即可存取**資源辨識符號選項**:

[![資源辨識符號選項](resource-qualifiers-images/vs/08-resource-qual-opt-sml.png)](resource-qualifiers-images/vs/08-resource-qual-opt.png#lightbox)

此對話方塊會呈現下列資源限定詞的下拉式功能表:

- **語言**&ndash;顯示可用的語言資源, 並提供加入新語言/區域資源的選項。

- **UI 模式**列出顯示模式 (例如**汽車 dock**和**桌上 dock**) 以及版面配置方向。 &ndash;

每一個下拉式功能表都會開啟新的對話方塊, 您可以在其中選取和設定資源限定詞 (如下所述)。

### <a name="language"></a>語言

[**語言**] 下拉式功能表只會列出已定義資源的語言 (或 [**所有語言**], 這是預設值)。 不過, 也有 [**新增語言/區域 ...** ] 選項, 可讓您將新語言新增至清單:

[![新增語言/區域](resource-qualifiers-images/vs/09-add-language-region-sml.png)](resource-qualifiers-images/vs/09-add-language-region.png#lightbox)

當您按一下 [**新增語言/區域 ...** ] 時, 會開啟 [**選取語言**] 對話方塊, 以顯示可用語言和區域的下拉式清單:

![語言清單](resource-qualifiers-images/vs/10-languages.png "語言清單")

在此範例中, 我們選擇了 [ **fr (法文)** ] 語言 , 並為 [法文] 地區方言的 (比利時)。 請注意, [**區域**] 欄位是選擇性的, 因為您可以指定許多語言, 而不考慮特定區域。 再次開啟 [**語言**] 下拉式功能表時, 會顯示新加入的語言/區域資源:

![選擇的語言和區域](resource-qualifiers-images/vs/11-language-region-added.png "選擇的語言和區域")

請注意, 如果您加入新的語言, 但沒有為它建立新的資源, 則下次開啟專案時, 將不再顯示新增的語言。

### <a name="ui-mode"></a>UI 模式

當您按一下 [ **UI 模式]** 下拉式功能表時, 會顯示一份模式清單, 例如 [**一般**]、[**汽車 Dock**]、[**書桌 Dock**]、[**電視** **]、[設備] 和 [監看式]** :


[![UI 模式功能表](resource-qualifiers-images/vs/12-ui-mode-sml.png)](resource-qualifiers-images/vs/12-ui-mode.png#lightbox)

此清單下的晚上模式**不是夜間**和**夜間**, 而是由**左至右**和由右**至左**的版面配置方向 (如需**從左至右**和從**右到左**選項的詳細資訊, 請參閱[LayoutDirection](xref:Android.Util.LayoutDirection))。
[**資源辨識符號選項**] 對話方塊中的最後一個專案是**圓形畫面**(適用于 Android 磨損) 或**不是圓形畫面**。
如需迴圈和非圓角畫面的詳細資訊, 請參閱[版面](https://developer.android.com/training/wearables/ui/layouts.html)配置。
如需 Android UI 模式的詳細資訊, 請參閱[UiModeManager](xref:Android.App.UiModeManager)。

## <a name="action-bar-settings"></a>動作列設定

[**動作列設定**] 圖示可用於畫刷 (主題編輯器) 圖示的左邊:

![動作列設定](resource-qualifiers-images/vs/14-action-bar.png "動作列設定")

這個圖示會開啟一個對話方塊 popover, 可讓您從三種動作列模式之一進行選取:

- **標準**&ndash;由標誌或圖示和標題文字組成, 其中包含選擇性的副標題。

- **清單**&ndash;列出瀏覽模式。 此模式不是靜態標題文字, 而是在活動內呈現 [流覽] 的清單功能表 (也就是可以向使用者顯示為下拉式清單)。

- 索引標籤&ndash; Tab 瀏覽模式。 此模式不是靜態標題文字, 而是在活動內呈現一系列用於導覽的索引標籤。

## <a name="themes"></a>主題

[**主題**] 下拉式功能表會顯示專案中定義的所有主題。 選取 [**更多主題**] 會開啟一個對話方塊, 其中包含已安裝之 Android SDK 提供的所有主題清單, 如下所示:

[![其他主題清單](resource-qualifiers-images/vs/15-theme-menu-sml.png "其他主題清單")](resource-qualifiers-images/vs/15-theme-menu.png#lightbox)

選取主題時, Design Surface 會更新, 以顯示新主題的效果。 請注意, 只有在 [**主題**] 對話方塊中按一下 [**確定]** 按鈕時, 才會將這項變更設為永久。 選取主題後, 它就會包含在 [**主題**] 下拉式功能表中, 如下所示:

![淺色主題現已推出](resource-qualifiers-images/vs/16-light-theme.png "淺色主題現已推出")

## <a name="android-version"></a>Android 版本

Android**版本**選取器會設定用來在設計工具中呈現配置的 android 版本。 選取器會顯示與專案的目標 framework 版本相容的所有版本:

![Android 版本清單](resource-qualifiers-images/vs/17-android-version.png "Android 版本清單")

您可以在 [**屬性] > [應用程式 > 使用 Android 版本編譯**] 底下的專案設定中設定目標 framework 版本。 如需有關目標 framework 版本的詳細資訊, 請參閱[瞭解 ANDROID API 層級](~/android/app-fundamentals/android-api-levels.md)。

[工具箱] 中可用的 widget 集合是由專案的目標 framework 版本所決定。 這也適用于 [**屬性] 視窗**中提供的屬性。 可用的 widget 清單並*不*是由工具列**版本**選取器中選取的值決定。 例如, 如果您將專案的目標版本設定為 Android 4.4, 您仍然可以在工具列版本選取器中選取 [Android 6.0], 以查看該專案在 Android 6.0 中的外觀, 但是您將無法新增特定于 Android 6.0 &ndash;的 widget 您仍會受限於 Android 4.4 中提供的小工具。

如需資源類型的詳細資訊, 請參閱[Android 資源](~/android/app-fundamentals/resources-in-android/index.md)。



# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="resource-qualifier-options"></a>資源辨識符號選項

按一下 [**橫向**模式] 按鈕右邊的省略號圖示, 即可存取**資源辨識符號選項**:

[![資源辨識符號選項](resource-qualifiers-images/xs/08-resource-qual-opt-m75-sml.png)](resource-qualifiers-images/xs/08-resource-qual-opt-m75.png#lightbox)

此對話方塊會呈現下列資源限定詞的下拉式功能表:

- **語言**&ndash;顯示可用的語言資源, 並提供加入新語言/區域資源的選項。

- **UI 模式**列出顯示模式 (例如**汽車 dock**和**桌上 dock**) 以及版面配置方向。 &ndash;

每一個下拉式功能表都會開啟新的對話方塊, 您可以在其中選取和設定資源限定詞 (如下所述)。

### <a name="language"></a>語言

[**語言**] 下拉式功能表只會列出已定義資源的語言 (或 [**所有語言**], 這是預設值)。 不過, 也有 [**新增語言/區域 ...** ] 選項, 可讓您將新語言新增至清單:

[![新增語言/區域](resource-qualifiers-images/xs/09-add-language-region-m75-sml.png)](resource-qualifiers-images/xs/09-add-language-region-m75.png#lightbox)

當您按一下 [**新增語言/區域 ...** ] 時, 會開啟 [**選取語言**] 對話方塊, 以顯示可用語言和區域的下拉式清單:

[![語言清單](resource-qualifiers-images/xs/10-languages-m75-sml.png)](resource-qualifiers-images/xs/10-languages-m75.png#lightbox)

在此範例中, 我們選擇了 [ **fr (法文)** ] 語言 , 並為 [法文] 地區方言的 (比利時)。 請注意, [**區域**] 欄位是選擇性的, 因為您可以指定許多語言, 而不考慮特定區域。 再次開啟 [**語言**] 下拉式功能表時, 會顯示新加入的語言/區域資源:

[![選擇的語言和區域](resource-qualifiers-images/xs/11-language-region-added-m75-sml.png)](resource-qualifiers-images/xs/11-language-region-added-m75.png#lightbox)

請注意, 如果您加入新的語言, 但沒有為它建立新的資源, 則下次開啟專案時, 將不再顯示新增的語言。

### <a name="ui-mode"></a>UI 模式

當您按一下 [ **UI 模式]** 下拉式功能表時, 會顯示一份模式清單, 例如 [**一般**]、[**汽車 Dock**]、[**書桌 Dock**]、[**電視** **]、[設備] 和 [監看式]** :

[![UI 模式功能表](resource-qualifiers-images/xs/12-ui-mode-m75-sml.png)](resource-qualifiers-images/xs/12-ui-mode-m75.png#lightbox)

此清單下的晚上模式**不是夜間**和**夜間**, 而是由**左至右**和由**右至左**的版面配置方向。 最後一組選項可讓您選取**圓形畫面**或**矩形畫面**(適用于 Android 磨損裝置)。

如需 Android UI 模式的詳細資訊, 請參閱[UiModeManager](xref:Android.App.UiModeManager)。
如需**從左至右**和由**右至左**選項的詳細資訊, 請參閱[LayoutDirection](xref:Android.Util.LayoutDirection)。


## <a name="action-bar-settings"></a>動作列設定

[**動作列設定**] 圖示可用於畫刷 (主題編輯器) 圖示的左邊:

[![動作列設定](resource-qualifiers-images/xs/13-action-bar-m75-sml.png)](resource-qualifiers-images/xs/13-action-bar-m75.png#lightbox)

這個圖示會開啟一個對話方塊 popover, 可讓您從三種動作列模式之一進行選取:

- **標準**&ndash;由標誌或圖示和標題文字組成, 其中包含選擇性的副標題。

- **清單**&ndash;列出瀏覽模式。 此模式不是靜態標題文字, 而是在活動內呈現 [流覽] 的清單功能表 (也就是可以向使用者顯示為下拉式清單)。

- 索引標籤&ndash; Tab 瀏覽模式。 此模式不是靜態標題文字, 而是在活動內呈現一系列用於導覽的索引標籤。

## <a name="themes"></a>主題

[**主題**] 下拉式功能表會顯示專案中定義的所有主題。 選取 [**更多主題**] 會開啟一個對話方塊, 其中包含已安裝之 Android SDK 提供的所有主題清單, 如下所示:

[![其他主題清單](resource-qualifiers-images/xs/14-theme-menu-m75-sml.png)](resource-qualifiers-images/xs/14-theme-menu-m75.png#lightbox)

選取主題時, Design Surface 會更新, 以顯示新主題的效果。 請注意, 只有在 [**主題**] 對話方塊中按一下 [**確定]** 按鈕時, 才會將這項變更設為永久。 選取主題後, 它就會包含在 [**主題**] 下拉式功能表中, 如下所示:

[![淺色主題現已推出](resource-qualifiers-images/xs/15-light-theme-m75-sml.png)](resource-qualifiers-images/xs/15-light-theme-m75.png#lightbox)

## <a name="android-version"></a>Android 版本

Android**版本**選取器會設定用來在設計工具中呈現配置的 android 版本。 選取器會顯示與專案的目標 framework 版本相容的所有版本:

[![Android 版本清單](resource-qualifiers-images/xs/16-android-version-m75-sml.png)](resource-qualifiers-images/xs/16-android-version-m75.png#lightbox)

您可以在專案的 設定 底下的 專案選項 底下設定目標 framework 版本, **> 組建 > 一般** 區段。 如需有關目標 framework 版本的詳細資訊, 請參閱[瞭解 ANDROID API 層級](~/android/app-fundamentals/android-api-levels.md)。

[工具箱] 中可用的 widget 集合是由專案的目標 framework 版本所決定。 這也適用于**屬性 Pad**中提供的屬性。 可用的 widget 清單並*不*是由工具列**版本**選取器中選取的值決定。 例如, 如果您將專案的目標版本設定為 Android 4.4, 您仍然可以在工具列版本選取器中選取 [Android 6.0], 以查看該專案在 Android 6.0 中的外觀, 但是您將無法新增特定于 Android 6.0 &ndash;的 widget 您仍會受限於 Android 4.4 中提供的小工具。

如需資源類型的詳細資訊, 請參閱[Android 資源](~/android/app-fundamentals/resources-in-android/index.md)。

-----
