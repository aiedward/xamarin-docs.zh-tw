---
title: Xamarin. Android Designer 材質設計功能
description: 本主題描述的設計工具功能，可讓開發人員更輕鬆地建立與資料設計相容的版面配置。 本節介紹並說明如何使用材質方格、材質色調色板、印刷樣式尺規和主題編輯器。
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 67755baf18ad5277e7c2fe8be705522a9b668ff3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756815"
---
# <a name="xamarinandroid-designer-material-design-features"></a>Xamarin. Android Designer 材質設計功能

_本主題描述的設計工具功能，可讓開發人員更輕鬆地建立與資料設計相容的版面配置。本節介紹並說明如何使用材質方格、材質色調色板、印刷樣式尺規和主題編輯器。_

> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evolve 2016：每個人都可以使用材質設計來建立美觀的應用程式**

## <a name="overview"></a>總覽

Android Designer 包含的功能可讓您更輕鬆地建立與材質相容的版面配置。 如果您不熟悉材質設計，請參閱[材質設計簡介](https://material.io/design/introduction)。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

在本指南中，我們將探討下列設計工具功能：

- *材質方格*&ndash; Design Surface 上的覆迭顯示格線、間距和 keylines，可協助您根據材質設計方針來放置版面配置 widget。

- *主題編輯器*&ndash;一種小顏色資源編輯器，可讓您設定主題子集的色彩資訊。 例如，您可以預覽和修改材質色彩`colorPrimary`，例如、 `colorPrimaryDark`和`colorAccent`。

我們將探討每項功能，並提供如何使用它們的範例。

## <a name="material-design-grid"></a>材質設計方格

[材質設計] 方格功能表可從設計工具頂端的工具列取得：

[![材質設計方格](material-design-features-images/vs/01-material-design-grid-w158-sml.png)](material-design-features-images/vs/01-material-design-grid-w158.png#lightbox)

當您按一下 [材質設計格線線] 圖示時，設計工具會在包含下列元素的 Design Surface 上顯示重迭：

- Keylines （橙色線）

- 間距（綠色區域）

- 方格（藍線）

這些元素可以在先前的螢幕擷取畫面中看到。 這些重迭專案都可設定。 當您按一下 [材質設計格線線] 功能表旁的省略號時，就會開啟對話方塊 popover，讓您停用/啟用方格、設定 keylines 的位置，以及設定空格。 請注意，所有值都是`dp`以（與密度無關的圖元）表示：

[![方格、輔助線和間距設定](material-design-features-images/vs/03-grid-configuration-w158-sml.png)](material-design-features-images/vs/03-grid-configuration-w158.png#lightbox)

若要加入新的輔助線，請在 [**位移**] 方塊中輸入新的位移值、選取位置（**靠左**、**上**、**右**或**下**），然後按一下 + 圖示以加入新的輔助線。 同樣地，若要加入新的間距，請分別在 [**大小**] 和 [**位移**] 方塊中輸入大小和位移（以 dp 表示）。 選取位置（**靠左**、**上**、**右**或**下**），然後按一下 + 圖示以新增新的間距。

當您變更這些設定值時，它們會儲存在版面配置 XML 檔案中，並在您再次開啟版面配置時重複使用。

## <a name="theme-editor"></a>主題編輯器

[**主題編輯器**] 可讓您自訂主題屬性子集的色彩資訊。 若要開啟 [**主題編輯器**]，請按一下工具列上的畫刷圖示：

[![主題編輯器圖示](material-design-features-images/vs/04-theme-editor-icon-w158-sml.png)](material-design-features-images/vs/04-theme-editor-icon-w158.png#lightbox)

雖然 [**主題編輯器**] 可從所有目標 Android 版本和 API 層級的工具列存取，但如果目標 api 層級早于 API 21 （Android 5.0 棒），則只有下面所述的功能子集可供使用。

[**主題編輯器**] 的左側面板會顯示構成目前所選主題的色彩清單（在此範例中，我們會使用`Default Theme`）：

[![主題編輯器](material-design-features-images/vs/05-theme-editor-w158-sml.png)](material-design-features-images/vs/05-theme-editor-w158.png#lightbox)

當您選取左側的色彩時，右側面板會提供下列索引標籤，協助您編輯該色彩：

- **繼承**&ndash;顯示所選色彩的樣式繼承圖，並列出指派給該主題色彩的已解決色彩和色彩代碼。

- **色彩選擇器**&ndash;可讓您將選取的色彩變更為任意值。

- **材質調色板**&ndash;可讓您將選取的色彩變更為符合材質設計的值。

- **資源**&ndash;可讓您將選取的色彩變更為主題中其他現有色彩資源的其中一個。

讓我們詳細查看每一個索引標籤。

### <a name="inherit-tab"></a>繼承索引標籤

如下列範例所示，[**繼承**] 索引標籤會列出**預設主題**之**背景**色彩的樣式繼承：

[![繼承索引標籤](material-design-features-images/vs/06-inherit-tab-w158-sml.png)](material-design-features-images/vs/06-inherit-tab-w158.png#lightbox)

在此範例中，**預設主題**繼承自使用`@color/background_material_light`的樣式，但會將它`color/material_grey_50`覆寫為，其色彩代碼值`#fffafafa`為。
如需樣式繼承的詳細資訊，請參閱[樣式和主題](https://developer.android.com/guide/topics/ui/themes.html#Inheritance)。

### <a name="color-picker"></a>色彩選擇器

下列螢幕擷取畫面說明**色彩選擇器**：

[![色彩選擇器](material-design-features-images/vs/07-color-picker-w158-sml.png)](material-design-features-images/vs/07-color-picker-w158.png#lightbox)

在此範例中，您可以透過各種方式將**背景**色彩變更為任何值：

- 直接按一下色彩。
- 輸入色調、飽和度和亮度值。
- 以 decimal 輸入 RGB （紅色、綠色、藍色）的值。
- 設定所選色彩的 Alpha （不透明度）。
- 直接輸入十六進位色彩代碼。

您在顏色選擇器中選擇的色彩*不*受限於材質設計方針或一組可用的色彩資源。

### <a name="resources"></a>資源

[**資源**] 索引標籤提供主題中已存在的色彩資源清單：

[![人員](material-design-features-images/vs/08-resources-w158-sml.png)](material-design-features-images/vs/08-resources-w158.png#lightbox)

使用 [**資源**] 索引標籤可將您的選項限制為這份色彩清單。 請記住，如果您選擇已指派給主題另一個部分的色彩資源，則 UI 的兩個相鄰元素可能會「一起執行」（因為它們有相同的色彩），因此使用者難以區別。

### <a name="material-palette"></a>材質調色板

[**材質色板**] 索引標籤會開啟 [**材質設計色彩] 調色板**。 從這個調色板選擇色彩值會限制您的色彩選擇，讓它與材質設計方針一致：

[![材質調色板](material-design-features-images/vs/09-material-palette-w158-sml.png)](material-design-features-images/vs/09-material-palette-w158.png#lightbox)

色板的頂端會顯示主要材質設計色彩，而色板的底部則會顯示所選主要色彩的色調範圍。 例如，當您選取 [**靛藍色**] 時，會在對話方塊底部顯示一個**靛藍色**色調的集合。
當您選取色調時，屬性的色彩會變更為選取的色調。 在下列範例中，按鈕`Background Tint`的會變更為*靛藍色 500*：

![選取 [靛藍色 500]](material-design-features-images/vs/10-indigo-w158.png)

`Background Tint`會設定為*靛藍色 500* （`#ff3f51b5`）的色彩代碼，而設計工具會更新背景色彩以反映這種變更：

[![背景色調已變更](material-design-features-images/vs/11-background-tint-w158-sml.png)](material-design-features-images/vs/11-background-tint-w158.png#lightbox)

如需材質設計色調色板的詳細資訊，請參閱材質設計[色彩調色板指南](https://material.io/design/color/)。

### <a name="creating-a-new-theme"></a>建立新主題

在下列範例中，我們將使用 [材質] 元件來建立新的自訂主題。 首先，我們會將**背景**色彩變更為*藍色 900*：

![將背景變更為藍色900](material-design-features-images/vs/12-change-background-to-blue-w158.png)

當色彩資源變更時，訊息會顯示訊息，*目前的主題具有未*儲存的變更：

[![未儲存的變更警告](material-design-features-images/vs/13-unsaved-changes-w158-sml.png)](material-design-features-images/vs/13-unsaved-changes-w158.png#lightbox)

設計工具中的**背景**色彩已變更為新的色彩選擇，但這項變更尚未儲存。 此時，您可以執行下列其中一項動作：

- 按一下 [**捨棄變更**] 以捨棄新的色彩選擇（或選擇），並將主題還原為其原始狀態。

- 按<kbd>CTRL + S</kbd> ，將您的變更儲存至目前的主題。

在下列範例中，按下<kbd>CTRL + S</kbd> ，以便將變更儲存至**AppTheme**：

[![儲存至 AppTheme 的變更](material-design-features-images/vs/14-custom-theme-w158-sml.png)](material-design-features-images/vs/14-custom-theme-w158.png#lightbox)

## <a name="summary"></a>總結

本主題描述了 Xamarin. Android Designer 中可用的材質設計功能。 文中說明如何啟用和設定材質設計方格，並說明如何使用主題編輯器來建立符合材質設計方針的新自訂主題。
如需有關適用于材質設計之 Xamarin 支援的詳細資訊，請參閱[材質主題](~/android/user-interface/material-theme.md)。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

在本指南中，我們將探討下列設計工具功能：

- *材質設計方格*&ndash; Design Surface 上的覆迭顯示格線、間距和 keylines，可協助您根據材質設計方針來放置版面配置 widget。

- *材質設計色調色板*&ndash;可協助您從官方材質設計調色板選擇色彩的屬性 Pad 對話方塊。

- *印刷樣式調整*&ndash; [`textAppearance`屬性 Pad] 對話方塊，可讓您選擇文字欄位屬性的材質設計相容設定。

- *主題編輯器*&ndash;一種小顏色資源編輯器，可讓您設定主題子集的色彩資訊。 例如，您可以預覽和修改材質色彩`colorPrimary`，例如、 `colorPrimaryDark`和`colorAccent`。

我們將探討每項功能，並提供如何使用它們的範例。

## <a name="material-design-grid"></a>材質設計方格

[材質設計] 方格功能表可從設計工具頂端的工具列取得：

[![材質設計方格](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

當您按一下 [材質設計格線線] 圖示時，設計工具會在包含下列元素的 Design Surface 上顯示重迭：

- Keylines （橙色線）

- 間距（綠色區域）

- 方格（藍線）

這些元素可以在下列螢幕擷取畫面中看到：

[![輔助線、間距和方格](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

這些重迭專案都可設定。 當您按一下 [材質設計&hellip;格線] 功能表旁的省略號（）時，就會開啟對話方塊 popover，讓您停用/啟用方格、設定 keylines 的位置，以及設定空格。 請注意，所有值都是`dp`以（與密度無關的圖元）表示：

[![方格、輔助線和間距設定](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

若要加入新的輔助線，請在 [**位移**] 方塊中輸入新的位移值、選取位置（**左**、**上**、**右**或**下**），然後按一下 [+] 圖示（在輸入值時出現在右邊）以加入新的輔助線。 同樣地，若要加入新的間距，請分別在 [**大小**] 和 [**位移**] 方塊中輸入大小和位移（以 dp 表示）。 選取位置（**靠左**、**上**、**右**或**下**），然後按一下 + 圖示以新增新的間距。

當您變更這些設定值時，它們會儲存在版面配置 XML 檔案中，並在您再次開啟版面配置時重複使用。

## <a name="material-design-color-palette"></a>材質設計色調色板

每個接受色彩的屬性面板專案現在都有一個額外的調色板圖示，您可以用來開啟 [材質設計] 色彩調色板，如下列螢幕擷取畫面所示：

[![色彩圖示](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

當您按一下此圖示時，就會開啟對話方塊 popover，讓您可以從 [材質設計] 調色板設定該屬性的色彩：

[![材質設計色調色板](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

色板的頂端會顯示主要材質設計色彩，而色板的底部則會顯示所選主要色彩的色調範圍。 例如，當您選取 [**靛藍色**] 時，會在對話方塊底部顯示一個**靛藍色**色調的集合。
當您選取色調時，屬性的色彩會變更為選取的色調。 在下列範例中，按鈕`Background Tint`的會變更為*靛藍色 500*：

[![選擇靛藍色500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint`設定為*靛藍色 500* （`#ff3f51b5`）的色彩代碼，而設計工具會更新按鈕的背景色彩以反映這項變更：

[![背景淡色變更](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

如需材質設計色調色板的詳細資訊，請參閱材質設計[色彩調色板指南](https://material.io/design/color/)。

## <a name="typographic-scale"></a>印刷樣式調整

[**屬性**填補**樣式**] 索引標籤的 [**文字外觀**] 區段有一個圖示，可`TextAppearance`讓您從符合材質設計規格的樣式中進行選取：

[![樣式索引標籤](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

當您按一下此圖示時，它會開啟 [**印刷樣式調整**] 對話方塊 popover，其中會顯示預先設定的文字樣式清單，供您選擇：

[![文字樣式選擇器](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

在下列範例中，按一下 [**顯示 1** ] 會將按鈕的文字變更為**顯示 1**的較大字型：

[![顯示1樣式](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

[**排版比例**] 對話方塊中的文字樣式會遵循**主題**設定。 例如，如果在設計工具中選擇**淺色**主題，可用的文字樣式清單會反映**淺色**主題：

[![淺色主題](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

## <a name="theme-editor"></a>主題編輯器

[**主題編輯器**] 可讓您自訂主題屬性子集的色彩資訊。 若要開啟 [**主題編輯器**]，請按一下工具列上的畫刷圖示：

[![主題編輯器圖示](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

雖然 [**主題編輯器**] 可從所有目標 Android 版本和 API 層級的工具列存取，但如果目標 api 層級早于 API 21 （Android 5.0 棒），則只有下面所述的功能子集可供使用。

[**主題編輯器**] 的左側面板會顯示構成目前所選主題的色彩清單（在此範例中，我們會使用`Default Theme`）：

[![主題編輯器](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

當您選取左側的色彩時，右側面板會提供下列索引標籤，協助您編輯該色彩：

- **繼承**&ndash;顯示所選色彩的樣式繼承圖，並列出指派給該主題色彩的已解決色彩和色彩代碼。

- **色彩選擇器**&ndash;可讓您將選取的色彩變更為任意值。

- **材質調色板**&ndash;可讓您將選取的色彩變更為符合材質設計的值。

- **資源**&ndash;可讓您將選取的色彩變更為主題中其他現有色彩資源的其中一個。

讓我們詳細查看每一個索引標籤。

### <a name="inherit-tab"></a>繼承索引標籤

如下列範例所示，[**繼承**] 索引標籤會列出**預設主題**之**背景**色彩的樣式繼承：

[![繼承索引標籤](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

在此範例中，**預設主題**繼承自使用`@color/background_material_dark`的樣式，但會將它`color/material_grey_850`覆寫為，其色彩代碼值`#ff303030`為。
如需樣式繼承的詳細資訊，請參閱[樣式和主題](https://developer.android.com/guide/topics/ui/themes.html#Inheritance)。

### <a name="color-picker"></a>色彩選擇器

下列螢幕擷取畫面說明**色彩選擇器**：

[![色彩選擇器](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)

在此範例中，您可以透過各種方式將**背景**色彩變更為任何值：

- 直接按一下色彩。
- 輸入色調、飽和度和亮度值。
- 以 decimal 輸入 RGB （紅色、綠色、藍色）的值。
- 設定所選色彩的 Alpha （不透明度）。
- 直接輸入十六進位色彩代碼。

您在顏色選擇器中選擇的色彩*不*受限於材質設計方針或一組可用的色彩資源。

### <a name="resources"></a>資源

[**資源**] 索引標籤提供主題中已存在的色彩資源清單：

[![人員](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

使用 [**資源**] 索引標籤可將您的選項限制為這份色彩清單。 請記住，如果您選擇已指派給主題另一個部分的色彩資源，則 UI 的兩個相鄰元素可能會「一起執行」（因為它們有相同的色彩），因此使用者難以區別。

### <a name="material-palette"></a>材質調色板

[**材質色板**] 索引標籤會開啟稍[早](#material-design-color-palette)所述的 [**材質設計色彩] 調色板**。 從這個調色板選擇色彩值會限制您的色彩選擇，讓它與材質設計方針一致。

[![材質調色板](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

### <a name="creating-a-new-theme"></a>建立新主題

在下列範例中，我們將使用 [材質] 元件來建立新的自訂主題。 首先，我們會將**背景**色彩變更為*藍色 900*：

[![將背景變更為藍色900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

當色彩資源變更時，訊息會顯示訊息，*目前的主題具有未*儲存的變更：

[![未儲存的變更警告](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

設計工具中的色彩變更已經完成，但這項變更尚未儲存。 此時，您可以執行下列其中一項動作：

- 按一下 [**捨棄變更**] 以捨棄新的色彩選擇（或選擇），並將主題還原為其原始狀態。

- 按 **&#8984; + S** ，將您的變更儲存至名為**Custom**的新主題。

## <a name="summary"></a>總結

本主題描述了 Xamarin. Android Designer 中可用的材質設計功能。 文中說明如何啟用和設定材質設計方格、如何使用 [材質設計色彩] 調色板來編輯色彩屬性，以及如何使用 [印刷樣式調整] 選取器來設定文字屬性。 同時也示範了如何使用主題編輯器來建立新的自訂主題，以符合材質設計方針。 如需有關適用于材質設計之 Xamarin 支援的詳細資訊，請參閱[材質主題](~/android/user-interface/material-theme.md)。

-----

## <a name="related-links"></a>相關連結

- [材料佈景主題](~/android/user-interface/material-theme.md)
- [材質設計簡介](https://material.io/design/introduction)
