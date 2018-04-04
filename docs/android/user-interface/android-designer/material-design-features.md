---
title: 材料設計功能
description: 本主題描述設計工具功能，讓開發人員建立相容材料設計版面配置，更容易。 本節介紹，並說明如何使用資料方格、 材質色彩調色盤、 印刷樣式的小數位數和佈景主題編輯器。
ms.prod: xamarin
ms.assetid: AC55E1B2-C239-4019-B0C3-A16F6CF0D6E0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: a764efe7f2cadd8c777f8427c0220e45eec662c9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="material-design-features"></a>材料設計功能

_本主題描述設計工具功能，讓開發人員建立相容材料設計版面配置，更容易。本節介紹，並說明如何使用資料方格、 材質色彩調色盤、 印刷樣式的小數位數和佈景主題編輯器。_


> [!Video https://youtube.com/embed/E3_ZjIOzVzY]

**發展 2016年： 每個人都可以建立美觀的應用程式具有材料設計**

## <a name="overview"></a>總覽

Xamarin.Android 設計工具包含可讓您更輕鬆地建立材料設計的標準版面配置功能。 如果您不熟悉材料設計，請參閱[材料設計簡介](https://www.google.com/design/spec/material-design/introduction.html)。

本指南中，我們必須看看下列的設計工具功能：

-   *材料方格*&ndash;覆疊方格、 間距及 keylines 可協助您將根據材料設計指導方針來配置 widget 會顯示在設計介面上的。

-   *材料色彩調色盤*&ndash;屬性板對話方塊，可協助您選擇從官方材料設計調色盤的色彩。

-   *印刷樣式的標尺*&ndash;相容材料設計設定的選項為您提供的屬性板對話方塊`textAppearance`文字欄位的屬性。

-   *佈景主題編輯器*&ndash;小型色彩資源編輯器，可讓您設定子集的佈景主題色彩資訊。 例如，您可以預覽並修改材料色彩，例如`colorPrimary`， `colorPrimaryDark`，和`colorAccent`。

我們將看一下這些功能，並提供使用方式的範例。



## <a name="material-design-grid"></a>材料設計格線

[資料設計格線] 功能表會提供從設計工具的頂端工具列：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![材料設計格線](material-design-features-images/vs/01-material-design-grid-sml.png)](material-design-features-images/vs/01-material-design-grid.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![材料設計格線](material-design-features-images/xs/01-material-design-grid-sml.png)](material-design-features-images/xs/01-material-design-grid.png#lightbox)

-----

當您按一下 [資料設計格線] 圖示時，設計工具會包含下列項目在設計介面上顯示重疊：

-   Keylines （橙色行）

-   間距 （綠色區域）

-   方格 （藍線）

下列螢幕擷取畫面中，可以看到這些項目：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Keyline、 間距及方格](material-design-features-images/vs/02-grid-and-keylines-sml.png)](material-design-features-images/vs/02-grid-and-keylines.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![Keyline、 間距及方格](material-design-features-images/xs/02-grid-and-keylines-sml.png)](material-design-features-images/xs/02-grid-and-keylines.png#lightbox)

-----

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

每一個重疊項目是設定的。 當您按一下 [資料設計格線] 功能表旁邊的省略符號時，就會開啟對話方塊 popover，可讓您停用/啟用方格、 設定 keylines、 位置及設定的間距。 請注意，所有值均以都表示`dp`（密度無關的像素）：

![方格、 keyline 和間距組態](material-design-features-images/vs/03-grid-configuration.png)

若要加入新 keyline，輸入新的位移的值，在**位移**方塊中，選取的位置 (**左**，**頂端**，**右**，或**底部**)，按一下 [+] 圖示新增新 keyline。

同樣地，若要加入新的間距，請輸入的大小和位移 （以 dp)**大小**和**位移**方塊中分別。 選取的位置 (**左**，**頂端**，**右**，或**底部**)，按一下 [+] 圖示新增新的間距。

當您變更這些設定值時，他們儲存配置的 XML 檔案中，當您再次開啟版面配置時重複使用。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

每一個重疊項目是設定的。 當您按一下 [資料設計格線] 功能表旁邊的向下三角形時，就會開啟對話方塊 popover，可讓您停用/啟用方格、 設定 keylines、 位置及設定的間距。 請注意，所有值均以都表示`dp`（密度無關的像素）：

[![方格、 keyline 和間距組態](material-design-features-images/xs/03-grid-configuration-sml.png)](material-design-features-images/xs/03-grid-configuration.png#lightbox)

若要加入新 keyline，輸入新的位移的值，在**位移**方塊中，選取的位置 (**左**，**頂端**，**右**，或**底部**)，按一下 [+] 圖示新增新 keyline。

同樣地，若要加入新的間距，請輸入的大小和位移 （以 dp)**大小**和**位移**方塊中分別。 選取的位置 (**左**，**頂端**，**右**，或**底部**)，按一下 [+] 圖示新增新的間距。

當您變更這些設定值時，他們儲存配置的 XML 檔案中，當您再次開啟版面配置時重複使用。


## <a name="material-design-color-palette"></a>材料設計色彩調色盤

現在接受一個色彩每一個屬性面板項目具有額外圖示可讓您以開啟 資料設計色彩調色盤，如下列螢幕擷取畫面所示：

[![色彩圖示](material-design-features-images/xs/04-new-color-icon-sml.png)](material-design-features-images/xs/04-new-color-icon.png#lightbox)

當您按一下這個圖示時，就會開啟對話方塊 popover，可讓您設定該屬性從 資料設計色彩調色盤的色彩：

[![材料設計色彩調色盤](material-design-features-images/xs/05-material-palette-sml.png)](material-design-features-images/xs/05-material-palette.png#lightbox)

色彩調色盤的頂端顯示主要材料設計色彩調色盤的底部會顯示所選取的主要色彩色調的範圍。 例如，當您選取**Indigo**，集合**Indigo**色調顯示在對話方塊底部。
當您選取的色調時，會將屬性的色彩變更為選取的色調。 在下列範例中，`Background Tint`按鈕會變更為*Indigo 500*:

[![選擇 Indigo 500](material-design-features-images/xs/06-indigo-sml.png)](material-design-features-images/xs/06-indigo.png#lightbox)

`Background Tint` 設定色彩代碼，以*Indigo 500* (`#ff3f51b5`)，並在設計工具更新以反映這項變更按鈕的背景色彩：

[![背景濃淡變更](material-design-features-images/xs/07-background-tint-sml.png)](material-design-features-images/xs/07-background-tint.png#lightbox)

如需材料設計色彩調色盤的詳細資訊，請參閱材料設計[色彩調色盤指南](http://www.google.com/design/spec/style/color.html#color-color-palette)。

## <a name="typographic-scale"></a>印刷樣式的小數位數

**的文字外觀**區段**屬性**板**樣式** 索引標籤有一個圖示，可讓您選取從`TextAppearance`符合資料的設計樣式規格：

[![樣式索引標籤](material-design-features-images/xs/08-typo-scale-icon-sml.png)](material-design-features-images/xs/08-typo-scale-icon.png#lightbox)

當您按一下這個圖示時，它會開啟**印刷樣式的標尺**對話方塊 popover，列出您可以選擇從預先設定的文字樣式：

[![文字樣式選擇器](material-design-features-images/xs/09-text-appearance-sml.png)](material-design-features-images/xs/09-text-appearance.png#lightbox)

在下列範例中，按一下**顯示 1**按鈕的文字變成較大的字型**顯示 1**:

[![顯示 1 樣式](material-design-features-images/xs/10-display-1-sml.png)](material-design-features-images/xs/10-display-1.png#lightbox)

中的文字樣式**印刷樣式的標尺**對話遵照**佈景主題**設定。 例如，如果**Light**佈景主題會在設計工具，可用的文字樣式鏡像的清單中選擇**Light**佈景主題：

[![淺色佈景主題](material-design-features-images/xs/11-light-theme-sml.png)](material-design-features-images/xs/11-light-theme.png#lightbox)

-----



## <a name="theme-editor"></a>佈景主題編輯器

**佈景主題編輯器**可讓您自訂的佈景主題屬性子集的色彩資訊。 若要開啟**佈景主題編輯器**，按一下工具列上的畫刷圖示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![佈景主題編輯器圖示](material-design-features-images/vs/04-theme-editor-icon.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![佈景主題編輯器圖示](material-design-features-images/xs/12a-theme-editor-icon-sml.png)](material-design-features-images/xs/12a-theme-editor-icon.png#lightbox)

-----

雖然**佈景主題編輯器**是可從工具列存取的所有目標 Android 版本和應用程式開發介面層級，如下所述的功能子集如果只可以使用目標應用程式開發介面層級低於 API 21 (Android 5.0棒棒糖符號）。

左側面板**佈景主題編輯器**顯示構成目前選取的佈景主題色彩的清單 (在此範例中，我們會使用`Default Theme`):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![佈景主題編輯器](material-design-features-images/vs/05-theme-editor-sml.png)](material-design-features-images/vs/05-theme-editor.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![佈景主題編輯器](material-design-features-images/xs/12b-theme-editor-sml.png)](material-design-features-images/xs/12b-theme-editor.png#lightbox)

-----

當您選取的色彩在左側時，右面板提供下列索引標籤可協助您編輯該色彩：

-   **繼承**&ndash;會顯示所選取色彩的樣式繼承圖表，並列出已解決的色彩指派給該佈景主題色彩的色彩代碼。

-   **色彩選擇器**&ndash;可讓您變更所選為任意值的色彩。

-   **材料調色盤**&ndash;可讓您變更所選色彩的值，以符合資料的設計。

-   **資源**&ndash;可讓您變更所選色彩的其中一個其他現有色彩佈景主題中的資源。

讓我們看看每個詳細資料中的這些索引標籤。



### <a name="inherit-tab"></a>繼承 索引標籤

在下列範例中，所見**繼承**索引標籤會列出樣式繼承**背景**色彩**預設佈景主題**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![繼承 索引標籤](material-design-features-images/vs/06-inherit-tab-sml.png)](material-design-features-images/vs/06-inherit-tab.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![繼承 索引標籤](material-design-features-images/xs/13-inherit-sml.png)](material-design-features-images/xs/13-inherit.png#lightbox)

-----

在此範例中，**預設佈景主題**繼承自使用的樣式， `@color/background_material_dark` ，但是覆寫它與`color/material_grey_850`，其中包含色彩的程式碼值的`#ff303030`。
如需樣式繼承的詳細資訊，請參閱[樣式和主題](http://developer.android.com/guide/topics/ui/themes.html#Inheritance)。



### <a name="color-picker"></a>色彩選擇器

下列螢幕擷取畫面說明**色彩選擇器**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![色彩選擇器](material-design-features-images/vs/07-color-picker-sml.png)](material-design-features-images/vs/07-color-picker.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![色彩選擇器](material-design-features-images/xs/14-color-picker-sml.png)](material-design-features-images/xs/14-color-picker.png#lightbox)

-----

在此範例中，**背景**色彩可以變更為任何透過各種方式的值：

-   直接按一下色彩。
-   輸入色調、 飽和度和亮度的值。
-   十進位格式輸入 RGB （紅色、 綠色、 藍色） 值。
-   設定所選取色彩的 alpha （不透明）。
-   直接輸入的十六進位色彩代碼。

您在 色彩選擇器中選擇的色彩就是*不*限制材料設計指導方針或可用的色彩資源的集合。


### <a name="resources"></a>資源

**資源** 索引標籤提供的色彩資源已有佈景主題中的清單：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![資源](material-design-features-images/vs/08-resources-sml.png)](material-design-features-images/vs/08-resources.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![資源](material-design-features-images/xs/15-resources-sml.png)](material-design-features-images/xs/15-resources.png#lightbox)

-----

使用**資源** 索引標籤會限制您的選擇，此色彩的清單。 請記住，如果您選擇已指派給另一個組件的佈景主題色彩資源時，UI 的兩個相鄰的項目可能 」 一起執行 」 （因為它們有相同的色彩） 而且變得難以區別使用者。



### <a name="material-palette"></a>材料調色盤

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

**材料調色盤**索引標籤隨即開啟**材料設計色彩調色盤**。 從這個調色盤選取色彩值會限制色彩選擇，使其與材料設計指導方針一致。

[![材料調色盤](material-design-features-images/vs/09-material-palette-sml.png)](material-design-features-images/vs/09-material-palette.png#lightbox)

色彩調色盤的頂端顯示主要材料設計色彩調色盤的底部會顯示所選取的主要色彩色調的範圍。 例如，當您選取**Indigo**，集合**Indigo**色調顯示在對話方塊底部。
當您選取的色調時，會將屬性的色彩變更為選取的色調。 在下列範例中，`Background Tint`按鈕會變更為*Indigo 500*:

![選取 Indigo 500](material-design-features-images/vs/10-indigo.png)

`Background Tint` 設定色彩代碼，以*Indigo 500* (`#ff3f51b5`)，並在設計工具更新以反映這項變更的背景色彩：

[![變更背景濃淡](material-design-features-images/vs/11-background-tint-sml.png)](material-design-features-images/vs/11-background-tint.png#lightbox)

如需材料設計色彩調色盤的詳細資訊，請參閱材料設計[色彩調色盤指南](http://www.google.com/design/spec/style/color.html#color-color-palette)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

**材料調色盤**索引標籤隨即開啟**材料設計色彩調色盤**描述[早](#material_palette)。 從這個調色盤選取色彩值會限制色彩選擇，使其與材料設計指導方針一致。

[![材料調色盤](material-design-features-images/xs/16-material-palette-sml.png)](material-design-features-images/xs/16-material-palette.png#lightbox)

-----



### <a name="creating-a-new-theme"></a>建立新的佈景主題

在下列範例中，我們將建立新的自訂佈景主題使用材料調色盤。 首先，我們將會變更**背景**色彩*藍色 900*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![將背景變更為藍色 900](material-design-features-images/vs/12-change-background-to-blue.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![將背景變更為藍色 900](material-design-features-images/xs/17-change-background-to-blue-sml.png)](material-design-features-images/xs/17-change-background-to-blue.png#lightbox)

-----


變更色彩資源時，訊息會出現內含訊息*目前主題中有未儲存的變更*:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![警告未儲存的變更](material-design-features-images/vs/13-unsaved-changes-sml.png)](material-design-features-images/vs/13-unsaved-changes.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![警告未儲存的變更](material-design-features-images/xs/18-unsaved-changes-sml.png)](material-design-features-images/xs/18-unsaved-changes.png#lightbox)

-----


**背景**設計工具中的色彩已變更為新的色彩選擇，但尚未儲存這項變更。 如何處理變更提供兩個選擇：

-   **捨棄變更**&ndash;捨棄新的色彩選擇 （或選擇），並會還原為其原始狀態的佈景主題。

-   **建立新的佈景主題**&ndash;會建立新的佈景主題，從目前選取的佈景主題衍生自並採用中所做的色彩覆寫**佈景主題編輯器**。

當您按一下**建立新的佈景主題**，下列其中一種將會發生，依所選主題：

-   如果設計工具上目前選取的佈景主題不是專案佈景主題，您就會使用自訂的佈景主題 （做為自訂的佈景主題的父系使用選取的佈景主題） 建立新的資源檔選項。 設計工具會在建立後，切換到自訂的佈景主題。

-   如果目前選取的佈景主題是定義在某位置中的專案佈景主題，您會有**更新佈景主題**選項; 請按一下此選項會更新目前選取的佈景主題，而不是建立一個新。

-   如果目前選取的佈景主題定義多個位置中的專案佈景主題 (比方說，在不同的後置字元**值**資料夾，例如**值 v21**)，您會看到一個對話方塊，詢問儲存自訂的佈景主題的新位置。

繼續上述範例中，按一下**建立新的佈景主題**結果建立新的佈景主題稱為**自訂**:


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![新增自訂佈景主題](material-design-features-images/vs/14-custom-theme-sml.png)](material-design-features-images/vs/14-custom-theme.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![新增自訂佈景主題](material-design-features-images/xs/19-custom-theme-sml.png)](material-design-features-images/xs/19-custom-theme.png#lightbox)

-----


目前選取的佈景主題是專案佈景主題，因為沒有任何對話方塊更新選取的佈景主題，或指定新位置。


## <a name="summary"></a>總結

本主題所述的 Xamarin.Android 設計工具中可用的材料設計功能。 它說明如何啟用及設定材料設計格線、 如何使用資料設計色彩調色盤編輯色彩屬性，以及如何使用的印刷樣式的小數位數的選取器設定文字屬性。 它也會示範如何使用佈景主題編輯器來建立新的自訂佈景主題符合材料設計指導方針。 如需 Xamarin.Android 材料設計支援的詳細資訊，請參閱[材料佈景主題](~/android/user-interface/material-theme.md)。



## <a name="related-links"></a>相關連結

- [材料佈景主題](~/android/user-interface/material-theme.md)
- [材料設計簡介](https://www.google.com/design/spec/material-design/introduction.html)
