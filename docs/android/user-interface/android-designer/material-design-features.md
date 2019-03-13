---
title: Xamarin.Android 設計師材料設計功能
description: 本主題會描述設計工具功能，方便開發人員建立材料設計符合規範的版面配置。 本節介紹，並說明如何使用資料方格、 材質調色盤、 印刷樣式的小數位數和佈景主題編輯器。
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 5e6d7b4bdfdf7ea48d26537cb41c763656b050e0
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57669645"
---
# <a name="xamarinandroid-designer-material-design-features"></a>Xamarin.Android 設計工具的材料設計功能

_本主題會描述設計工具功能，方便開發人員建立材料設計符合規範的版面配置。本節介紹，並說明如何使用資料方格、 材質調色盤、 印刷樣式的小數位數和佈景主題編輯器。_

> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**Evolve 2016：每個人都可以使用材質設計來建立美觀的應用程式**

## <a name="overview"></a>總覽

Xamarin.Android 設計工具包含功能，可讓您更輕鬆地建立材料設計為符合規範的版面配置。 如果您不熟悉 Material Design，請參閱[Material Design 簡介](https://material.io/design/introduction)。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

本指南中，我們將看看下列的設計工具功能：

-   *材質的方格*&ndash;覆疊格線、 間距及 keylines 可協助您將根據材料設計指導方針的版面配置 widget 會顯示在設計介面上。

-   *佈景主題編輯器*&ndash;小型色彩資源編輯器，可讓您設定的一小部分的佈景主題色彩資訊。 例如，您可以在 預覽，並修改材質的色彩，例如`colorPrimary`， `colorPrimaryDark`，和`colorAccent`。

我們將看一下每一項功能，並提供範例，示範如何使用它們。

## <a name="material-design-grid"></a>材質設計格線

材質設計格線功能表可從頂端的設計工具工具列：

[![材質設計格線](material-design-features-images/vs/01-material-design-grid-w158-sml.png)](material-design-features-images/vs/01-material-design-grid-w158.png#lightbox)

當您按一下材質設計格線圖示時，設計工具會顯示包含下列元素的設計介面上的覆疊：

-   Keylines （橘色線）

-   間距 （綠色的區域）

-   方格 （藍線）

先前的螢幕擷取畫面中，就可以看到這些項目。 每一個重疊項目是可設定。 當您按一下 [材質設計格線] 功能表旁邊的省略符號時，則會開啟對話方塊 popover，可讓您停用/啟用方格中，設定位置的 keylines，以及設定空格。 請注意，所有的值會以`dp`（密度獨立像素為單位）：

[![方格、 keyline 和間距的組態](material-design-features-images/vs/03-grid-configuration-w158-sml.png)](material-design-features-images/vs/03-grid-configuration-w158.png#lightbox)

若要加入新的 keyline，輸入新的位移的值，在**位移**方塊中，選取的位置 (**左**，**頂端**，**右**，或**底部**)，按一下 + 加入新的 keyline 的圖示。 同樣地，若要新增新的間距，請輸入的大小和位移 （以 dp)**大小**並**位移**方塊中分別。 選取的位置 (**左**，**頂端**，**右**，或**下方**)，按一下 + 圖示以新增新的間距。

當您變更這些設定值時，應用在配置的 XML 檔案中儲存和重複使用時再次開啟版面配置。


## <a name="theme-editor"></a>佈景主題編輯器

**佈景主題編輯器**可讓您自訂的佈景主題屬性子集的色彩資訊。 若要開啟 **佈景主題編輯器**，按一下工具列上的畫刷圖示：

[![佈景主題編輯器圖示](material-design-features-images/vs/04-theme-editor-icon-w158-sml.png)](material-design-features-images/vs/04-theme-editor-icon-w158.png#lightbox)

雖然**佈景主題編輯器**僅可從工具列存取所有目標 Android 版本和 API 層級，如下所述的功能子集有可用的目標 API 層級是否早於 API 21 (Android 5.0棒棒糖符號）。

左側面板**佈景主題編輯器**顯示組成目前選取的佈景主題色彩的清單 (在此範例中，我們會使用`Default Theme`):

[![佈景主題編輯器](material-design-features-images/vs/05-theme-editor-w158-sml.png)](material-design-features-images/vs/05-theme-editor-w158.png#lightbox)

當您選取的色彩在左側時，右側面板中會提供下列索引標籤，以協助您編輯該色彩：

-   **繼承**&ndash;顯示所選取之色彩的樣式繼承圖表，並列出已解決的色彩和指派給該佈景主題色彩的色彩代碼。

-   **色彩選擇器**&ndash;可讓您將選取的色彩變更為任意值。

-   **材質調色盤**&ndash;可讓您變更選取的色彩符合 Material Design 的值。

-   **資源**&ndash;可讓您將選取的色彩變更為其他現有色彩資源佈景主題中的其中一個。

讓我們看看每個詳細資料中的索引標籤。

### <a name="inherit-tab"></a>繼承 索引標籤

下列範例所示**繼承**索引標籤會列出的樣式繼承**背景**色彩**預設佈景主題**:

[![繼承 索引標籤](material-design-features-images/vs/06-inherit-tab-w158-sml.png)](material-design-features-images/vs/06-inherit-tab-w158.png#lightbox)

在此範例中，**預設佈景主題**使用的樣式繼承`@color/background_material_light`但會使用覆寫`color/material_grey_50`，其中包含色彩的程式碼值`#fffafafa`。
如需有關樣式繼承的詳細資訊，請參閱[樣式和佈景主題](https://developer.android.com/guide/topics/ui/themes.html#Inheritance)。

### <a name="color-picker"></a>色彩選擇器

下列螢幕擷取畫面說明**色彩選擇器**:

[![色彩選擇器](material-design-features-images/vs/07-color-picker-w158-sml.png)](material-design-features-images/vs/07-color-picker-w158.png#lightbox)

在此範例中，**背景**色彩變更為任何值，透過各種方式：

-   直接按一下色彩。
-   輸入 色調、 飽和度和亮度值。
-   輸入十進位格式的 RGB （紅色、 綠色、 藍色） 值。
-   設定所選取色彩的 alpha (opacity)。
-   直接輸入十六進位色彩代碼。

您在 色彩選擇器中選擇的色彩就是*不*限制材料設計指導方針，或一組可用的色彩資源。

### <a name="resources"></a>資源

**資源**索引標籤會提供一份已存在於佈景主題色彩資源：

[![資源](material-design-features-images/vs/08-resources-w158-sml.png)](material-design-features-images/vs/08-resources-w158.png#lightbox)

使用**資源** 索引標籤會限制您的選擇，此色彩的清單。 請記住，如果您選擇 [已指派給另一個組件之佈景主題色彩資源] 時，UI 的兩個相鄰的項目可能會 「 執行一起 」 （因為它們有相同的色彩） 和變得很困難，讓使用者可用來區別。

### <a name="material-palette"></a>材質調色盤

**材質調色盤**索引標籤隨即開啟**材料設計調色盤**。 從這個調色盤選擇色彩值會限制您的色彩選擇，使其與材料設計指導方針一致：

[![材質調色盤](material-design-features-images/vs/09-material-palette-w158-sml.png)](material-design-features-images/vs/09-material-palette-w158.png#lightbox)

色彩調色盤頂端會顯示主要的 Material Design 色彩調色盤底部會顯示所選取的主要色彩色調的範圍。 例如，當您選取**Indigo**，一堆**Indigo**色調會顯示在對話方塊底部。
當您選取的色調時，則會將屬性的色彩變更為所選的色調。 在下列範例中， `Background Tint`  按鈕會變成*Indigo 500*:

![選取 Indigo 500](material-design-features-images/vs/10-indigo-w158.png)

`Background Tint` 設定色彩代碼，以*Indigo 500* (`#ff3f51b5`)，並在設計工具更新以反映這項變更的背景色彩：

[![變更背景濃淡](material-design-features-images/vs/11-background-tint-w158-sml.png)](material-design-features-images/vs/11-background-tint-w158.png#lightbox)

如需詳細的 Material Design 色彩調色盤的詳細資訊，請參閱 Material Design[色彩調色盤指南](https://material.io/design/color/)。

### <a name="creating-a-new-theme"></a>建立新的佈景主題

在下列範例中，我們將使用材質調色盤，以建立新的自訂佈景主題。 首先，我們將會變更**背景**色彩*藍色 900*:

![將背景變更為藍色 900](material-design-features-images/vs/12-change-background-to-blue-w158.png)

變更色彩資源時，訊息會出現訊息，*目前的佈景主題中有未儲存的變更*:

[![警告未儲存的變更](material-design-features-images/vs/13-unsaved-changes-w158-sml.png)](material-design-features-images/vs/13-unsaved-changes-w158.png#lightbox)

**背景**設計工具中的色彩已變更為新的色彩選取範圍，但這項變更尚未儲存。 此時，您可以執行下列其中一項：

-   按一下 **捨棄變更**捨棄新的色彩選擇 （或選擇），並還原為其原始狀態的佈景主題。 

-   按下<kbd>CTRL + S</kbd>您將變更儲存到目前的佈景主題。 

在下列範例中， <kbd>CTRL + S</kbd>使所做的變更已儲存至按下**AppTheme**:

[![AppTheme 儲存的變更](material-design-features-images/vs/14-custom-theme-w158-sml.png)](material-design-features-images/vs/14-custom-theme-w158.png#lightbox)

## <a name="summary"></a>總結

本主題所述的 Xamarin.Android 設計工具中可用的材料設計功能。 它說明如何啟用及設定材質設計格線，以及該文說明如何使用佈景主題編輯器來建立新的自訂佈景主題符合材料設計指導方針。
多個 Material Design 的 Xamarin.Android 支援的詳細資訊，請參閱[材料佈景主題](~/android/user-interface/material-theme.md)。




# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

本指南中，我們將看看下列的設計工具功能：

-   *材質設計格線*&ndash;覆疊格線、 間距及 keylines 可協助您將根據材料設計指導方針的版面配置 widget 會顯示在設計介面上。

-   *材料設計調色盤*&ndash;屬性板對話方塊，可協助您從官方的 Material Design 調色盤選擇色彩。

-   *印刷樣式的縮放比例*&ndash;屬性輸入板 對話方塊，可讓您選擇的材料設計符合規範設定`textAppearance`文字欄位的屬性。

-   *佈景主題編輯器*&ndash;小型色彩資源編輯器，可讓您設定的一小部分的佈景主題色彩資訊。 例如，您可以在 預覽，並修改材質的色彩，例如`colorPrimary`， `colorPrimaryDark`，和`colorAccent`。

我們將看一下每一項功能，並提供範例，示範如何使用它們。

## <a name="material-design-grid"></a>材質設計格線

材質設計格線功能表可從頂端的設計工具工具列：

[![材質設計格線](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

當您按一下材質設計格線圖示時，設計工具會顯示包含下列元素的設計介面上的覆疊：

-   Keylines （橘色線）

-   間距 （綠色的區域）

-   方格 （藍線）

下列螢幕擷取畫面中，就可以看到這些項目：

[![Keyline、 間距及方格](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

每一個重疊項目是可設定。 當您按一下省略符號 (&hellip;) 旁邊材質設計格線 功能表中，對話方塊 popover 隨即開啟，可讓您停用/啟用方格、 設定 keylines，位置和設定的空格。 請注意，所有的值會以`dp`（密度獨立像素為單位）：

[![方格、 keyline 和間距的組態](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

若要加入新的 keyline，輸入新的位移的值，在**位移**方塊中，選取的位置 (**左**，**頂端**，**右**，或**底部**)，按一下 [+] 圖示 （這會顯示為右輸入的值） 以新增新的 keyline。 同樣地，若要新增新的間距，請輸入的大小和位移 （以 dp)**大小**並**位移**方塊中分別。 選取的位置 (**左**，**頂端**，**右**，或**下方**)，按一下 + 圖示以新增新的間距。

當您變更這些設定值時，應用在配置的 XML 檔案中儲存和重複使用時再次開啟版面配置。

## <a name="material-design-color-palette"></a>材料設計色彩調色盤

現在接受一種色彩每個屬性面板項目具有其他調色盤圖示可供您若要開啟 資料設計色彩調色盤，如以下螢幕擷取畫面所示：

[![色彩圖示](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

當您按一下此圖示時，便會開啟對話方塊 popover，可讓您設定該屬性從 Material Design 色彩調色盤的色彩：

[![材料設計色彩調色盤](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

色彩調色盤頂端會顯示主要的 Material Design 色彩調色盤底部會顯示所選取的主要色彩色調的範圍。 例如，當您選取**Indigo**，一堆**Indigo**色調會顯示在對話方塊底部。
當您選取的色調時，則會將屬性的色彩變更為所選的色調。 在下列範例中， `Background Tint`  按鈕會變成*Indigo 500*:

[![選擇 Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` 設定色彩代碼，以*Indigo 500* (`#ff3f51b5`)，並在設計工具更新以反映這項變更按鈕的背景色彩：

[![背景濃淡變更](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

如需詳細的 Material Design 色彩調色盤的詳細資訊，請參閱 Material Design[色彩調色盤指南](https://material.io/design/color/)。

## <a name="typographic-scale"></a>印刷樣式的小數位數

**文字的外觀**一節**屬性**板**樣式**索引標籤有一個圖示，可讓您選取從`TextAppearance`符合材料設計的樣式規格：

[![樣式索引標籤](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

當您按一下此圖示時，它會開啟**印刷樣式的小數位數**對話方塊 popover，顯示您可以選擇從預先設定的文字樣式的清單：

[![文字樣式選擇器](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

在下列範例中，按一下**顯示 1**  按鈕的文字變更的較大的字型**顯示 1**:

[![顯示 1 樣式](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

中的文字樣式**印刷樣式的縮放比例**對話遵照**佈景主題**設定。 比方說，如果**Light**佈景主題會選擇在設計師中，清單中的可用的文字樣式鏡像**Light**佈景主題：

[![淺色佈景主題](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

## <a name="theme-editor"></a>佈景主題編輯器

**佈景主題編輯器**可讓您自訂的佈景主題屬性子集的色彩資訊。 若要開啟 **佈景主題編輯器**，按一下工具列上的畫刷圖示：

[![佈景主題編輯器圖示](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

雖然**佈景主題編輯器**僅可從工具列存取所有目標 Android 版本和 API 層級，如下所述的功能子集有可用的目標 API 層級是否早於 API 21 (Android 5.0棒棒糖符號）。

左側面板**佈景主題編輯器**顯示組成目前選取的佈景主題色彩的清單 (在此範例中，我們會使用`Default Theme`):

[![佈景主題編輯器](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

當您選取的色彩在左側時，右側面板中會提供下列索引標籤，以協助您編輯該色彩：

-   **繼承**&ndash;顯示所選取之色彩的樣式繼承圖表，並列出已解決的色彩和指派給該佈景主題色彩的色彩代碼。

-   **色彩選擇器**&ndash;可讓您將選取的色彩變更為任意值。

-   **材質調色盤**&ndash;可讓您變更選取的色彩符合 Material Design 的值。

-   **資源**&ndash;可讓您將選取的色彩變更為其他現有色彩資源佈景主題中的其中一個。

讓我們看看每個詳細資料中的索引標籤。

### <a name="inherit-tab"></a>繼承 索引標籤

下列範例所示**繼承**索引標籤會列出的樣式繼承**背景**色彩**預設佈景主題**:

[![繼承 索引標籤](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

在此範例中，**預設佈景主題**使用的樣式繼承`@color/background_material_dark`但會使用覆寫`color/material_grey_850`，其中包含色彩的程式碼值`#ff303030`。
如需有關樣式繼承的詳細資訊，請參閱[樣式和佈景主題](https://developer.android.com/guide/topics/ui/themes.html#Inheritance)。

### <a name="color-picker"></a>色彩選擇器

下列螢幕擷取畫面說明**色彩選擇器**:

[![色彩選擇器](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)


在此範例中，**背景**色彩變更為任何值，透過各種方式：

-   直接按一下色彩。
-   輸入 色調、 飽和度和亮度值。
-   輸入十進位格式的 RGB （紅色、 綠色、 藍色） 值。
-   設定所選取色彩的 alpha (opacity)。
-   直接輸入十六進位色彩代碼。

您在 色彩選擇器中選擇的色彩就是*不*限制材料設計指導方針，或一組可用的色彩資源。

### <a name="resources"></a>資源

**資源**索引標籤會提供一份已存在於佈景主題色彩資源：

[![資源](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

使用**資源** 索引標籤會限制您的選擇，此色彩的清單。 請記住，如果您選擇 [已指派給另一個組件之佈景主題色彩資源] 時，UI 的兩個相鄰的項目可能會 「 執行一起 」 （因為它們有相同的色彩） 和變得很困難，讓使用者可用來區別。

### <a name="material-palette"></a>材質調色盤

**材質調色盤**索引標籤隨即開啟**材料設計調色盤**所述[舊版](#material_palette)。 選擇從這個調色盤的色彩值會限制您的色彩選擇，使其與材料設計指導方針一致。

[![材質調色盤](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

### <a name="creating-a-new-theme"></a>建立新的佈景主題

在下列範例中，我們將使用材質調色盤，以建立新的自訂佈景主題。 首先，我們將會變更**背景**色彩*藍色 900*:

[![將背景變更為藍色 900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

變更色彩資源時，訊息會出現訊息，*目前的佈景主題中有未儲存的變更*:

[![警告未儲存的變更](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

已在設計工具中的色彩變化，但這項變更尚未儲存。 此時，您可以執行下列其中一項：

-   按一下 **捨棄變更**捨棄新的色彩選擇 （或選擇），並還原為其原始狀態的佈景主題。 

-   按下 **&#8984; + S**若要將變更儲存至新的佈景主題稱為**自訂**。 


## <a name="summary"></a>總結

本主題所述的 Xamarin.Android 設計工具中可用的材料設計功能。 它說明如何啟用及設定材質設計格線、 如何使用資料設計調色盤編輯色彩屬性，以及如何設定文字屬性使用的印刷樣式的小數位數的選取器。 它也會示範如何使用佈景主題編輯器來建立新的自訂佈景主題符合材料設計指導方針。 多個 Material Design 的 Xamarin.Android 支援的詳細資訊，請參閱[材料佈景主題](~/android/user-interface/material-theme.md)。

-----


## <a name="related-links"></a>相關連結

- [材料佈景主題](~/android/user-interface/material-theme.md)
- [材料設計簡介](https://material.io/design/introduction)
