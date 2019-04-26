---
title: Xamarin.Android 設計工具基本概念
description: 本主題會介紹 Xamarin.Android 設計工具功能，說明如何啟動設計工具、 告訴您，設計介面，並詳細說明如何使用 [屬性] 窗格來編輯小工具屬性的資料。
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/05/2018
ms.openlocfilehash: fe909d72f3c6d6733318b5dcbd1858a1a9e28b37
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61165057"
---
# <a name="xamarinandroid-designer-basics"></a>Xamarin.Android 設計工具基本概念

_本主題會介紹 Xamarin.Android 設計工具功能，說明如何啟動設計工具、 告訴您，設計介面，並詳細說明如何使用 [屬性] 窗格來編輯小工具屬性的資料。_


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>啟動設計工具

建立版面配置，或按兩下現有的版面配置檔案就可以啟動時，會自動啟動設計工具。 例如，按兩下**activity_main.axml**中**資源 > 配置**資料夾將會載入設計工具，如以下螢幕擷取畫面所示：

[![在 Visual Studio 中設計工具的螢幕](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

同樣地，您可以加入新的版面配置，以滑鼠右鍵按一下**版面配置**資料夾中的**方案總管**，然後選取**新增 > 新增項目 > Android 配置**:

[![加入新項目 對話方塊](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

這會建立新 **.axml**版面配置檔，並載入設計工具。

## <a name="designer-features"></a>設計工具功能

設計工具包含數個區段支援及其各種功能，如下列螢幕擷取畫面所示：

[![設計工具窗格的圖表](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

當您編輯設計工具中的版面配置時，您可以使用下列功能來建立及設計您的設計：

-   **設計介面**&ndash;執行視覺方式建構使用者介面，方法是讓您的版面配置會如何出現在裝置上的可編輯的表示法。 **設計介面**內的顯示**設計窗格**(具有**設計工具工具列**位於其上方)。

-   **來源窗格**&ndash;提供的基礎 XML 來源對應上顯示的設計檢視**設計介面**。

-   **設計工具工具列**&ndash;顯示的選取器清單：**裝置**，**版本**，**佈景主題**，版面配置設定及動作列設定。 **設計工具工具列**也包含圖示以啟動 佈景主題編輯器及啟用材質設計格線。

-   **工具箱**&ndash;提供一份 widget 和版面配置，您可以拖放到**設計介面**。

-   **屬性視窗**&ndash;列出所選的小工具，來檢視及編輯的屬性。

-   **文件大綱**&ndash;顯示構成配置 widget 的樹狀目錄。 您可以按一下在樹狀目錄中，讓它在選取的項目**設計介面**。 此外，按一下樹狀目錄中的項目載入的項目屬性**屬性**視窗。

## <a name="design-surface"></a>設計介面

設計工具可讓您將從拖曳至 [工具箱] 拖放 widget**設計介面**。 當與設計工具中的小工具互動 （透過加入新的 widget，或重新定位現有的） 時，垂直和水平列會顯示將可用的插入點。 在下列範例中，新`Button`小工具拖曳至**設計介面**:

[![在設計介面上的範例插入行](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

此外，可複製小工具： 您可以使用複製和貼上複製的 widget 中，或者您可以拖曳和卸除現有的小工具時按下<kbd>CTRL</kbd>索引鍵。

### <a name="designer-toolbar"></a>設計工具工具列

**設計工具 工具列**(位於上方**設計介面**) 提供組態選取器和工具功能表：

[![設計工具工具列的圖表](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

**設計工具工具列**可存取下列功能：

-   **替代的版面配置選取器**&ndash;可讓您選取從不同的版面配置版本。

-   **裝置選取器**&ndash;定義一組特定的裝置相關聯的限定詞 （例如螢幕大小、 解析度和鍵盤可用性）。 您也可以新增和刪除新的裝置。

-   **Android 版本選取器**&ndash;版面配置的目標 Android 版本。 設計工具將呈現的版面配置，根據所選的 Android 版本而定。

-   **佈景主題選取器**&ndash;選取 UI 佈景主題，版面配置。

-   **組態選取器**&ndash;選取裝置設定，例如*直向*或是*橫向*。

-   **資源限定詞選項**&ndash;隨即開啟一個對話方塊，顯示下拉式功能表來選取*語言*， *UI 模式下*，*夜間模式*，及*圓螢幕*選項。

-   **動作列設定**&ndash;設定版面配置的動作列設定。

-   **佈景主題編輯器**&ndash;便會開啟*佈景主題編輯器*，這可讓您自訂的 選取的佈景主題項目。

-   **材質設計格線**&ndash;啟用或停用*材質設計格線*。 相鄰材質設計格線下拉式選單中的項目會開啟一個對話方塊，可讓您自訂的方格。

這些主題中的更詳細說明每一項功能：

-   [資源限定詞和視覺效果選項](~/android/user-interface/android-designer/resource-qualifiers.md)提供有關的詳細的資訊**裝置選取器**， **Android 版本選取器**，**佈景主題選取器**，**組態選取器**，**資源資格選項**，和**動作列設定**。

-   [替代的版面配置檢視](~/android/user-interface/android-designer/alternative-layout-views.md)說明如何使用**替代版面配置選取器**。

-   [Xamarin.Android 設計師材料設計功能](~/android/user-interface/android-designer/material-design-features.md)提供完整的概觀**佈景主題編輯器**並**材質設計格線**。

### <a name="context-menu-commands"></a>內容功能表命令

內容功能表位於集中**設計介面**然後在**文件大綱**。 此功能表會顯示可供所選小工具和其容器，讓您更輕鬆地在容器上執行作業的命令 (這不一定能夠輕鬆選上**設計介面**)。 內容功能表中的範例如下：

[![以滑鼠右鍵按一下設計介面時，範例操作功能表](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

在此範例中，以滑鼠右鍵按一下`TextView`開啟內容功能表，提供數個選項：

-   **LinearLayout** &ndash;開啟進行編輯的子功能表`LinearLayout`的父代`TextView`。

-   **刪除**，**複製**，以及**剪下**&ndash;套用至以滑鼠右鍵按一下作業`TextView`。


### <a name="zoom-controls"></a>縮放控制項

**設計介面**支援縮放透過數個控制項，如所示：

[![設計介面的縮放控制項的圖表](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

這些控制項讓您更容易查看特定區域的設計工具中的使用者介面：

-   **反白顯示容器**&ndash;會在反白顯示容器**設計介面**，使其更輕鬆地找出時放大和縮小。

-   **標準大小**&ndash;呈現版面配置像素的像素的以便您可以看到配置所選裝置的解析度所呈現的樣子。

-   **調整成視窗大小**&ndash;使整個版面配置會顯示在設計介面上設定的縮放層級。

-   **拉近**&ndash;放大以累加方式每按一下，放大版面配置。

-   **縮小**&ndash;縮小以累加方式每按一下，讓版面配置看起來比較小，在設計介面上。

請注意所選的縮放設定不會影響應用程式在執行階段的使用者介面。

## <a name="switching-between-design-and-source-panes"></a>切換 設計 與 來源窗格

中心帶之間**設計**並**來源**窗格，有數個按鈕，用來修改如何**設計**和**來源**窗格會顯示：

[![窗格中顯示按鈕位置](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

這些按鈕執行下列作業：

-   **設計**&ndash;這個最上層的按鈕**設計**，選取**設計**窗格。 按一下此按鈕時，**工具箱**並**屬性**窗格已啟用，**文字編輯器 工具列**則不會顯示。 當**摺疊**（參見下方），按下按鈕**設計**而不需要單獨顯示窗格**來源**窗格。

-   **交換窗格**&ndash;這個按鈕 （類似於兩個相對的箭號） 交換**設計**並**來源**窗格，讓**來源**窗格位於左邊和**設計**位於右邊的窗格。 這些窗格，再按一次交換回到其原始位置。

-   **來源**&ndash;這個按鈕 （類似於兩個相對的角括弧） 選取**來源**窗格。 按一下此按鈕時，**工具箱**並**屬性**窗格會停用和**文字編輯器 工具列**就會顯示在 Visual Studio 的頂端。 當**摺疊**按一下按鈕時 （如下所示），按一下**來源**按鈕會顯示**來源**窗格，而不是**設計**窗格。

-   **垂直分割**&ndash;這個按鈕 （類似於直條），會顯示**設計**並**來源**窗格-並存。 這是預設排列方式。

-   **水平分割**&ndash;這個按鈕 （類似於水平列），會顯示**設計**上面的窗格**來源**窗格。 **交換窗格**可以按一下要放置**來源**上面的窗格**設計**窗格。

-   **摺疊窗格**&ndash;這個按鈕 （類似於兩個右角括弧） 「 摺疊 」 的雙窗格顯示**設計**並**來源**到其中的單一檢視這些窗格中。
    這個按鈕會變成**展開窗格**（類似於兩個向左角括弧），可以按一下傳回至雙窗格的檢視 按鈕 (**設計**和**來源**) 顯示模式。

當**摺疊窗格**按一下時，只有**設計** 窗格隨即顯示。 不過，您可以按一下**來源** 按鈕，而是僅限檢視**來源**窗格。 按一下 **設計**按鈕，以返回**設計**窗格。

## <a name="source-pane"></a>來源窗格

**來源** 窗格會顯示基礎設計上顯示的 XML 來源**設計介面**。 因為在此同時，這兩個檢視可供使用，就可以建立所設計的視覺表示法與設計的基礎 XML 來源之間來回傳送的 UI 設計：

[![在 [來源] 窗格中的範例 XML 來源](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

XML 來源所做的變更會立即呈現在**設計介面**; 上所做的變更**設計介面**導致顯示在 XML 來源**來源**窗格隨之更新。 當您變更中的 XML**來源**窗格、 自動完成和 IntelliSense 功能可用於接下來所述的 XML 為基礎的 UI 開發的速度。

以更高的巡覽式方便使用冗長的 XML 檔案時**來源**窗格支援 Visual Studio 捲軸 （如同在先前的螢幕擷取畫面的右邊）。 如需捲軸的詳細資訊，請參閱[自訂捲軸的追蹤程式碼如何](https://msdn.microsoft.com/library/dn237345.aspx)。


### <a name="autocompletion"></a>自動完成

當您開始輸入屬性名稱的小工具時，您可以按下<kbd>CTRL + 空格鍵</kbd>以查看可能的完成清單。 例如，輸入後`android:lay`在下列範例中 (後面輸入<kbd>CTRL + 空格鍵</kbd>)，會顯示下列清單：

[![版面配置屬性的自動完成](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

按下<kbd>ENTER</kbd>接受第一個列出的完成時，或使用方向鍵來捲動至所需的完成，然後按<kbd>ENTER</kbd>。 或者，您可以使用滑鼠來捲動，然後按一下所需的完成。

### <a name="intellisense"></a>IntelliSense

您輸入的小工具的新屬性，並開始將它指派值之後，IntelliSense 快顯的觸發程序的字元型別，並提供有效的值，若要使用該屬性的清單之後。 例如，輸入第一個雙括號之後`android:layout_width`在下列範例中，自動完成選取器隨即顯示，針對此寬度提供有效的選擇清單：

[![版面配置寬度的 IntelliSense 範例](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

（如上述螢幕擷取畫面中紅色所述），這個快顯視窗的底部有兩個按鈕。 按一下 **專案資源**左邊的按鈕會將清單限制為屬於的應用程式專案中，同時按一下資源**架構資源**右側按鈕會限制清單顯示從 framework 的可用資源。
這些按鈕切換為開啟或關閉： 您可以按一下他們再次停用篩選的動作，每個都提供。



## <a name="properties-pane"></a>屬性窗格

設計工具支援的小工具屬性，透過編輯**屬性**窗格： 

![[屬性] 視窗的螢幕擷取畫面](designer-basics-images/vs/08-property-pad.png)

列出的屬性**屬性**窗格中，變更依據小工具選取了**設計介面**。

### <a name="default-values"></a>預設值

大部分的 widget 的內容會在空白**屬性**視窗因為其值是繼承自選取 Android 的佈景主題。
**屬性**視窗只會顯示所選小工具明確設定的值; 它不會顯示繼承自佈景主題的值。

### <a name="referencing-resources"></a>參考的資源

某些屬性可以參考非版面配置檔案中所定義的資源 **.axml**檔案。 這種最常見的情況下都`string`和`drawable`資源。 不過，參考可以也用於其他資源，例如`Boolean`值和維度。 當某個屬性支援資源參考時，瀏覽圖示 （正方形） 會顯示屬性的文字項目旁邊。 這個按鈕會開啟時按下的資源選取器。

比方說，下列螢幕擷取畫面顯示可用的選項時按一下右邊的文字欄位的變暗的平方`Text`中的 widget**屬性**視窗：

[![範例文字選項清單](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

當**資源...** 按下時，**選取資源**此時會顯示對話方塊：

[![使用所列的幾個資源的範例資源螢幕擷取畫面](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

從這份清單中，您可以選取要用於該小工具，而不是硬式編碼的文字中的文字資源**屬性**窗格。 下一個範例所示的資源選取器`Src`屬性`ImageView`:

[![ImageView 清單圖示資源的資源選取器](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

按一下右邊的空正方形`Src`屬性會開啟**選取資源**資源清單，舉凡色彩 （如上所示） 可繪製資源來使用的對話方塊。


### <a name="boolean-property-references"></a>布林值屬性參考

*布林*屬性通常會選取為 [屬性] 視窗中的屬性旁邊的核取記號。 您可以指定`true`或`false`值，方法是選取或取消核取此核取方塊，或者您可以按一下屬性右邊的深色區域分布正方形選取的屬性參考。 在下列範例中，文字變更為全部大寫]，即可**文字全部大寫字**與 [選取相關聯的布林值屬性參照`TextView`:

![設定布林值屬性的範例](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>編輯內容內嵌

Android 設計工具支援上的某些屬性直接編輯**設計介面**（因此您不需要搜尋屬性清單中的這些屬性）。 可以直接編輯的屬性包括文字、 邊界和大小。

### <a name="text"></a>文字

有些小工具的文字屬性 (例如`Button`並`TextView`)，可以直接在編輯**設計介面**。 按兩下小工具會將它放置於編輯模式，如下所示：

![Hello 字串文字資源](designer-basics-images/vs/12-text-resource.png "資源")

您可以輸入新的文字值，或者您可以輸入新的資源字串。 在下列範例中，`@string/hello`資源會取代該文字， `CLICK THIS BUTTON`:

![Shift + enter 鍵可自動將文字連結到新的資源](designer-basics-images/vs/13-shift-enter-resource.png)

這項變更會儲存在 widget`text`屬性; 它不會修改指派給值`@string/hello`資源。
在新的文字字串時，您可以按下<kbd>Shift</kbd> +
<kbd>Enter</kbd>自動連結至新的資源的 輸入的文字。

### <a name="margin"></a>邊界

當您選取小工具時，設計工具就會顯示可讓您以互動方式變更的大小或邊界的小工具的控制代碼。 選取時，請按一下小工具將邊界編輯模式和大小編輯模式之間切換。

當您第一次按一下小工具時，會顯示的控點。 如果您將滑鼠移至其中一個控點時，設計工具要顯示的控點會變更的屬性 (如下所示的`layout_marginLeft`屬性):

![在設計工具中處理螢幕擷取畫面顯示邊界](designer-basics-images/vs/15-margin-handles.png)

如果已設定邊界，虛線會顯示，表示邊界所佔用的空間：

![範例中的虛線標記按鈕周圍的空間](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>大小

如先前所述，您可以按一下小工具，已選取時切換到大小編輯模式。 按一下三角形的控點來設定 指定維度的大小`wrap_content`:

![自動換行內容和調整大小控點](designer-basics-images/vs/17-wrap-content.png)

按一下 **包裝內容**控制代碼會壓縮該維度中的小工具，如此就不能大於必要包裝含括的內容。 在此範例中，以在下一步 的螢幕擷取畫面所示的水平方式會縮小按鈕的文字。

當 [大小] 值設定為**內容包裝**，在設計工具顯示的三角形的控制代碼，以相反方向變更的大小，以指向`match_parent`:

![比對父控制代碼](designer-basics-images/vs/18-match-parent.png)

按一下 **比對父**控點會還原該維度的大小，使其與父小工具相同。

此外，您可以拖曳循環的調整大小控點 （如上述螢幕擷取畫面所示） 來調整大小的任意 widget`dp`值。 當您這樣做時，同時**內容換行**和**相符項目父**控制代碼會顯示該維度的：

![循環的調整大小控點](designer-basics-images/vs/19-resize-dp.png)

並非所有的容器允許編輯`Size`widget。 例如，請注意，在以下螢幕擷取畫面與`LinearLayout`選取，調整大小控點不會出現：

![沒有調整大小控點](designer-basics-images/vs/20-no-resize-handles.png)


## <a name="document-outline"></a>文件大綱

**文件大綱**顯示版面配置的小工具階層架構。
在下列範例中，包含`LinearLayout`選取小工具：

![文件大綱的範例](designer-basics-images/vs/21-document-outline.png)

外框的所選小工具 (在此情況下， `LinearLayout`) 也上則醒目提示**設計介面**。 文件大綱 中的所選小工具與保持同步上與其對應項**設計介面**。 這可用於選取 檢視群組，並不一定能夠輕鬆選上**設計介面**。

**文件大綱**支援複製及貼上，或您可以使用拖曳和卸除。 從支援拖曳和置放**文件大綱**來**設計介面**以及從**設計介面**至**文件大綱**. 此外，在中的項目上按一下滑鼠右鍵**文件大綱**會顯示該項目的內容功能表 (相同的操作功能表，當您以滑鼠右鍵按一下該相同的小工具上時出現**設計介面**)。




# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="launching-the-designer"></a>啟動設計工具

建立版面配置，或按兩下現有的.axml 檔案就可以啟動時，會自動啟動設計工具。 例如，按兩下**Main.axml**中**資源 > 配置**資料夾將會載入設計工具，如下所示：

[![設計工具的螢幕，在 Visual Studio for Mac](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

同樣地，您可以加入新的版面配置，以滑鼠右鍵按一下**版面配置**中的資料夾**Solution Pad** ，然後選取**新增 > 新的檔案 > Android > 配置**:

[![加入新檔案 對話方塊](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

這會建立新的.axml 檔案，並將它載入到設計介面上。

## <a name="designer-features"></a>設計工具功能

設計工具包含數個區段支援及其各種功能，如下列螢幕擷取畫面所示：

[![設計工具窗格的圖表](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

當您編輯設計工具中的版面配置時，您可以使用下列功能來建立及設計您的設計：

-   **設計介面**&ndash;執行視覺方式建構使用者介面，方法是讓您的版面配置會如何出現在裝置上的可編輯的表示法。

-   **工具列**&ndash;顯示的選取器清單：**裝置**，**版本**，**佈景主題**，版面配置設定及動作列設定。 工具列也包含圖示以啟動 佈景主題編輯器及啟用材質設計格線。

-   **工具箱**&ndash;提供一份 widget 和版面配置，您可以拖放到設計介面上。

-   **屬性面板**&ndash;列出所選的小工具，來檢視及編輯的屬性。

-   **文件大綱**&ndash;顯示構成配置 widget 的樹狀目錄。 您可以按一下在樹狀目錄中，讓它在設計工具中選取的項目。 此外，按一下樹狀目錄中的項目載入的項目屬性 [屬性] 面板。

## <a name="toolbar"></a>工具列

工具列 （位於設計介面上方） 顯示組態選取器和工具功能表：

[![設計工具工具列的圖表](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

工具列提供下列功能的存取權：

-   **替代的版面配置選取器**&ndash;可讓您選取從不同的版面配置版本。

-   **裝置選取器**&ndash;定義一組特定的裝置，例如螢幕大小、 解析度和鍵盤可用性相關聯的限定詞。 您也可以新增和刪除新的裝置。

-   **Android 版本選取器**&ndash;版面配置的目標 Android 版本。 設計工具將呈現的版面配置，根據所選的 Android 版本而定。

-   **佈景主題選取器**&ndash;選取 UI 佈景主題，版面配置。

-   **組態選取器**&ndash;選取裝置設定，例如*直向*或是*橫向*。

-   **資源限定詞選項**&ndash;隨即開啟一個對話方塊，顯示下拉式功能表來選取*語言*， *UI 模式下*，*夜間模式*，及*圓螢幕*選項。

-   **動作列設定**&ndash;設定版面配置的動作列設定。

-   **佈景主題編輯器**&ndash;便會開啟*佈景主題編輯器*，這可讓您自訂的 選取的佈景主題項目。

-   **材質設計格線**&ndash;啟用或停用*材質設計格線*。 相鄰材質設計格線下拉式選單中的項目會開啟一個對話方塊，可讓您自訂的方格。

這些主題中的更詳細說明每一項功能：

[資源限定詞和視覺效果選項](~/android/user-interface/android-designer/resource-qualifiers.md)提供有關的詳細的資訊**裝置選取器**， **Android 版本選取器**，**佈景主題選取器**，**組態選取器**，**資源資格選項**，和**動作列設定**。

[替代的版面配置檢視](~/android/user-interface/android-designer/alternative-layout-views.md)說明如何使用**替代版面配置選取器**。

[材料設計功能](~/android/user-interface/android-designer/material-design-features.md)提供完整的概觀**佈景主題編輯器**並**材質設計格線**。

## <a name="design-surface"></a>設計介面

設計工具可讓您從 [工具箱] 拖曳至設計介面上拖放的 widget。 當與設計工具中的小工具互動 （透過加入新的 widget，或重新定位現有的） 時，垂直和水平列會顯示將可用的插入點。 在下列範例中，新`Button`小工具被拖曳至設計介面：

[![在設計介面上的範例插入行](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

此外，可複製小工具： 您可以使用複製和貼上複製的 widget 中，或者您可以拖曳和卸除現有的小工具時按下<kbd>Ctrl</kbd>索引鍵。

### <a name="context-menu-commands"></a>內容功能表命令

在設計介面和文件大綱 中使用操作功能表。 此功能表會顯示可供所選小工具和其容器，讓您更輕鬆地在容器 （這並不一定能夠輕鬆選設計介面上） 上執行作業的命令。 內容功能表中的範例如下：

[![以滑鼠右鍵按一下設計介面時，範例操作功能表](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

在此範例中，以滑鼠右鍵按一下`Button`開啟內容功能表，提供數個選項：

-   **LinearLayout** &ndash;開啟進行編輯的子功能表`LinearLayout`的父代`Button`。

-   **剪下**，**複製**，以及**刪除**&ndash;套用至以滑鼠右鍵按一下作業`Button`。

### <a name="zoom-controls"></a>縮放控制項

在設計介面支援透過數個控制項縮放，如所示：

[![設計介面的縮放控制項的圖表](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

這些控制項讓您更容易查看特定區域的設計工具中的使用者介面：

-   **反白顯示容器**&ndash;會反白顯示設計介面上的容器，使其更輕鬆地找出時放大和縮小。

-   **標準大小**&ndash;呈現版面配置像素的像素的以便您可以看到配置所選裝置的解析度所呈現的樣子。

-   **調整成視窗大小**&ndash;使整個版面配置會顯示在設計介面上設定的縮放層級。

-   **拉近**&ndash;放大以累加方式每按一下，放大版面配置。

-   **縮小**&ndash;縮小以累加方式每按一下，讓版面配置看起來比較小，在設計介面上。

請注意所選的縮放設定不會影響應用程式在執行階段的使用者介面。

## <a name="property-pad"></a>屬性面板

設計工具支援的小工具屬性，透過編輯**屬性輸入板**。 屬性面板變更依據設計工具介面中選取小工具所列出的屬性。 當`Button`選取在先前的範例時，該屬性`Button`widget 會顯示：

[![[屬性] 面板的螢幕擷取畫面](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>內容面板區段

[屬性] 面板會分成數個群組在一起的類似屬性的區段&ndash;這可讓您輕鬆地找到想要的屬性：

-   **Widget** &ndash;主要屬性的小工具，例如`id`， `visibility`，`text`等等。管理小工具的內容屬性通常會放在這裡。

-   **樣式**&ndash;這類變更的小工具的視覺外觀的屬性`font`， `text color`，`background`等等。

-   **版面配置**&ndash;設定小工具的大小與位置的屬性。

-   **捲軸**&ndash;捲動屬性。

-   **行為**&ndash;設定小工具的運作方式的旗標。

### <a name="default-values"></a>預設值

大部分的 widget 的內容會在空白**屬性輸入板**因為其值是繼承自選取 Android 的佈景主題。 **屬性輸入板**只會顯示所選小工具明確設定的值; 它不會顯示繼承自佈景主題的值。

### <a name="referencing-resources"></a>參考的資源

某些屬性可以參考非版面配置檔案中所定義的資源 **.axml**檔案。 這種最常見的情況下都`string`和`drawable`資源。 不過，參考可以也用於其他資源，例如`Boolean`值和維度。
當某個屬性支援資源參考，瀏覽圖示 (的省略符號， &hellip;) 屬性的文字項目旁邊會顯示。
按一下時，這個按鈕會開啟資源選擇器。

比方說，下列螢幕擷取畫面顯示可用的資源時按一下文字欄位的右邊的省略符號`Button`中的 widget**屬性輸入板**:

[![列出兩項資源的資源範例螢幕擷取畫面](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

下一個範例所示的資源選取器`Src`屬性`ImageView`:

[![ImageView 清單圖示資源的資源選取器](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>布林值屬性參考

*布林*屬性通常會顯示為 [屬性] 面板中的核取方塊。 當`Boolean`屬性支援資源參考、 小型的核取方塊出現在內容旁。 已檢查的核取方塊表示`true`空白的方塊表示`false`。 您可以也可以直接輸入值，像是`true`或`false`。 輸入您的滑鼠暫留會顯示小型文字欄位圖示。 如果您想要以手動方式輸入值，您可以按一下它。

[![設定布林值屬性的範例](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)


## <a name="grouped-properties"></a>群組的屬性

有些小工具有會群組在一起的多值屬性 (例如`Padding`，例如)。 這些屬性值會列在**屬性輸入板**單一、 可展開的資料列中。 其中部分屬性可以直接在編輯群組的資料列，例如`Padding`屬性如下所示：

[![邊框間距屬性的範例設定](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>編輯內容內嵌

Android Designer 支援直接編輯某些屬性在設計介面上的 （因此您不需要搜尋屬性清單中的這些屬性）。 可以直接編輯的屬性包括文字、 邊界和大小。

### <a name="text"></a>文字

有些小工具的文字屬性 (例如`Button`和`TextView`)，可以直接在設計介面上編輯。 按兩下小工具會將它放置於編輯模式，如下所示：

[![Hello 字串資源](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

您可以輸入新的文字值，或者您可以輸入新的資源字串。 在下列範例中，`@string/hello`資源會取代該文字， `CLICK THIS BUTTON`:

[![Shift + enter 鍵可自動將文字連結到新的資源](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

這項變更會儲存在 widget`text`屬性; 它不會修改指派給值`@string/hello`資源。
在新的文字字串時，您可以按下<kbd>Shift</kbd> +
<kbd>Enter</kbd>自動連結至新的資源的 輸入的文字。

### <a name="margin"></a>邊界

當您選取小工具時，設計工具就會顯示可讓您以互動方式變更的大小或邊界的小工具的控制代碼。 選取時，請按一下小工具將邊界編輯模式和大小編輯模式之間切換。

當您第一次按一下小工具時，會顯示的控點。 如果您將滑鼠移至其中一個控點時，設計工具要顯示的控點會變更的屬性 (如下所示的`layout_marginLeft`屬性):

[![在設計工具中處理螢幕擷取畫面顯示邊界](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

如果已設定邊界，虛線會顯示，表示邊界所佔用的空間：

[![範例中的虛線標記按鈕周圍的空間](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>大小

如先前所述，您可以按一下小工具，已選取時切換到大小編輯模式。 按一下三角形的控點來設定 指定維度的大小`wrap_content`:

[![自動換行內容和調整大小控點](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

按一下 **包裝內容**控制代碼會縮小小工具，這樣就不能大於不必括住的內容包裝該維度中。 在此範例中，以在下一步 的螢幕擷取畫面所示的水平方式會縮小按鈕的文字。

當 [大小] 值設定為**內容包裝**，在設計工具顯示的三角形的控制代碼，以相反方向變更的大小，以指向`match_parent`:

[![比對父控制代碼](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

按一下 **比對父**控點會還原該維度的大小，使其與父小工具相同。

此外，您可以拖曳循環的調整大小控點 （如上述螢幕擷取畫面所示） 來調整大小的任意 widget`dp`值。 當您這樣做時，同時**內容換行**和**相符項目父**控制代碼會顯示該維度的：

[![循環的調整大小控點](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

並非所有的容器允許編輯`Size`widget。 例如，請注意，在以下螢幕擷取畫面與`LinearLayout`選取，調整大小控點不會出現：

[![沒有調整大小控點](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>文件大綱

**文件大綱**顯示版面配置的小工具階層架構。
在下列範例中，包含`LinearLayout`選取小工具：

[![文件大綱](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

外框的所選小工具 (在此情況下， `LinearLayout`) 也在設計介面上則醒目提示。 文件大綱 中的所選小工具保持與設計介面上與其對應項同步。 這是適用於選取 檢視群組，並不一定能夠輕鬆選設計介面上。

文件大綱 支援複製並貼上，或者您可以使用拖曳和卸除。 拖放支援文件大綱至設計介面以及從設計介面，以文件大綱。 此外，以滑鼠右鍵按一下文件大綱 中的項目會顯示該項目 （相同的操作功能表出現時，相同的小工具的設計介面上以滑鼠右鍵按一下） 的操作功能表。

-----
