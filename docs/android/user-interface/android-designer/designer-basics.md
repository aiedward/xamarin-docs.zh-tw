---
title: 設計工具的基本概念
description: 本主題導入了設計工具功能，說明如何啟動設計工具、 說明設計介面，並詳細說明如何使用 [屬性] 窗格來編輯 widget 屬性。
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: a8201301fc0437ecb79a81f40e865f14dc6af020
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798807"
---
# <a name="designer-basics"></a>設計工具的基本概念

_本主題導入了設計工具功能，說明如何啟動設計工具、 說明設計介面，並詳細說明如何使用 [屬性] 窗格來編輯 widget 屬性。_


## <a name="launching-the-designer"></a>啟動設計工具

建立版面配置時，或按兩下現有的.axml 檔案啟動時，就會自動啟動設計工具。 例如，按兩下**Main.axml**中**資源 > 配置**資料夾將會載入設計工具，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在 Visual Studio 中設計工具的螢幕](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![適用於 Mac 的 Visual Studio 中設計工具的螢幕](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

同樣地，您可以加入新的版面配置，以滑鼠右鍵按一下**配置**資料夾中的**方案總管 中**，然後選取**新增 > 新項目 > Android 配置**:

[![加入新項目 對話方塊](designer-basics-images/vs/02-add-new-layout-sml.w157.png)](designer-basics-images/vs/02-add-new-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

同樣地，您可以加入新的版面配置，以滑鼠右鍵按一下**配置**資料夾中的**方案板**，然後選取**新增 > 新的檔案 > Android > 配置**:

[![加入新檔案 對話方塊](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

-----

這會建立新.axml 檔案，並將其載入至設計介面。



## <a name="designer-features"></a>設計工具功能

設計工具包含數個區段可支援各種功能，如下列螢幕擷取畫面所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![設計工具窗格的圖形](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![設計工具窗格的圖形](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

-----

當您編輯設計工具中的配置時，您可以使用下列功能來建立和圖形設計：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

-   **設計介面**&ndash;有助於配置會如何出現在裝置上的可編輯的表示法中，提供您的使用者介面的視覺方式建構。

-   **工具列**&ndash;顯示的選取器清單：**裝置**，**版本**，**佈景主題**，版面配置設定及動作列設定。 工具列也包含用於啟動佈景主題編輯器啟用材料設計格線的圖示。

-   **工具箱**&ndash;提供 widget 和版面配置，您可以拖放到設計介面上的清單。

-   **屬性視窗**&ndash;列出所選的小工具，來檢視及編輯的屬性。

-   **文件大綱**&ndash;顯示構成配置 widget 的樹狀目錄。 您可以按一下在樹狀目錄中，讓它可在設計工具中選取的項目。 此外，按一下樹狀目錄中的項目載入的項目屬性 [屬性] 視窗。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

-   **設計介面**&ndash;有助於配置會如何出現在裝置上的可編輯的表示法中，提供您的使用者介面的視覺方式建構。

-   **工具列**&ndash;顯示的選取器清單：**裝置**，**版本**，**佈景主題**，版面配置設定及動作列設定。 工具列也包含用於啟動佈景主題編輯器啟用材料設計格線的圖示。

-   **工具箱**&ndash;提供 widget 和版面配置，您可以拖放到設計介面上的清單。

-   **屬性板**&ndash;列出所選的小工具，來檢視及編輯的屬性。

-   **文件大綱**&ndash;顯示構成配置 widget 的樹狀目錄。 您可以按一下在樹狀目錄中，讓它可在設計工具中選取的項目。 此外，按一下樹狀目錄中的項目載入項目的屬性屬性填補。

-----



## <a name="toolbar"></a>工具列

工具列 （位於設計介面上方） 顯示組態的選取器和工具功能表：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![設計工具工具列的圖表](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![設計工具工具列的圖表](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

-----


工具列提供下列功能的存取權：

-   **替代的版面配置選擇器**&ndash;可讓您從中選取不同的版面配置的版本。

-   **裝置選取器**&ndash;定義一組特定的裝置，例如螢幕大小、 解析度和鍵盤可用性相關聯的辨識符號。 您也可以加入和刪除新的裝置。

-   **Android 版本選取器**&ndash;版面配置的目標 Android 版本。 在設計工具會轉譯根據選取的 Android 版本版面配置。

-   **佈景主題選取器**&ndash;選取版面配置的 UI 佈景主題。

-   **設定選取器**&ndash;選取裝置設定，例如*縱向*或*橫向*。

-   **資源限定詞選項**&ndash;開啟對話方塊，顯示下拉式功能表選取*語言*， *UI 模式下*，*夜間模式*，和*Round 螢幕*選項。

-   **動作列設定**&ndash;設定動作列版面配置。

-   **佈景主題編輯器**&ndash;開啟*佈景主題編輯器*，這可讓您自訂的所選主題的項目。

-   **材料設計格線**&ndash;啟用或停用*材料設計格線*。 相鄰材料設計格線下拉功能表項目會開啟一個對話方塊，可讓您自訂的方格。

這些主題將詳細說明這些功能：

[資源限定詞和視覺效果選項](~/android/user-interface/android-designer/resource-qualifiers.md)相關詳細的資訊提供**裝置選取器**， **Android 版本選取器**，**佈景主題選取器**，**組態選取器**，**資源資格選項**，和**動作列設定**。

[另一種版面配置檢視](~/android/user-interface/android-designer/alternative-layout-views.md)說明如何使用**的替代方式配置選取器**。

[材料設計功能](~/android/user-interface/android-designer/material-design-features.md)提供全面的概觀**佈景主題編輯器**和**材料設計格線**。



## <a name="design-surface"></a>設計介面

在設計工具，讓您從 [工具箱] 拖曳至設計介面上拖放的 widget。 當與設計工具中的 widget 互動 （透過加入新的 widget 或重新定位現有的） 時，垂直和水平線會顯示可用的插入點的標記。 在下列範例中，新`Button`widget 被拖曳至設計介面：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![在設計介面上的範例插入行](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在設計介面上的範例插入行](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

-----

此外，可以複製 widget： 您可以使用複製和貼上複製 widget 中，或者您可以拖放時按下現有 widget <kbd>Ctrl</kbd>索引鍵。


### <a name="context-menu-commands"></a>內容功能表命令

在設計介面和文件大綱 中使用操作功能表。 這個功能表會顯示適用於所選的 widget 和其容器，讓您更輕鬆地在容器 （這不一定容易在設計介面上選取） 上執行作業的命令。 內容功能表中的範例如下：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![以滑鼠右鍵按一下設計介面時，範例操作功能表](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

在此範例中，以滑鼠右鍵按一下`TextView`開啟內容功能表提供數個選項：

-   **線性**&ndash;開啟子功能表編輯`LinearLayout`的父代`TextView`。

-   **刪除**，**複製**，和**剪下**&ndash;作業套用至右鍵`TextView`。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![以滑鼠右鍵按一下設計介面時，範例操作功能表](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

在此範例中，以滑鼠右鍵按一下`TextView`開啟內容功能表提供數個選項：

-   **RelativeLayout** &ndash;開啟子功能表編輯`RelativeLayout`的父代`TextView`。

-   **線性**&ndash;開啟子功能表編輯`LinearLayout`的父代`TextView`。

-   **刪除**，**複製**，和**剪下**&ndash;作業套用至右鍵`TextView`。

-----

在此範例中，以滑鼠右鍵按一下`TextView`開啟內容功能表提供數個選項：

-   **線性**&ndash;開啟子功能表編輯`LinearLayout`的父代`TextView`。

-   **刪除**，**複製**，和**剪下**&ndash;作業套用至右鍵`TextView`。



### <a name="zoom-controls"></a>縮放控制項

在設計介面支援縮放透過數個控制項，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![設計介面縮放控制項的圖表](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![設計介面縮放控制項的圖表](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

-----

這些控制項讓您更容易看特定區域的設計工具中的使用者介面：

-   **反白顯示容器**&ndash;會反白顯示在設計介面上的容器，以使其更容易找到時放大和縮小。

-   **標準大小**&ndash;呈現版面配置像素的-像素，如此您可以查看配置所選裝置的解析度。

-   **調整成視窗大小** &ndash; ，如此才能在設計介面上看到整個版面配置設定的縮放層級。

-   **放大**&ndash;放大以累加方式每按一下，放大版面配置。

-   **縮小**&ndash;縮小以累加方式每按一下，讓出現在設計介面上較小的配置。

請注意，所選的縮放設定不會影響在執行階段應用程式的使用者介面。


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="property-pad"></a>屬性填補

設計工具支援透過 widget 內容的編輯**屬性板**。 屬性板變更依據 widget 已選取設計工具介面中列出的屬性。 當`Button`選取前一個範例中時，該屬性`Button`widget 會顯示：

[![屬性板的螢幕擷取畫面](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

-----


# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="properties-window"></a>屬性視窗

設計工具支援透過 widget 內容的編輯**屬性 視窗**。 依據 widget 已在設計介面選取 屬性視窗變更中列出的屬性。
當`Button`選取前一個範例中時，該屬性`Button`widget 會顯示：

![[屬性] 視窗的螢幕擷取畫面](designer-basics-images/vs/08-property-pad.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="property-pad-sections"></a>內容板區段

屬性板分成數個區段類似的屬性群組在一起&ndash;這可讓您更輕鬆地找出您感興趣的屬性：

-   **Widget** &ndash;主要內容的 widget，例如`id`， `visibility`，`text`等等。管理 widget 內容的屬性通常用來放置此處。

-   **樣式**&ndash;這類變更 widget 的視覺外觀的內容`font`， `text color`，`background`等等。

-   **版面配置**&ndash;設定 widget 大小與位置的屬性。

-   **捲軸**&ndash;捲動屬性。

-   **行為**&ndash;設定小工具的運作方式的旗標。

-----



### <a name="default-values"></a>預設值

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

大部分的 widget 的內容會在空白**屬性**視窗因為它們的值是繼承自選取 Android 的佈景主題。
**屬性**視窗只會顯示所選的小工具已明確設定的值; 它不會顯示繼承自佈景主題的值。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

大部分的 widget 的內容會在空白**屬性板**因為它們的值是繼承自選取 Android 的佈景主題。 **屬性板**只會顯示所選的小工具已明確設定的值; 它不會顯示繼承自佈景主題的值。

-----


### <a name="referencing-resources"></a>參考的資源

某些屬性可以參考檔案以外的版面配置中定義的資源 **.axml**檔案。 此類型的最常見的情況下會`string`和`drawable`資源。 不過，參考可以也用於其他資源，例如`Boolean`值和維度。
當某個屬性支援資源參考，瀏覽圖示 (的省略符號， &hellip;) 屬性的文字項目旁邊會顯示。
這個按鈕會開啟資源選擇器時按下。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

例如，下列螢幕擷取畫面顯示可用的資源時按下的文字欄位右邊的省略符號時`Button`widget 中的**屬性**視窗：

[![範例資源與列出的兩個資源的螢幕擷取畫面](designer-basics-images/vs/09-resources-sml.png)](designer-basics-images/vs/09-resources.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

例如，下列螢幕擷取畫面顯示可用的資源時按下的文字欄位右邊的省略符號時`Button`widget 中的**屬性板**:

[![範例資源與列出的兩個資源的螢幕擷取畫面](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

-----

下一個範例說明資源選取器`Src`屬性`ImageView`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![ImageView 清單圖示資源的資源選擇器](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![ImageView 清單圖示資源的資源選擇器](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

-----



### <a name="boolean-property-references"></a>布林值屬性參考

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

*布林*屬性通常會選擇從下拉功能表中 [屬性] 視窗。 您可以選取`true`或`false`值，或者您可以按一下選取的屬性參考**選取資源...**.您可以也會直接輸入值，例如`true`或`false`。

![設定布林值屬性的範例](designer-basics-images/vs/11-boolean.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

*布林*屬性通常會顯示為屬性輸入板中的核取方塊。 當`Boolean`屬性支援資源參考、 小型的核取方塊出現在內容旁。 已核取核取方塊表示`true`和空白的方塊表示`false`。 您可以也會直接輸入值，例如`true`或`false`。 您的滑鼠停留輸入顯示小型文字欄位圖示。 如果您想要以手動方式輸入值，您可以按一下它。

[![設定布林值屬性的範例](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)


## <a name="grouped-properties"></a>群組的屬性

有些小工具具有群組在一起的多重值屬性 (例如`Padding`，例如)。 這些屬性值會列在**屬性板**單一的可展開的資料列中。 其中一些屬性可以直接在編輯群組的資料列，例如`Padding`屬性如下所示：

[![Padding 屬性的範例設定](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

-----


## <a name="editing-properties-inline"></a>編輯內容內嵌

Android 設計工具支援直接編輯某些屬性在設計介面上 （因此不需要搜尋屬性清單中的這些屬性）。 可以直接編輯的屬性包括文字、 邊界和大小。

### <a name="text"></a>Text

有些小工具的文字屬性 (例如`Button`和`TextView`)，可以直接在設計介面上編輯。 按兩下小工具會將它放置於編輯模式，如下所示：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Hello 字串的文字資源](designer-basics-images/vs/12-text-resource.png "文字資源")

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![Hello 字串資源](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

-----

您可以輸入新的文字值，或者您可以輸入新的資源字串。 在下列範例中，`@string/hello`資源會取代文字， `CLICK THIS BUTTON`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![Shift + Enter，以自動連結到新的資源的文字](designer-basics-images/vs/13-shift-enter-resource.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![Shift + Enter，以自動連結到新的資源的文字](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

-----

這項變更會儲存在 widget`text`屬性; 它不會修改指派給的值`@string/hello`資源。
新的文字字串中時，您可以按<kbd>Shift</kbd> +
<kbd>Enter</kbd>自動連結到新的資源的 輸入的文字。


### <a name="margin"></a>邊界

當您選取 widget 時，設計工具就會顯示可讓您以互動方式變更的大小或邊界的 widget 的控制代碼。 選取的同時，請按一下此 widget 將邊界編輯模式大小編輯模式之間切換。

當您第一次按一下小工具時，會顯示的控點。 如果您將滑鼠移至其中一個控點時，設計工具要顯示的控點會變更的屬性 (如下所示的`layout_marginLeft`屬性):

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![在設計工具中處理螢幕擷取畫面顯示邊界](designer-basics-images/vs/15-margin-handles.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![在設計工具中處理螢幕擷取畫面顯示邊界](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

-----

如果已設定邊界，虛線會顯示，表示邊界所佔的空間：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![範例中的虛線標記按鈕周圍的空間](designer-basics-images/vs/16-margins-set.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![範例中的虛線標記按鈕周圍的空間](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

-----



### <a name="size"></a>大小

如先前所述，您可以切換大小編輯模式，則已被選取時，請按一下小工具。 按一下三角形的控制代碼所指定的維度，以將大小設定`wrap_content`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![自動換行內容和調整大小控點](designer-basics-images/vs/17-wrap-content.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![自動換行內容和調整大小控點](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

-----

按一下**換行內容**控制代碼會壓縮該維度好讓它不能大於必要包裝括住的內容中的 widget。 在此範例中，則按鈕文字中下一個螢幕擷取畫面所示水平縮小。

當 [大小] 值設定為**換行內容**，設計師會顯示三重平滑的控制代碼，以相反方向變更大小，以指向`match_parent`:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![比對父控制代碼](designer-basics-images/vs/18-match-parent.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![比對父控制代碼](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

-----

按一下**相符項目父**控制代碼會還原該維度中的大小，使其與父 widget 相同。

此外，您可拖曳的循環的縮放控點 （如上述螢幕擷取畫面所示） 以調整到任意 widget`dp`值。 當您這樣做時，請同時**換行內容**和**相符項目父**控制代碼會顯示該維度的：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![循環的調整大小控點](designer-basics-images/vs/19-resize-dp.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![循環的調整大小控點](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

-----

並非所有容器都允許編輯`Size`的 widget。 例如，請注意在以下螢幕擷取畫面與`LinearLayout`選取，調整大小控點不會出現：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![沒有調整大小控點](designer-basics-images/vs/20-no-resize-handles.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![沒有調整大小控點](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

-----



## <a name="document-outline"></a>文件大綱

**文件大綱**顯示版面配置的小工具階層架構。
在下列範例中，包含`LinearLayout`選取 widget:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![文件大綱](designer-basics-images/vs/21-document-outline.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

[![文件大綱](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

-----

外框的所選的小工具 (在此情況下， `LinearLayout`) 也會反白的設計介面上。 文件大綱 中選取的 widget 會保持其設計介面上的對應項目同步的。 這是適用於選取 檢視群組，並不一定容易選取設計介面上。

文件大綱 支援複製並貼上，或您可以使用拖曳和卸除。 拖放支援文件大綱至設計介面以及從設計介面，以文件大綱。 此外，文件大綱 中的項目上按一下滑鼠右鍵顯示操作功能表項目 （相同內容功能表，當您以滑鼠右鍵按一下該相同的小工具的設計介面上會出現）。
