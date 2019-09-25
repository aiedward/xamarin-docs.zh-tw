---
title: Xamarin. Android Designer 的基本概念
description: 本主題介紹 Xamarin. Android Designer 功能、說明如何啟動設計工具、描述 Design Surface，以及如何使用 [屬性] 窗格來編輯 widget 屬性的詳細資料。
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/05/2018
ms.openlocfilehash: 0089f8681142b1d3d3446c21ba89ab2648e4a638
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2019
ms.locfileid: "70761412"
---
# <a name="xamarinandroid-designer-basics"></a>Xamarin. Android Designer 的基本概念

_本主題介紹 Xamarin. Android Designer 功能、說明如何啟動設計工具、描述 Design Surface，以及如何使用 [屬性] 窗格來編輯 widget 屬性的詳細資料。_

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>啟動設計工具

設計工具會在建立配置時自動啟動，也可以按兩下現有的版面配置檔案來啟動。 例如，按兩下 [**資源 >** 配置] 資料夾中的 [ **activity_main** ]，將會載入設計工具，如下列螢幕擷取畫面所示：

[![Visual Studio 中的設計工具畫面](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

同樣地，您也可以用滑鼠右鍵按一下 **方案總管**中**的 配置** 資料夾，然後選取 **加入 > 新專案，以加入新的版面配置。> Android 版面**配置：

[![[加入新專案] 對話方塊](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

這會建立新的**axml**配置檔案，並將其載入設計工具中。

> [!TIP]
> 較新版的 Visual Studio 支援在 Android Designer 中開啟 .xml 檔案。
>
> Android Designer 同時支援 .axml 和 .xml 檔案。

## <a name="designer-features"></a>設計工具功能

設計工具是由數個支援其各種功能的區段所組成，如下列螢幕擷取畫面所示：

[![設計師窗格的圖表](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

當您在設計工具中編輯版面配置時，您會使用下列功能來建立及塑造您的設計：

- **Design Surface**&ndash;提供版面配置在裝置上的顯示方式，藉此協助視覺結構化使用者介面。 **Design Surface**會顯示在 [**設計] 窗格**內（**設計工具工具列**位於其上方）。

- **來源窗格**提供基礎 XML 來源的視圖，其對應于 Design Surface 上呈現的設計。 &ndash;

- **設計工具工具列**&ndash;顯示選取器清單：**裝置**、**版本**、**主題**、版面配置設定和動作列設定。 **設計工具工具列**也包含用來啟動主題編輯器和啟用 [材質設計] 方格的圖示。

- **工具箱**提供 widget 和版面配置的清單，您可以將其拖放到**Design Surface 上。** &ndash;

- **屬性視窗**&ndash;列出所選 widget 的屬性，以便進行流覽和編輯。

- **檔大綱**&ndash;顯示組成版面配置的 widget 樹狀結構。 您可以按一下樹狀結構中的專案，使其在**Design Surface**上選取。 此外，按一下樹狀結構中的專案，就會將專案的屬性載入 [**屬性**] 視窗中。

## <a name="design-surface"></a>設計介面

設計工具可讓您將 widget 從 [工具箱] 拖放到**Design Surface**上。 當您在設計工具中與 widget 互動時（藉由加入新的 widget 或重新置放現有的 widget），會顯示垂直和水平線條來標示可用的插入點。 在下列範例中，會將`Button`新的小工具拖曳至**Design Surface**：

[![Design Surface 上的插入行範例](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

此外，您可以複製小工具：您可以使用 [複製] 和 [貼上] 來複製 widget，也可以在按下<kbd>CTRL</kbd>鍵時拖放現有的 widget。

### <a name="designer-toolbar"></a>設計工具工具列

**設計工具工具列**（位於**Design Surface**上方）會顯示設定選取器和工具功能表：

[![設計師工具列的圖表](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

**設計工具工具列**提供下列功能的存取權：

- **替代版面配置選取器**&ndash;可讓您從不同的版面配置版本中選取。

- **裝置選取器**&ndash;定義與特定裝置相關聯的一組限定詞（例如螢幕大小、解析度和鍵盤可用性）。 您也可以新增和刪除新的裝置。

- **Android 版本選取器**&ndash;版面配置的目標 Android 版本。 設計工具會根據選取的 Android 版本來轉譯版面配置。

- **主題選取器**&ndash;選取版面配置的 UI 主題。

- 設定**選取器**選取裝置設定，例如 [直向 *] 或 [* 橫向]。 &ndash;

- **資源辨識符號選項**開啟一個對話方塊，其中會顯示用於選取 [語言]、[UI 模式]、[夜間模式] 和 [圓形螢幕] 選項的下拉式功能表。 &ndash;

- **動作列設定**&ndash;設定版面配置的動作列設定。

- **主題編輯器**開啟 [*主題編輯器*]，讓您可以自訂所選主題的元素。 &ndash;

- **材質設計方格**啟用或停用 [*材質設計] 方格。* &ndash; [材質設計] 方格旁的下拉式功能表專案會開啟一個對話方塊，讓您自訂格線。

這些功能將在下列主題中更詳細地說明：

- [資源限定詞和視覺效果選項](~/android/user-interface/android-designer/resource-qualifiers.md)提供有關**裝置選取器**、 **Android 版本選取器**、**主題選取器**、設定**選取器**、資源限定的詳細資訊**選項**，並**動作列設定**。

- [替代的版面配置視圖](~/android/user-interface/android-designer/alternative-layout-views.md)說明如何使用**替代的版面配置選取器**。

- [Android Designer 材質設計功能](~/android/user-interface/android-designer/material-design-features.md)提供**主題編輯器**和**材質設計方格**的完整總覽。

### <a name="context-menu-commands"></a>內容功能表命令

在**Design Surface**和**檔大綱**中都可以使用內容功能表。 此功能表會顯示所選 widget 及其容器可用的命令，讓您更輕鬆地在容器上執行作業（這在**Design Surface**上不一定容易選取）。 以下是內容功能表的範例：

[![以滑鼠右鍵按一下 Design Surface 時的內容功能表範例](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

在此範例中，以滑鼠右鍵`TextView`按一下開啟的內容功能表，其中提供數個選項：

- **LinearLayout**開啟子功能表以`LinearLayout`編輯的父系`TextView`。 &ndash;

- **刪除**、**複製**和**剪** &ndash;下套用至以滑鼠右鍵按一下`TextView`的作業。

### <a name="zoom-controls"></a>縮放控制項

**Design Surface**支援透過數個控制項縮放，如下所示：

[![Design Surface 縮放控制項的圖表](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

這些控制項可讓您更輕鬆地在設計工具中查看使用者介面的特定區域：

- **反白顯示容器**反白顯示 Design Surface 上的容器，以便在放大和縮小時更容易找到。 &ndash;

- **一般大小**&ndash;呈現圖元的版面配置，讓您可以查看版面配置如何查看所選裝置的解析度。

- **調整成視窗大小**&ndash;設定縮放層級，讓整個配置顯示在 Design Surface 上。

- **放大**&ndash;按每次點擊以累加放大，放大版面配置。

- **縮小**&ndash;在每次按一下時，以累加方式縮小，讓版面配置在 Design Surface 上顯示為較小。

請注意，所選的縮放設定不會影響執行時間應用程式的使用者介面。

## <a name="switching-between-design-and-source-panes"></a>在 [設計] 和 [來源] 窗格之間切換

在 [**設計**] 和 [**來源**] 窗格之間的中央寬頻中，有幾個按鈕可用來修改 [**設計**] 和 [**來源**] 窗格的顯示方式：

[![窗格顯示按鈕位置](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

這些按鈕會執行下列動作：

- **設計**這個最上層的按鈕 [ **design**（設計）] 會選取 [設計] 窗格。 &ndash; 按一下這個按鈕時，[**工具箱**] 和 [**屬性**] 窗格就會啟用，而且不會顯示 [**文字編輯器] 工具列**。 按一下 [折迭 **] 按鈕（** 如下所示）時，[**設計**] 窗格會單獨顯示，而不會出現 [**來源**] 窗格。

- **交換窗格**這個按鈕（類似兩個反向箭號）會交換 [設計] 和 [來源] 窗格，讓 [來源] 窗格位於左側，而 [設計] 窗格位於右側。 &ndash; 再次按一下該窗格，就會將它們重新交換到其原始位置。

- **來源**這個按鈕（類似兩個相反的角括弧）會選取 [來源] 窗格。 &ndash; 按一下這個按鈕時，[**工具箱**] 和 [**屬性**] 窗格會停用，而且 [**文字編輯器] 工具列**會顯示在 Visual Studio 的頂端。 按一下 [折迭] 按鈕（如下所示）時，按一下 [**來源** **] 按鈕會**顯示 [**來源**] 窗格，而不是 [**設計**] 窗格。

- **垂直分割**這個按鈕（類似于分隔號）會並排顯示 [**設計**] 和 [來源] 窗格。 &ndash; 這是預設的相片順序。

- **水準分割**這個按鈕（類似于水準橫條）會在 [**來源**] 窗格上方顯示 [設計] 窗格。 &ndash; 您可以按一下 [**交換窗格**]，將 [**來源**] 窗格放在**設計**窗格的上方。

- 折迭**窗格**這個按鈕（類似兩個右角括弧）會「折迭」 [設計] 和 [**來源**] 的雙重窗格顯示成其中一個窗格的單一視圖。 &ndash;
    這個按鈕會變成 [**展開窗格]** 按鈕（類似兩個左角括弧），您可以按一下這個按鈕，將視圖返回雙窗格（**設計**和**來源**）顯示模式。

按一下 [折迭**窗格]** 時，只會顯示 [**設計**] 窗格。 不過，您可以按一下 [**來源**] 按鈕，改為只查看 [**來源**] 窗格。 再次按一下 [**設計**] 按鈕，返回 [**設計**] 窗格。

## <a name="source-pane"></a>來源窗格

[**來源**] 窗格會顯示**Design Surface**上所顯示設計的基礎 XML 來源。 因為這兩個視圖都可以同時使用，所以您可以在設計的視覺標記法與設計的基礎 XML 來源之間來回切換，藉以建立 UI 設計：

[![原始碼窗格中的 XML 來源範例](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

對 XML 來源所做的變更會立即呈現在**Design Surface**上;對**Design Surface**所做的變更，會據以更新 [**來源**] 窗格中顯示的 XML 來源。 當您對 [**來源**] 窗格中的 xml 進行變更時，可以使用自動完成和 IntelliSense 功能來加速以 XML 為基礎的 UI 開發，如下所述。

若要在使用長 XML 檔案時更方便導覽，[**來源**] 窗格支援 Visual Studio 捲軸（如上一個螢幕擷取畫面右側所示）。 如需有關捲軸的詳細資訊，請參閱[如何透過自訂捲軸來追蹤程式碼](https://msdn.microsoft.com/library/dn237345.aspx)。

### <a name="autocompletion"></a>自動完成

當您開始輸入 widget 的屬性名稱時，可以按<kbd>CTRL + 空格鍵</kbd>查看可能的完成清單。 例如，在下列範例`android:lay`中輸入之後（後面接著輸入<kbd>CTRL + 空格鍵</kbd>），就會顯示下列清單：

[![版面配置屬性的自動完成](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

按<kbd>enter</kbd>以接受第一個列出的完成，或使用方向鍵來流覽至所需的完成，然後按<kbd>enter</kbd>鍵。 或者，您可以使用滑鼠來滾動至，然後按一下所需的完成。

### <a name="intellisense"></a>IntelliSense

在您輸入 widget 的新屬性並開始為其指派值之後，IntelliSense 會在輸入觸發程式字元後出現，並提供有效的值清單供該屬性使用。 例如， `android:layout_width`在下列範例中輸入第一個雙引號之後，會彈出自動完成的選取器來提供此寬度的有效選項清單：

[![版面配置寬度的 IntelliSense 範例](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

此快顯的底部有兩個按鈕（如上面螢幕擷取畫面中的紅色所示）。 按一下左側的 [**專案資源**] 按鈕，會將清單限制為屬於應用程式專案的資源，而按一下右側的 [**架構資源**] 按鈕會限制清單，以顯示架構中可用的資源。
這些按鈕會切換為開啟或關閉：您可以再次按一下它們，以停用每個按鈕所提供的篩選動作。

## <a name="properties-pane"></a>屬性窗格

設計工具支援透過 [**屬性**] 窗格編輯 widget 屬性： 

![屬性視窗的螢幕擷取畫面](designer-basics-images/vs/08-property-pad.png)

[**屬性**] 窗格中列出的屬性會根據**Design Surface**上選取的 widget 而變更。

### <a name="default-values"></a>預設值

大部分 widget 的屬性在 [**屬性**] 視窗中都是空白的，因為它們的值會繼承自選取的 Android 主題。
[**屬性**] 視窗只會顯示針對所選 widget 明確設定的值;它不會顯示繼承自主題的值。

### <a name="referencing-resources"></a>參考資源

有些屬性可以參考**axml**檔案以外之檔案中所定義的資源。 這種類型最常見的情況是`string`和`drawable`資源。 不過，參考也可以用於其他資源，例如`Boolean`值和維度。 當屬性支援資源參考時，會在屬性的文字專案旁顯示流覽圖示（正方形）。 按一下此按鈕時，會開啟資源選取器。

例如，下列螢幕擷取畫面顯示當您在 [ `Text` **屬性**] 視窗中按一下小工具文字欄位右邊的暗角方形時可用的選項：

[![文字選項的範例清單](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

按一下 [**資源 ...** ] 時，會顯示 [**選取資源**] 對話方塊：

[![列出數個資源的範例資源螢幕擷取畫面](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

在這份清單中，您可以選取要用於該 widget 的文字資源，而不是在 [**屬性**] 窗格中對文字進行硬式編碼。 下一個範例說明的`Src`屬性`ImageView`的資源選取器：

[![ImageView 的資源選取器清單圖示資源](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

按一下`Src`屬性右邊的空白方形，會開啟 [**選取資源**] 對話方塊，其中包含從色彩（如上方所示）到可繪製資源的資源清單。

### <a name="boolean-property-references"></a>布林值屬性參考

*布林值*屬性通常會在屬性視窗中的屬性旁選取為核取記號。 您可以藉由`true`勾選`false`或取消核取此核取方塊來指定或值，也可以按一下屬性右邊的深色實心方形來選取屬性參考。 在下列範例中，按一下與所選`TextView`相關聯的 [**全部大寫**布林值屬性參考] 文字，即可將文字變更為 [全部大寫]：

![設定布林值屬性的範例](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>編輯內嵌的屬性

Android Designer 支援對**Design Surface**上的特定屬性進行直接編輯（因此，您不需要在屬性清單中搜尋這些屬性）。 可以直接編輯的屬性包括文字、邊界和大小。

### <a name="text"></a>Text

某些 widget （例如`Button`和`TextView`）的文字屬性可以直接在**Design Surface**上編輯。 按兩下 widget 會使其進入編輯模式，如下所示：

![Hello 字串的文字資源](designer-basics-images/vs/12-text-resource.png "文字資源")

您可以輸入新的文字值，也可以輸入新的資源字串。 在下列範例中， `@string/hello`資源會取代為下列`CLICK THIS BUTTON`文字：

![Shift + Enter 可自動將文字連結至新的資源](designer-basics-images/vs/13-shift-enter-resource.png)

這項變更會儲存在 widget 的`text`屬性中，而不會修改指派`@string/hello`給資源的值。
當您輸入新的文字字串時，您可以按<kbd>Shift</kbd>  +
 <kbd>enter</kbd>鍵，將輸入的文字自動連結到新的資源。

### <a name="margin"></a>邊界

當您選取 widget 時，設計工具會顯示可讓您以互動方式變更小工具大小或邊界的控點。 選取 widget 時，會在邊界編輯模式與大小編輯模式之間切換。

當您第一次按一下 widget 時，會顯示邊界控點。 如果您將滑鼠移至其中一個控點，則設計工具會顯示控制碼將變更的屬性（如下所示`layout_marginLeft`的屬性）：

![螢幕擷取畫面，顯示設計工具中的邊界控點](designer-basics-images/vs/15-margin-handles.png)

如果已設定邊界，則會顯示點線，表示邊界所佔用的空間：

![標示按鈕周圍空格的虛線範例](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>大小

如先前所述，您可以在選取小工具時，切換到大小編輯模式。 按一下三角形控點，將指定維度的大小設定為`wrap_content`：

![將內容換行並調整大小控點](designer-basics-images/vs/17-wrap-content.png)

按一下 [**換行內容**] 控點會壓縮該維度中的小工具，使其不會大於包裝所包含內容的必要項。 在此範例中，按鈕文字會水準縮小，如下一個螢幕擷取畫面所示。

當 [大小] 值設定為 [**換行內容**] 時，設計工具會顯示三角形控制碼，指向相反的方向`match_parent`，將大小變更為：

![符合父控制碼](designer-basics-images/vs/18-match-parent.png)

按一下 [**符合] 父代**控點會還原該維度中的大小，使其與父 widget 相同。

此外，您也可以拖曳迴圈調整大小控點（如上述螢幕擷取畫面所示），將 widget 的`dp`大小調整為任意值。 當您這麼做時，會針對該維度顯示 [**包裝內容**] 和 [**符合] 父代**控制碼：

![迴圈調整大小控點](designer-basics-images/vs/19-resize-dp.png)

並非所有容器都允許編輯`Size` widget 的。 例如，請注意，在下方的螢幕擷取畫面中`LinearLayout` ，如果有選取的，則不會顯示調整大小控點：

![沒有調整大小控點](designer-basics-images/vs/20-no-resize-handles.png)

## <a name="document-outline"></a>文件大綱

[**檔大綱**] 會顯示版面配置的 widget 階層。
在下列範例中，會選取`LinearLayout`包含的 widget：

![檔大綱範例](designer-basics-images/vs/21-document-outline.png)

選取的 widget （在此案例中為`LinearLayout`）的外框也會反白顯示在**Design Surface**上。 在 [檔大綱] 中選取的 widget 會與**Design Surface**上的對應項保持同步。 這適用于選取 [視圖群組]，這不一定能輕易地在**Design Surface**上進行選取。

[**檔大綱**] 支援複製和貼上，或者您可以使用拖放。 從**檔大綱**支援拖放至**Design Surface** ，以及從**Design Surface**到**檔大綱**。 此外，以滑鼠右鍵按一下 [**檔大綱**] 中的專案，就會顯示該專案的操作功能表（當您以滑鼠右鍵按一下**Design Surface**上相同的 widget 時，會出現相同的操作功能表）。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="launching-the-designer"></a>啟動設計工具

建立版面配置時，會自動啟動設計工具，也可以按兩下現有的 axml 檔案來啟動。 例如，按兩下  **Resources > Layout**  資料夾中的  **axml** ，將會載入設計工具，如下所示：

[![Visual Studio for Mac 中的設計工具畫面](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

同樣地，您也可以用滑鼠右鍵按一下  **Solution Pad**中的 **版面**配置 資料夾，然後選取 **新增 > 新檔案 > Android >** 配置 來新增版面配置：

[![[新增檔案] 對話方塊](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

這會建立新的 axml 檔案，並將它載入至 Design Surface。

> [!TIP]
> 較新版的 Visual Studio 支援在 Android Designer 中開啟 .xml 檔案。
>
> Android Designer 同時支援 .axml 和 .xml 檔案。

## <a name="designer-features"></a>設計工具功能

設計工具是由數個支援其各種功能的區段所組成，如下列螢幕擷取畫面所示：

[![設計師窗格的圖表](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

當您在設計工具中編輯版面配置時，您會使用下列功能來建立及塑造您的設計：

- **Design Surface**&ndash;提供版面配置在裝置上的顯示方式，藉此協助視覺結構化使用者介面。

- **工具列**&ndash;顯示選取器清單：**裝置**、**版本**、**主題**、版面配置設定和動作列設定。 工具列也包含用來啟動主題編輯器和啟用 [材質設計] 方格的圖示。

- **工具箱**&ndash;提供 widget 和版面配置的清單，您可以將其拖放到 Design Surface 上。

- **屬性 Pad**&ndash;列出所選 widget 的屬性，以便進行流覽和編輯。

- **檔大綱**&ndash;顯示組成版面配置的 widget 樹狀結構。 您可以按一下樹狀結構中的專案，使其在設計工具中選取。 此外，按一下樹狀結構中的專案，就會將專案的屬性載入至屬性 Pad。

## <a name="toolbar"></a>工具列

工具列（位於 Design Surface 上方）會顯示設定選取器和工具功能表：

[![設計師工具列的圖表](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

工具列可讓您存取下列功能：

- **替代版面配置選取器**&ndash;可讓您從不同的版面配置版本中選取。

- **裝置選取器**&ndash;定義一組與特定裝置相關聯的限定詞，例如螢幕大小、解析度和鍵盤可用性。 您也可以新增和刪除新的裝置。

- **Android 版本選取器**&ndash;版面配置的目標 Android 版本。 設計工具會根據選取的 Android 版本來轉譯版面配置。

- **主題選取器**&ndash;選取版面配置的 UI 主題。

- 設定**選取器**選取裝置設定，例如 [直向 *] 或 [* 橫向]。 &ndash;

- **資源辨識符號選項**開啟一個對話方塊，其中會顯示用於選取 [語言]、[UI 模式]、[夜間模式] 和 [圓形螢幕] 選項的下拉式功能表。 &ndash;

- **動作列設定**&ndash;設定版面配置的動作列設定。

- **主題編輯器**開啟 [*主題編輯器*]，讓您可以自訂所選主題的元素。 &ndash;

- **材質設計方格**啟用或停用 [*材質設計] 方格。* &ndash; [材質設計] 方格旁的下拉式功能表專案會開啟一個對話方塊，讓您自訂格線。

這些功能將在下列主題中更詳細地說明：

[資源限定詞和視覺效果選項](~/android/user-interface/android-designer/resource-qualifiers.md)提供有關**裝置選取器**、 **Android 版本選取器**、**主題選取器**、設定**選取器**、資源限定的詳細資訊**選項**，並**動作列設定**。

[替代的版面配置視圖](~/android/user-interface/android-designer/alternative-layout-views.md)說明如何使用**替代的版面配置選取器**。

[材質設計功能](~/android/user-interface/android-designer/material-design-features.md)提供 [**主題編輯器**] 和 [**材質設計] 方格**的全面總覽。

## <a name="design-surface"></a>設計介面

設計工具可讓您將 widget 從 [工具箱] 拖放到 Design Surface 上。 當您在設計工具中與 widget 互動時（藉由加入新的 widget 或重新置放現有的 widget），會顯示垂直和水平線條來標示可用的插入點。 在下列範例中，會將`Button`新的小工具拖曳至 Design Surface：

[![Design Surface 上的插入行範例](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

此外，您可以複製小工具：您可以使用 [複製] 和 [貼上] 來複製 widget，也可以在按下<kbd>Ctrl</kbd>鍵時拖放現有的 widget。

### <a name="context-menu-commands"></a>內容功能表命令

在 Design Surface 和檔大綱中都可以使用內容功能表。 此功能表會顯示所選 widget 及其容器可用的命令，讓您更輕鬆地在容器上執行作業（這在 Design Surface 上不一定容易選取）。 以下是內容功能表的範例：

[![以滑鼠右鍵按一下 Design Surface 時的內容功能表範例](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

在此範例中，以滑鼠右鍵`Button`按一下開啟的內容功能表，其中提供數個選項：

- **LinearLayout**開啟子功能表以`LinearLayout`編輯的父系`Button`。 &ndash;

- 適用于以滑鼠右鍵 &ndash;按一下`Button`的**剪**下、**複製**和刪除作業。

### <a name="zoom-controls"></a>縮放控制項

Design Surface 支援透過數個控制項縮放，如下所示：

[![Design Surface 縮放控制項的圖表](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

這些控制項可讓您更輕鬆地在設計工具中查看使用者介面的特定區域：

- **反白顯示容器**&ndash;反白顯示 Design Surface 上的容器，以便在放大和縮小時更容易找到。

- **一般大小**&ndash;呈現圖元的版面配置，讓您可以查看版面配置如何查看所選裝置的解析度。

- **調整成視窗大小**&ndash;設定縮放層級，讓整個配置顯示在 Design Surface 上。

- **放大**&ndash;按每次點擊以累加放大，放大版面配置。

- **縮小**&ndash;在每次按一下時，以累加方式縮小，讓版面配置在 Design Surface 上顯示為較小。

請注意，所選的縮放設定不會影響執行時間應用程式的使用者介面。

## <a name="property-pad"></a>屬性 Pad

設計工具支援透過**屬性 Pad**編輯 widget 屬性。 根據在設計工具介面中選取的 widget，屬性 Pad 中列出的屬性會變更。 選取上述`Button`範例中的時，會顯示該`Button` widget 的屬性：

[![屬性 pad 的螢幕擷取畫面](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>屬性 Pad 區段

屬性 Pad 分為數個區段，將類似的屬性群組在&ndash;一起，讓您更容易找到感關注的屬性：

- **Widget**Widget 的主要屬性， `id`例如、 `visibility`、 `text`等。 &ndash;管理 widget 內容的屬性通常會放在這裡。

- **樣式**變更小工具視覺外觀的屬性， `font`例如、 `text color`、 `background`等。 &ndash;

- **版面**配置&ndash;設定 widget 位置和大小的屬性。

- **Scroll**&ndash;滾動屬性。

- **行為**&ndash;設定 widget 運作方式的旗標。

### <a name="default-values"></a>預設值

大部分 widget 的屬性在**屬性 Pad**中都是空白的，因為其值繼承自選取的 Android 主題。 **屬性 Pad**只會顯示針對所選 widget 明確設定的值;它不會顯示繼承自主題的值。

### <a name="referencing-resources"></a>參考資源

有些屬性可以參考**axml**檔案以外之檔案中所定義的資源。 這種類型最常見的情況是`string`和`drawable`資源。 不過，參考也可以用於其他資源，例如`Boolean`值和維度。
當屬性支援資源參考時，會在屬性的文字專案旁&hellip;顯示流覽圖示（省略號）。
按一下時，此按鈕會開啟資源選取器。

例如，下列螢幕擷取畫面顯示在`Button` **屬性 Pad**中按一下小工具文字欄位右邊的省略號時可用的資源：

[![列出兩個資源的範例資源螢幕擷取畫面](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

下一個範例說明的`Src`屬性`ImageView`的資源選取器：

[![ImageView 的資源選取器清單圖示資源](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>布林值屬性參考

*布林值*屬性通常會在屬性 Pad 中顯示為核取方塊。 `Boolean`當屬性支援資源參考時，屬性旁會出現一個小核取方塊。 核取的複選`true`框表示，而空白`false`方塊則表示。 您也可以直接輸入值，例如`true`或。 `false` 將滑鼠停留在輸入上方會顯示一個小型文字欄位圖示。 如果您想要手動輸入值，可以按一下它。

[![設定布林值屬性的範例](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)

## <a name="grouped-properties"></a>群組屬性

某些 widget 具有多值屬性，會群組在一起（ `Padding`例如）。 這些屬性值會列于**屬性 Pad**的單一可擴充資料列中。 其中一些屬性可以直接在群組的資料列中編輯，例如如下所`Padding`示的屬性：

[![填補屬性的範例設定](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>編輯內嵌的屬性

Android Designer 支援對 Design Surface 上的特定屬性進行直接編輯（因此，您不需要在屬性清單中搜尋這些屬性）。 可以直接編輯的屬性包括文字、邊界和大小。

### <a name="text"></a>Text

某些 widget （例如`Button`和`TextView`）的文字屬性可以直接在 Design Surface 上編輯。 按兩下 widget 會使其進入編輯模式，如下所示：

[![Hello 字串的文字資源](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

您可以輸入新的文字值，也可以輸入新的資源字串。 在下列範例中， `@string/hello`資源會取代為下列`CLICK THIS BUTTON`文字：

[![Shift + Enter 可自動將文字連結至新的資源](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

這項變更會儲存在 widget 的`text`屬性中，而不會修改指派`@string/hello`給資源的值。
當您輸入新的文字字串時，您可以按<kbd>Shift</kbd>  +
 <kbd>enter</kbd>鍵，將輸入的文字自動連結到新的資源。

### <a name="margin"></a>邊界

當您選取 widget 時，設計工具會顯示可讓您以互動方式變更小工具大小或邊界的控點。 選取 widget 時，會在邊界編輯模式與大小編輯模式之間切換。

當您第一次按一下 widget 時，會顯示邊界控點。 如果您將滑鼠移至其中一個控點，則設計工具會顯示控制碼將變更的屬性（如下所示`layout_marginLeft`的屬性）：

[![螢幕擷取畫面，顯示設計工具中的邊界控點](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

如果已設定邊界，則會顯示點線，表示邊界所佔用的空間：

[![標示按鈕周圍空格的虛線範例](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>大小

如先前所述，您可以在選取小工具時，切換到大小編輯模式。 按一下三角形控點，將指定維度的大小設定為`wrap_content`：

[![將內容換行並調整大小控點](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

按一下 [**換行內容**] 控點會壓縮該維度中的小工具，如此一來，就不會再將封閉的內容換行。 在此範例中，按鈕文字會水準縮小，如下一個螢幕擷取畫面所示。

當 [大小] 值設定為 [**換行內容**] 時，設計工具會顯示三角形控制碼，指向相反的方向`match_parent`，將大小變更為：

[![符合父控制碼](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

按一下 [**符合] 父代**控點會還原該維度中的大小，使其與父 widget 相同。

此外，您也可以拖曳迴圈調整大小控點（如上述螢幕擷取畫面所示），將 widget 的`dp`大小調整為任意值。 當您這麼做時，會針對該維度顯示 [**包裝內容**] 和 [**符合] 父代**控制碼：

[![迴圈調整大小控點](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

並非所有容器都允許編輯`Size` widget 的。 例如，請注意，在下方的螢幕擷取畫面中`LinearLayout` ，如果有選取的，則不會顯示調整大小控點：

[![沒有調整大小控點](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>文件大綱

[**檔大綱**] 會顯示版面配置的 widget 階層。
在下列範例中，會選取`LinearLayout`包含的 widget：

[![檔大綱](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

選取的 widget （在此案例中為`LinearLayout`）的外框也會反白顯示在 Design Surface 上。 在 [檔大綱] 中選取的 widget 會與 Design Surface 上的對應項保持同步。 這適用于選取 [視圖群組]，這不一定能輕易地在 Design Surface 上進行選取。

[檔大綱] 支援複製和貼上，或者您可以使用拖放。 從檔大綱支援拖放至 Design Surface，以及從 Design Surface 到檔大綱。 此外，以滑鼠右鍵按一下 [檔大綱] 中的專案，就會顯示該專案的操作功能表（當您以滑鼠右鍵按一下 Design Surface 上相同的 widget 時，會出現相同的操作功能表）。

-----
