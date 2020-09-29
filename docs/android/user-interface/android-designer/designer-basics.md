---
title: Xamarin. Android Designer 基本概念
description: 本主題介紹 Xamarin. Android Designer 功能、說明如何啟動設計工具、描述 Design Surface，以及詳細說明如何使用 [屬性] 窗格來編輯 widget 屬性。
ms.prod: xamarin
ms.assetid: 48B20C9A-B2A2-AE82-76B2-A3C1E5A4050D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/05/2018
ms.openlocfilehash: e4d4ffe70db8f79ddf5e8f7a8ca1bda0fdc5aa11
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458514"
---
# <a name="xamarinandroid-designer-basics"></a>Xamarin. Android Designer 基本概念

_本主題介紹 Xamarin. Android Designer 功能、說明如何啟動設計工具、描述 Design Surface，以及詳細說明如何使用 [屬性] 窗格來編輯 widget 屬性。_

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="launching-the-designer"></a>啟動設計工具

設計工具會在版面配置建立時自動啟動，也可以藉由按兩下現有的版面配置檔案來啟動。 例如，按兩下 [ **Resources > Layout** ] 資料夾中的**activity_main .axml** ，將會載入設計工具，如下列螢幕擷取畫面所示：

[![Visual Studio 中的設計工具畫面](designer-basics-images/vs/01-open-designer-sml.png)](designer-basics-images/vs/01-open-designer.png#lightbox)

同樣地，您也可以在**方案總管**中的**版面**配置資料夾上按一下滑鼠右鍵，然後選取 [**加入 > 新專案] > Android 版面**配置，以加入新的版面配置：

[![[新增項目] 對話方塊](designer-basics-images/vs/02-add-new-layout-sml.png)](designer-basics-images/vs/02-add-new-layout.png#lightbox)

這會建立新的 **.axml** 版面配置檔案，並將它載入至設計工具。

> [!TIP]
> 較新版的 Visual Studio 支援在 Android Designer 中開啟 .xml 檔案。
>
> Android Designer 同時支援 .axml 和 .xml 檔案。

## <a name="designer-features"></a>設計工具功能

設計工具是由數個支援其各種功能的區段所組成，如下列螢幕擷取畫面所示：

[![設計工具窗格的圖表](designer-basics-images/vs/03-designer-features-sml.png)](designer-basics-images/vs/03-designer-features.png#lightbox)

當您在設計工具中編輯版面配置時，您會使用下列功能來建立和塑造設計：

- **Design Surface** &ndash; 藉由提供您可編輯的外觀，讓您知道配置在裝置上的顯示方式，以促進使用者介面的視覺結構設計。 **Design Surface**會顯示在**設計窗格**中， (**設計工具工具列**位於其) 的上方。

- **來源窗格** &ndash; 提供基礎 XML 來源的視圖，該來源對應于 **Design Surface**上所呈現的設計。

- **設計工具工具列** &ndash; 顯示選取器清單： [ **裝置**]、[ **版本**]、[ **主題**]、[版面配置設定] 和 [動作列] 設定。 **設計工具工具列**也包含用於啟動主題編輯器，以及啟用材質設計方格的圖示。

- **工具箱** &ndash; 提供小工具和版面配置清單，您可以將其拖放到 **Design Surface**上。

- **屬性視窗** &ndash; 列出所選取 widget 的屬性以進行流覽和編輯。

- **檔大綱** &ndash; 顯示組成版面配置的 widget 樹狀目錄。 您可以按一下樹狀目錄中的專案，使其在 **Design Surface**上進行選取。 此外，按一下樹狀結構中的專案，就會將專案的屬性載入至 [ **屬性** ] 視窗。

## <a name="design-surface"></a>設計介面

設計工具可讓您將 widget 從 [工具箱] 拖放到 **Design Surface**上。 當您在設計工具中與 widget 互動 (藉由加入新的 widget 或重新置放現有的小工具) ，會顯示垂直和水平線條來標示可用的插入點。 在下列範例中，會將新的 `Button` widget 拖曳至 **Design Surface**：

[![Design Surface 上的插入行範例](designer-basics-images/vs/05-insertion-points-sml.png)](designer-basics-images/vs/05-insertion-points.png#lightbox)

此外，您可以複製小工具：您可以使用 [複製] 和 [貼上] 複製 widget，也可以在按下 <kbd>CTRL</kbd> 鍵時拖放現有的小工具。

### <a name="designer-toolbar"></a>設計工具工具列

**設計工具工具列** (位於**Design Surface**) 提供設定選取器和工具功能表：

[![設計工具工具列的圖表](designer-basics-images/vs/04-toolbar-sml.png)](designer-basics-images/vs/04-toolbar.png#lightbox)

**設計工具工具列**會提供下列功能的存取權：

- **替代版面配置選取器** &ndash; 可讓您從不同的版面配置版本中進行選取。

- **裝置選取器** &ndash; 定義一組限定詞 (，例如與特定裝置相關聯的螢幕大小、解析度和鍵盤可用性) 。 您也可以新增和刪除新的裝置。

- **Android 版本選取器** &ndash; 版面配置的目標 Android 版本。 設計工具會根據選取的 Android 版本轉譯版面配置。

- **主題選取器** &ndash; 選取版面配置的 UI 主題。

- 設定**選取器** &ndash;選取裝置設定，例如 [*縱向*] 或 [*橫向*]。

- **資源辨識符號選項** &ndash; 開啟對話方塊，以顯示用於選取 *語言*、 *UI 模式*、 *夜間模式*和 *圓形螢幕* 選項的下拉式功能表。

- **動作列設定** &ndash; 設定配置的動作列設定。

- **主題編輯器** &ndash; 開啟 *主題編輯器*，讓您可以自訂所選主題的元素。

- **材質設計方格** &ndash; 啟用或停用 *材質設計方格*。 [材質設計] 方格旁的下拉式功能表專案會開啟一個對話方塊，讓您自訂方格。

這些主題會更詳細地說明這些功能：

- [資源限定詞和視覺效果選項](~/android/user-interface/android-designer/resource-qualifiers.md) 提供有關 **裝置選取器**、   **Android 版本選取器**、 **主題選取**器、設定 **選取器**、 **資源限定選項**和 **動作列設定**的詳細資訊。

- [替代版面配置視圖](~/android/user-interface/android-designer/alternative-layout-views.md) 會說明如何使用 **替代的版面配置選取器**。

- [Xamarin. Android Designer 的材質設計功能](~/android/user-interface/android-designer/material-design-features.md) 提供 **主題編輯器** 和   **材質設計方格**的完整總覽。

### <a name="context-menu-commands"></a>內容功能表命令

您可以在 **Design Surface** 和 **檔大綱**中使用內容功能表。 此功能表會顯示所選小工具及其容器可用的命令，讓您更輕鬆地在容器上執行作業， (在 **Design Surface**) 上不一定很容易選取。 以下是內容功能表的範例：

[![在 Design Surface 上按一下滑鼠右鍵時的範例內容功能表](designer-basics-images/vs/06-context-menu-sml.png)](designer-basics-images/vs/06-context-menu.png#lightbox)

在此範例中，以滑鼠右鍵按一下以 `TextView` 開啟可提供數個選項的內容功能表：

- **LinearLayout** &ndash; 開啟用來編輯父系的子功能表 `LinearLayout` `TextView` 。

- **刪除**、 **複製**和 **剪**下套用 &ndash; 至以滑鼠右鍵按一下的作業 `TextView` 。

### <a name="zoom-controls"></a>縮放控制項

**Design Surface**支援透過數個控制項進行縮放，如下所示：

[![Design Surface 縮放控制項的圖表](designer-basics-images/vs/07-zoom-controls-sml.png)](designer-basics-images/vs/07-zoom-controls.png#lightbox)

這些控制項可讓您更輕鬆地查看設計工具中的使用者介面某些區域：

- **醒目提示容器** &ndash; 強調   **Design Surface** 上的容器，以便在放大和縮小時更容易找到。

- **一般大小** &ndash; 轉譯圖元的版面配置，讓您可以查看版面配置將如何查看所選裝置的解析度。

- **符合視窗大小** &ndash; 設定縮放層級，讓 Design Surface 可以看到整個版面配置。

- **放大** &ndash; 在每次點擊時以累加方式放大，並放大版面配置。

- **縮小** &ndash; 在每次點擊時以累加方式縮小，讓版面配置在 Design Surface 上顯示較小。

請注意，選擇的縮放設定不會影響應用程式在執行時間的使用者介面。

## <a name="switching-between-design-and-source-panes"></a>在設計和來源窗格之間切換

在 [ **設計** ] 和 [ **來源** ] 窗格之間的中間區域中，有數個按鈕可用來修改 [ **設計** ] 和 [ **來源** ] 窗格的顯示方式：

[![窗格顯示按鈕位置](designer-basics-images/vs/25-pane-buttons-sml.png)](designer-basics-images/vs/25-pane-buttons.png#lightbox)

這些按鈕會執行下列動作：

- **設計** &ndash; [ **設計**] 這個最頂端的按鈕，會選取 [ **設計** ] 窗格。 按一下這個按鈕時，[ **工具箱** ] 和 [ **屬性** ] 窗格會啟用，且不會顯示 [ **文字編輯器] 工具列** 。 按一下 [折迭 **] 按鈕時** (看到以下) ，則不會顯示 [ **設計** ] 窗格，而不會顯示 [ **來源** ] 窗格。

- **交換窗格** &ndash; 這個按鈕 (類似兩個反向箭號) 交換 [ **設計** ] 和 [ **來源** ] 窗格，讓 [ **來源** ] 窗格位於左邊，而 [ **設計** ] 窗格位於右邊。 再按一下它，就會將這些窗格重新交換回到其原始位置。

- **來源** &ndash; 此按鈕 (類似兩個反向角括弧) 選取 [ **來源** ] 窗格。 按一下這個按鈕時，[ **工具箱** ] 和 [ **屬性** ] 窗格會停用，而 [ **文字編輯器] 工具列** 會顯示在 Visual Studio 的上方。 按一下 [折迭 **] 按鈕時** (請參閱下方) ，按一下 [ **來源** ] 按鈕會顯示 [ **來源** ] 窗格，而不是 [ **設計** ] 窗格。

- **垂直分割** &ndash; 此按鈕 (類似于分隔號形圖) ，可並排顯示 [ **設計** ] 和 [ **來源** ] 窗格。 這是預設的相片順序。

- **水準分割** &ndash;此按鈕 (類似于水準橫條圖) ，會在 [**來源**] 窗格上方顯示 [**設計**] 窗格。 您可以按一下 [**交換**] 窗格，將 [**來源**] 窗格放在**設計**窗格的上方。

- 折迭**窗格** &ndash;此按鈕 (類似兩個右角角括弧) 「折迭」**設計**和**來源**的雙重窗格顯示，成為其中一個窗格的單一視圖。
    這個按鈕會變成 **展開窗格** 按鈕 (類似兩個左角角括弧) ，您可以按一下這個按鈕，將視圖回到雙窗格 (**設計** 和 **來源**) 顯示模式。

按一下 [折迭 **] 窗格** 時，只會顯示 [ **設計** ] 窗格。 不過，您可以按一下 [ **來源** ] 按鈕，改為只查看 [ **來源** ] 窗格。 再次按一下 [ **設計** ] 按鈕，以返回 [ **設計** ] 窗格。

## <a name="source-pane"></a>來源窗格

[ **來源** ] 窗格會顯示 **Design Surface**上所顯示的設計基礎的 XML 來源。 因為這兩個視圖都可以同時使用，所以您可以在設計的視覺標記法和設計的基礎 XML 來源之間來回移動，藉以建立 UI 設計：

[![來源窗格中的 XML 來源範例](designer-basics-images/vs/22-source-pane-w158-sml.png)](designer-basics-images/vs/22-source-pane-w158.png#lightbox)

對 XML 來源所做的變更會立即在 **Design Surface**上轉譯;對 **Design Surface** 所做的變更會使 [ **來源** ] 窗格中顯示的 XML 來源隨之更新。 當您對 [ **來源** ] 窗格中的 xml 進行變更時，可以使用自動完成和 IntelliSense 功能來加速以 XML 為基礎的 UI 開發，如接下來所述。

若要在使用長 XML 檔案時方便導覽，[ **來源** ] 窗格支援 Visual Studio 捲軸 (如先前的螢幕擷取畫面中的右邊所示) 。 如需捲軸的詳細資訊，請參閱 [如何藉由自訂捲軸來追蹤程式碼](/visualstudio/ide/how-to-track-your-code-by-customizing-the-scrollbar)。

### <a name="autocompletion"></a>自動完成

當您開始輸入 widget 的屬性名稱時，您可以按 <kbd>CTRL + 空格鍵</kbd> 來查看可能的完成清單。 例如，輸入 `android:lay` 下列範例之後 (接著輸入 <kbd>CTRL + 空格鍵</kbd>) ，會顯示下列清單：

[![自動完成版面配置屬性](designer-basics-images/vs/23-autocompletion-w158-sml.png)](designer-basics-images/vs/23-autocompletion-w158.png#lightbox)

按 <kbd>enter</kbd> 以接受第一個列出的完成，或使用方向鍵來滾動至所要的完成，然後按 <kbd>enter</kbd>鍵。 或者，您也可以使用滑鼠滾動至所要的完成，然後按一下。

### <a name="intellisense"></a>IntelliSense

當您輸入 widget 的新屬性並開始為它指派值之後，IntelliSense 會在輸入觸發程式字元之後隨即出現，並提供要用於該屬性的有效值清單。 例如，在下列範例中輸入的第一個雙引號之後 `android:layout_width` ，自動完成選取器會彈出，以提供此寬度的有效選項清單：

[![版面配置寬度的 IntelliSense 範例](designer-basics-images/vs/24-intellisense-w158-sml.png)](designer-basics-images/vs/24-intellisense-w158.png#lightbox)

此快顯視窗的底部有兩個按鈕 (，如上方螢幕擷取畫面所示的紅色) 。 按一下左邊的 [ **專案資源** ] 按鈕，就會將清單限制為應用程式專案的一部分，而按一下右側的 [ **Framework 資源** ] 按鈕會限制清單，以顯示架構中可用的資源。
這些按鈕會開啟或關閉：您可以再次按一下它們，以停用每個按鈕所提供的篩選動作。

## <a name="properties-pane"></a>屬性窗格

設計工具支援透過 [ **屬性** ] 窗格編輯 widget 屬性： 

![屬性視窗的螢幕擷取畫面](designer-basics-images/vs/08-property-pad.png)

[ **屬性** ] 窗格中所列的屬性會根據在 **Design Surface**上選取的 widget 而變更。

### <a name="default-values"></a>預設值

大部分 widget 的屬性在 [ **屬性** ] 視窗中都是空白的，因為它們的值繼承自選取的 Android 主題。
[ **屬性** ] 視窗只會顯示針對所選取 widget 明確設定的值;它不會顯示繼承自主題的值。

### <a name="referencing-resources"></a>參考資源

某些屬性可以參考 **.axml** 檔案以外的檔案中所定義的資源。 這種類型最常見的案例是 `string` 和 `drawable` 資源。 不過，參考也可以用於其他資源，例如 `Boolean` 值和維度。 當屬性支援資源參考時，會在屬性的文字輸入旁邊顯示 (正方形) 的流覽圖示。 按一下時，此按鈕會開啟資源選取器。

例如，下列螢幕擷取畫面顯示當您在 `Text` [ **屬性** ] 視窗中，按一下小工具文字欄位右邊的暗角正方形時，可用的選項：

[![文字選項的範例清單](designer-basics-images/vs/09-text-options-sml.png)](designer-basics-images/vs/09-text-options.png#lightbox)

按一下 [ **資源** ] 時，會顯示 [ **選取資源** ] 對話方塊：

[![列出數個資源的範例資源螢幕擷取畫面](designer-basics-images/vs/09b-resources-w158-sml.png)](designer-basics-images/vs/09b-resources-w158.png#lightbox)

從這份清單中，您可以選取要用於該 widget 的文字資源，而不是對 [ **屬性** ] 窗格中的文字進行硬式編碼。 下一個範例說明的屬性的資源選取器，如下所示 `Src` `ImageView` ：

[![資源選取器列出 >imageview 的圖示資源](designer-basics-images/vs/10-src-resource-sml.png)](designer-basics-images/vs/10-src-resource.png#lightbox)

按一下屬性右邊的空白方塊 `Src` ，就會開啟 [ **選取資源** ] 對話方塊，其中包含從色彩 (的資源清單，如上所示) 可繪製資源。

### <a name="boolean-property-references"></a>布林值屬性參考

*布林值* 屬性通常會選取為屬性視窗中屬性旁的核取記號。 您可以藉由核取或取消核取 `true` `false` 此核取方塊來指定或值，也可以按一下屬性右邊的深色填滿方形來選取屬性參考。 在下列範例中，藉由按一下與所選取之相關聯的 [ **全部大寫** 布林值] 屬性參考，文字會變更為 [全部大寫] `TextView` ：

![設定布林值屬性的範例](designer-basics-images/vs/11-boolean.png)

## <a name="editing-properties-inline"></a>以內嵌方式編輯屬性

Android Designer 支援直接編輯 **Design Surface** (上的某些屬性，所以您不需要在屬性清單) 中搜尋這些屬性。 可以直接編輯的屬性包括文字、邊界和大小。

### <a name="text"></a>Text

某些 widget 的 text 屬性 (例如 `Button` 和 `TextView`) ，可以直接在 **Design Surface**上編輯。 按兩下 widget 會使其進入編輯模式，如下所示：

![Hello 字串的文字資源](designer-basics-images/vs/12-text-resource.png "文字資源")

您可以輸入新的文字值，也可以輸入新的資源字串。 在下列範例中， `@string/hello` 資源會以文字取代 `CLICK THIS BUTTON` ：

![Shift + Enter 可將文字自動連結到新的資源](designer-basics-images/vs/13-shift-enter-resource.png)

這項變更會儲存在 widget 的屬性中，而不 `text` 會修改指派給資源的值 `@string/hello` 。
當您在新的文字字串中輸入索引鍵時，您可以按<kbd>Shift</kbd>  +
 <kbd>enter</kbd> ，將輸入的文字自動連結到新的資源。

### <a name="margin"></a>Margin

當您選取 widget 時，設計工具會顯示可讓您以互動方式變更小工具大小或邊界的控制碼。 選取小工具時，會在邊界編輯模式與大小編輯模式之間切換。

當您第一次按一下小工具時，會顯示邊界控點。 如果您將滑鼠移至其中一個控點，設計工具會顯示該控制碼將變更的屬性 (如下所示的 `layout_marginLeft` 屬性) ：

![顯示設計工具中邊界控點的螢幕擷取畫面](designer-basics-images/vs/15-margin-handles.png)

如果已設定邊界，則會顯示虛線，指出邊界所佔用的空間：

![標示按鈕周圍空間的虛線範例](designer-basics-images/vs/16-margins-set.png)

### <a name="size"></a>大小

如先前所述，您可以在已選取小工具的情況下，按一下 widget 切換至大小編輯模式。 按一下三角形控點，將指定之維度的大小設定為 `wrap_content` ：

![包裝內容和調整大小控點](designer-basics-images/vs/17-wrap-content.png)

按一下 [ **換行內容** ] 控點會壓縮該維度中的小工具，使其不會超過所需的內容以包裝包含的內容。 在此範例中，按鈕文字會以水準方式收縮，如接下來的螢幕擷取畫面所示。

當 [大小] 值設定為 [ **換行內容**] 時，設計工具會顯示指向相反方向的三角形控點，將大小變更為 `match_parent` ：

![符合父控制碼](designer-basics-images/vs/18-match-parent.png)

按一下 [ **Match] 父** 控點會還原該維度中的大小，使其與父 widget 相同。

此外，您可以拖曳圓形調整大小控點 (如上面的螢幕擷取畫面所示) 將小工具大小調整為任意 `dp` 值。 當您這樣做時，會針對該維度顯示 **Wrap 內容** 和 **相符的父** 控制碼：

![圓形調整大小控點](designer-basics-images/vs/19-resize-dp.png)

並非所有的容器都允許編輯 `Size` widget 的。 例如，請注意，在下列螢幕擷取畫面中，如果 `LinearLayout` 選取了，就不會顯示調整大小控點：

![沒有調整大小控點](designer-basics-images/vs/20-no-resize-handles.png)

## <a name="document-outline"></a>文件大綱

[ **檔大綱** ] 會顯示版面配置的 widget 階層。
在下列範例中， `LinearLayout` 會選取包含的小工具：

![檔大綱範例](designer-basics-images/vs/21-document-outline.png)

所選 widget 的大綱 (在此案例中， `LinearLayout` **Design Surface**上也會反白顯示) 。 檔大綱中選取的 widget 會與 **Design Surface**上的對應元件保持同步。 這適用于選取 [視圖群組]，這在 **Design Surface**上不一定很容易選取。

**檔大綱**支援複製和貼上，您也可以使用拖放。 從**檔大綱**支援拖放至**Design Surface**以及**檔大綱**的**Design Surface** 。 此外，以滑鼠右鍵按一下 [ **檔大綱** ] 中的專案，就會顯示該專案的內容功能表， (在 **Design Surface**) 上以滑鼠右鍵按一下相同 widget 時所顯示的相同內容功能表。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="launching-the-designer"></a>啟動設計工具

設計工具會在版面配置建立時自動啟動，或是按兩下現有的 .axml 檔案來啟動。 例如，按兩下**資源 >** 配置資料夾中的 [ **.axml** ] 會載入設計工具，如下所示：

[![Visual Studio for Mac 中的設計工具畫面](designer-basics-images/xs/01-open-designer-sml.png)](designer-basics-images/xs/01-open-designer.png#lightbox)

同樣地，您也可以在**Solution Pad**中的**版面**配置資料夾上按一下滑鼠右鍵，然後選取 [**新增 > 新檔案 > Android > 版面**配置]，以加入新的版面配置：

[![[新增檔案] 對話方塊](designer-basics-images/xs/02-add-new-layout-sml.png)](designer-basics-images/xs/02-add-new-layout.png#lightbox)

這會建立新的 .axml 檔案，並將它載入 Design Surface。

> [!TIP]
> 較新版的 Visual Studio 支援在 Android Designer 中開啟 .xml 檔案。
>
> Android Designer 同時支援 .axml 和 .xml 檔案。

## <a name="designer-features"></a>設計工具功能

設計工具是由數個支援其各種功能的區段所組成，如下列螢幕擷取畫面所示：

[![設計工具窗格的圖表](designer-basics-images/xs/03-designer-features-sml.png)](designer-basics-images/xs/03-designer-features.png#lightbox)

當您在設計工具中編輯版面配置時，您會使用下列功能來建立和塑造設計：

- **Design Surface** &ndash; 藉由提供您可編輯的外觀，讓您知道配置在裝置上的顯示方式，以促進使用者介面的視覺結構設計。

- **工具列** &ndash; 顯示選取器清單： [ **裝置**]、[ **版本**]、[ **主題**]、[版面配置設定] 和 [動作列] 設定。 工具列也包含用來啟動主題編輯器，以及啟用材質設計方格的圖示。

- **工具箱** &ndash; 提供小工具和版面配置清單，您可以將其拖放到 Design Surface 上。

- **屬性面板** &ndash; 列出所選取 widget 的屬性以進行流覽和編輯。

- **檔大綱** &ndash; 顯示組成版面配置的 widget 樹狀目錄。 您可以按一下樹狀目錄中的專案，使其在設計工具中被選取。 此外，按一下樹狀結構中的專案，就會將專案的屬性載入屬性面板中。

## <a name="toolbar"></a>工具列

工具列 (位於 Design Surface) 提供設定選取器和工具功能表：

[![設計工具工具列的圖表](designer-basics-images/xs/04-toolbar-sml.png)](designer-basics-images/xs/04-toolbar.png#lightbox)

工具列會提供下列功能的存取權：

- **替代版面配置選取器** &ndash; 可讓您從不同的版面配置版本中進行選取。

- **裝置選取器** &ndash; 定義一組與特定裝置相關聯的限定詞，例如螢幕大小、解析度和鍵盤可用性。 您也可以新增和刪除新的裝置。

- **Android 版本選取器** &ndash; 版面配置的目標 Android 版本。 設計工具會根據選取的 Android 版本轉譯版面配置。

- **主題選取器** &ndash; 選取版面配置的 UI 主題。

- 設定**選取器** &ndash;選取裝置設定，例如 [*縱向*] 或 [*橫向*]。

- **資源辨識符號選項** &ndash; 開啟對話方塊，以顯示用於選取 *語言*、 *UI 模式*、 *夜間模式*和 *圓形螢幕* 選項的下拉式功能表。

- **動作列設定** &ndash; 設定配置的動作列設定。

- **主題編輯器** &ndash; 開啟 *主題編輯器*，讓您可以自訂所選主題的元素。

- **材質設計方格** &ndash; 啟用或停用 *材質設計方格*。 [材質設計] 方格旁的下拉式功能表專案會開啟一個對話方塊，讓您自訂方格。

這些主題會更詳細地說明這些功能：

[資源限定詞和視覺效果選項](~/android/user-interface/android-designer/resource-qualifiers.md) 提供有關 **裝置選取器**、 **Android 版本選取器**、 **主題選取**器、設定 **選取器**、 **資源限定選項**和 **動作列設定**的詳細資訊。

[替代版面配置視圖](~/android/user-interface/android-designer/alternative-layout-views.md) 會說明如何使用 **替代的版面配置選取器**。

[材質設計功能](~/android/user-interface/android-designer/material-design-features.md) 提供 **主題編輯器** 和 **材質設計方格**的完整總覽。

## <a name="design-surface"></a>設計介面

設計工具可讓您將 widget 從 [工具箱] 拖放到 Design Surface 上。 當您在設計工具中與 widget 互動 (藉由加入新的 widget 或重新置放現有的小工具) ，會顯示垂直和水平線條來標示可用的插入點。 在下列範例中，會將新的 `Button` widget 拖曳至 Design Surface：

[![Design Surface 上的插入行範例](designer-basics-images/xs/05-insertion-points-sml.png)](designer-basics-images/xs/05-insertion-points.png#lightbox)

此外，您可以複製小工具：您可以使用 [複製] 和 [貼上] 複製 widget，也可以在按下 <kbd>Ctrl</kbd> 鍵時拖放現有的小工具。

### <a name="context-menu-commands"></a>內容功能表命令

您可以在 Design Surface 和檔大綱中使用內容功能表。 此功能表會顯示所選小工具及其容器可用的命令，讓您更輕鬆地在容器上執行作業， (在 Design Surface) 上不一定很容易選取。 以下是內容功能表的範例：

[![在 Design Surface 上按一下滑鼠右鍵時的範例內容功能表](designer-basics-images/xs/06-context-menu-sml.png)](designer-basics-images/xs/06-context-menu.png#lightbox)

在此範例中，以滑鼠右鍵按一下以 `Button` 開啟可提供數個選項的內容功能表：

- **LinearLayout** &ndash; 開啟用來編輯父系的子功能表 `LinearLayout` `Button` 。

- **剪**下、 **複製**和 **刪除**套用 &ndash; 至以滑鼠右鍵按一下的作業 `Button` 。

### <a name="zoom-controls"></a>縮放控制項

Design Surface 支援透過數個控制項進行縮放，如下所示：

[![Design Surface 縮放控制項的圖表](designer-basics-images/xs/07-zoom-controls-sml.png)](designer-basics-images/xs/07-zoom-controls.png#lightbox)

這些控制項可讓您更輕鬆地查看設計工具中的使用者介面某些區域：

- **醒目提示容器** &ndash; 強調 Design Surface 上的容器，以便在放大和縮小時更容易找到。

- **一般大小** &ndash; 轉譯圖元的版面配置，讓您可以查看版面配置將如何查看所選裝置的解析度。

- **符合視窗大小** &ndash; 設定縮放層級，讓 Design Surface 可以看到整個版面配置。

- **放大** &ndash; 在每次點擊時以累加方式放大，並放大版面配置。

- **縮小** &ndash; 在每次點擊時以累加方式縮小，讓版面配置在 Design Surface 上顯示較小。

請注意，選擇的縮放設定不會影響應用程式在執行時間的使用者介面。

## <a name="property-pad"></a>屬性面板

設計工具支援透過 **屬性板**編輯 widget 屬性。 屬性面板中列出的屬性會根據在設計工具介面中選取的 widget 而變更。 `Button`選取上一個範例中的時， `Button` 會顯示該 widget 的屬性：

[![屬性板的螢幕擷取畫面](designer-basics-images/xs/08-property-pad-sml.png)](designer-basics-images/xs/08-property-pad.png#lightbox)

## <a name="property-pad-sections"></a>屬性面板區段

屬性板會分成數個區段，以將類似的屬性群組在一起， &ndash; 讓您更容易找出感興趣的屬性：

- **Widget** &ndash; Widget 的主要屬性，例如 `id` 、、等等   `visibility` `text` 。管理小工具內容的屬性通常會放在這裡。

- **樣式** &ndash; 變更 widget 視覺外觀的屬性，例如 `font` 、 `text color` 、等等 `background` 。

- **版面** &ndash; 配置設定小工具位置和大小的屬性。

- **滾動** &ndash; 條滾動屬性。

- **行為** &ndash; 設定 widget 運作方式的旗標。

### <a name="default-values"></a>預設值

大部分 widget 的屬性在 **屬性面板** 中都是空白的，因為它們的值繼承自選取的 Android 主題。 **屬性填充**只會顯示針對所選取 widget 明確設定的值;它不會顯示繼承自主題的值。

### <a name="referencing-resources"></a>參考資源

某些屬性可以參考 **.axml** 檔案以外的檔案中所定義的資源。 這種類型最常見的案例是 `string` 和 `drawable` 資源。 不過，參考也可以用於其他資源，例如 `Boolean` 值和維度。
當屬性支援資源參考時，[流覽] 圖示 (省略號， &hellip;) 顯示于屬性的文字專案旁。
按一下時，此按鈕會開啟資源選取器。

例如，下列螢幕擷取畫面顯示當您在 `Button` **屬性面板**中，按一下 widget 文字欄位右邊的省略號時，可用的資源：

[![列出兩個資源的範例資源螢幕擷取畫面](designer-basics-images/xs/09-resources-sml.png)](designer-basics-images/xs/09-resources.png#lightbox)

下一個範例說明的屬性的資源選取器，如下所示 `Src` `ImageView` ：

[![資源選取器列出 >imageview 的圖示資源](designer-basics-images/xs/10-src-resource-sml.png)](designer-basics-images/xs/10-src-resource.png#lightbox)

### <a name="boolean-property-references"></a>布林值屬性參考

*布林值* 屬性通常會顯示為屬性面板中的核取方塊。 當 `Boolean` 屬性支援資源參考時，屬性旁邊會出現一個小的核取方塊。 核取的核取方塊表示 `true` ，空的方塊表示 `false` 。 您也可以直接輸入值，例如 `true` 或 `false` 。 將滑鼠停留在輸入上方會顯示一個小型文字欄位圖示。 如果您想要手動輸入值，可以按一下它。

[![設定布林值屬性的範例](designer-basics-images/xs/12-boolean-sml.png)](designer-basics-images/xs/12-boolean.png#lightbox)

## <a name="grouped-properties"></a>群組屬性

有些小工具具有多值屬性，這些屬性會群組在一起 (例如 `Padding`) 。 這些屬性值會在 **屬性面板** 的單一可展開資料列中列出。 其中有些屬性可以直接在群組資料列中編輯，例如如下所 `Padding` 示的屬性：

[![填補屬性的範例設定](designer-basics-images/xs/13-padding-property-sml.png)](designer-basics-images/xs/13-padding-property.png#lightbox)

## <a name="editing-properties-inline"></a>以內嵌方式編輯屬性

Android Designer 支援直接編輯 Design Surface (上的某些屬性，所以您不需要在屬性清單) 中搜尋這些屬性。 可以直接編輯的屬性包括文字、邊界和大小。

### <a name="text"></a>Text

某些 widget 的 text 屬性 (例如 `Button` 和 `TextView`) ，可以直接在 Design Surface 上編輯。 按兩下 widget 會使其進入編輯模式，如下所示：

[![Hello 字串的文字資源](designer-basics-images/xs/14-text-resource-sml.png)](designer-basics-images/xs/14-text-resource.png#lightbox)

您可以輸入新的文字值，也可以輸入新的資源字串。 在下列範例中， `@string/hello` 資源會以文字取代 `CLICK THIS BUTTON` ：

[![Shift + Enter 可將文字自動連結到新的資源](designer-basics-images/xs/15-shift-enter-resource-sml.png)](designer-basics-images/xs/15-shift-enter-resource.png#lightbox)

這項變更會儲存在 widget 的屬性中，而不 `text` 會修改指派給資源的值 `@string/hello` 。
當您在新的文字字串中輸入索引鍵時，您可以按<kbd>Shift</kbd>  +
 <kbd>enter</kbd> ，將輸入的文字自動連結到新的資源。

### <a name="margin"></a>Margin

當您選取 widget 時，設計工具會顯示可讓您以互動方式變更小工具大小或邊界的控制碼。 選取小工具時，會在邊界編輯模式與大小編輯模式之間切換。

當您第一次按一下小工具時，會顯示邊界控點。 如果您將滑鼠移至其中一個控點，設計工具會顯示該控制碼將變更的屬性 (如下所示的 `layout_marginLeft` 屬性) ：

[![顯示設計工具中邊界控點的螢幕擷取畫面](designer-basics-images/xs/16-margin-handles-sml.png)](designer-basics-images/xs/16-margin-handles.png#lightbox)

如果已設定邊界，則會顯示虛線，指出邊界所佔用的空間：

[![標示按鈕周圍空間的虛線範例](designer-basics-images/xs/17-margins-set-sml.png)](designer-basics-images/xs/17-margins-set.png#lightbox)

### <a name="size"></a>大小

如先前所述，您可以在已選取小工具的情況下，按一下 widget 切換至大小編輯模式。 按一下三角形控點，將指定之維度的大小設定為 `wrap_content` ：

[![包裝內容和調整大小控點](designer-basics-images/xs/18-wrap-content-sml.png)](designer-basics-images/xs/18-wrap-content.png#lightbox)

按一下 [ **換行內容** ] 控點會壓縮該維度中的小工具，如此就不會超過所需的內容來包裝包含的內容。 在此範例中，按鈕文字會以水準方式收縮，如接下來的螢幕擷取畫面所示。

當 [大小] 值設定為 [ **換行內容**] 時，設計工具會顯示指向相反方向的三角形控點，將大小變更為 `match_parent` ：

[![符合父控制碼](designer-basics-images/xs/19-match-parent-sml.png)](designer-basics-images/xs/19-match-parent.png#lightbox)

按一下 [ **Match] 父** 控點會還原該維度中的大小，使其與父 widget 相同。

此外，您可以拖曳圓形調整大小控點 (如上面的螢幕擷取畫面所示) 將小工具大小調整為任意 `dp` 值。 當您這樣做時，會針對該維度顯示 **Wrap 內容** 和 **相符的父** 控制碼：

[![圓形調整大小控點](designer-basics-images/xs/20-resize-dp-sml.png)](designer-basics-images/xs/20-resize-dp.png#lightbox)

並非所有的容器都允許編輯 `Size` widget 的。 例如，請注意，在下列螢幕擷取畫面中，如果 `LinearLayout` 選取了，就不會顯示調整大小控點：

[![沒有調整大小控點](designer-basics-images/xs/21-no-resize-handles-sml.png)](designer-basics-images/xs/20-no-resize-handles.png#lightbox)

## <a name="document-outline"></a>文件大綱

[ **檔大綱** ] 會顯示版面配置的 widget 階層。
在下列範例中， `LinearLayout` 會選取包含的小工具：

[![檔大綱](designer-basics-images/xs/22-outline-view-sml.png)](designer-basics-images/xs/22-outline-view.png#lightbox)

所選 widget 的大綱 (在此案例中， `LinearLayout` Design Surface 上也會反白顯示) 。 檔大綱中選取的 widget 會與 Design Surface 上的對應元件保持同步。 這適用于選取 [視圖群組]，這在 Design Surface 上不一定很容易選取。

檔大綱支援複製和貼上，您也可以使用拖放。 從檔大綱支援拖放至 Design Surface 以及檔大綱的 Design Surface。 此外，以滑鼠右鍵按一下 [檔大綱] 中的專案，就會顯示該專案的內容功能表， (在 Design Surface) 上以滑鼠右鍵按一下相同 widget 時所顯示的相同內容功能表。

-----