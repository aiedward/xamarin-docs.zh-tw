---
title: Xamarin.Mac 中的映像
description: 這篇文章涵蓋使用 「 映像和在 Xamarin.Mac 應用程式中的圖示。 它說明建立和維護的映像建立您的應用程式圖示所需和使用 C# 程式碼和 Xcode 的 Interface Builder 中的映像。
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 719efc87b8843d0d2fcd2643aab23aa6849d940a
ms.sourcegitcommit: 190808013249005ceffbc798f9f4570e8cdc943a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2019
ms.locfileid: "54841376"
---
# <a name="images-in-xamarinmac"></a>Xamarin.Mac 中的映像

_這篇文章涵蓋使用 「 映像和在 Xamarin.Mac 應用程式中的圖示。它說明建立和維護的映像建立您的應用程式圖示所需和使用 C# 程式碼和 Xcode 的 Interface Builder 中的映像。_

## <a name="overview"></a>總覽

當在 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取的相同影像與圖示工具工作的開發人員*OBJECTIVE-C*並*Xcode*沒有。

有數種方式內 macOS （之前稱為 Mac OS X） 應用程式使用資產，則該映像。 只顯示一部分的應用程式的 UI，映像提供圖示，將它指派至 UI 控制項，例如工具列或來源清單項目，Xamarin.Mac 輕鬆地加入您的 macOS 應用程式中的絕佳作品，透過下列方式: 

- **UI 項目**-為背景或在映像檢視中的應用程式的一部分，就可以顯示映像 (`NSImageView`)。
- **按鈕**-可以在按鈕中顯示映像 (`NSButton`)。
- **影像儲存格中**-為基礎的資料表控制項的一部分 (`NSTableView`或`NSOutlineView`)，可以使用映像，在映像的儲存格 (`NSImageCell`)。
- **工具列項目**-映像可以新增至工具列 (`NSToolbar`) 做為影像的工具列項目 (`NSToolbarItem`)。
- **來源清單圖示**-[來源] 清單的一部分 (特殊格式化`NSOutlineView`)。
- **應用程式圖示**-以連續影像可以群組成`.icns`集以及當做您的應用程式圖示。 請參閱我們[應用程式圖示](~/mac/deploy-test/app-icon.md)文件的詳細資訊。

此外，macOS 提供一組預先定義可用於整個應用程式的映像。

[![範例執行的應用程式](image-images/intro01.png "範例執行的應用程式")](image-images/intro01-large.png#lightbox)

在本文中，我們將涵蓋在 Xamarin.Mac 應用程式中使用影像和圖示的基本概念。 強烈建議您逐步[Hello，Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和 Interface Builder 簡介](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)並[輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions)各節中的，因為它涵蓋重要概念和技術，我們將在本文中使用。


## <a name="adding-images-to-a-xamarinmac-project"></a>Xamarin.Mac 專案中加入影像

新增時使用的映像在 Xamarin.Mac 應用程式，有幾個地方和開發人員可以包含到專案的來源映像檔的方式：

- **[取代] 的主要專案樹狀結構**-影像直接加入專案樹狀結構。 當呼叫程式碼從主要專案樹狀結構中儲存的映像，不指定任何資料夾位置。 例如：`NSImage image = NSImage.ImageNamed("tags.png");`。 
- **[取代] 的 [資源] 資料夾**-特殊**資源**資料夾是映像 （或任何其他映像或開發人員的檔案，例如圖示、 啟動螢幕或一般的組合將成為應用程式一部分的任何檔案新增希望）。 呼叫儲存在映像時**資源**從程式碼的資料夾，就像映像儲存在主專案樹狀結構，不指定任何資料夾位置。 例如：`NSImage.ImageNamed("tags.png")`。
- **自訂的資料夾或 [取代] 的子資料夾**-開發人員可以將自訂資料夾加入至專案的來源樹狀結構，並儲存映像那里。 為了進一步組織專案的子資料夾中，可以是巢狀的位置加入檔案的位置。 比方說，如果開發人員加入`Card`的專案和子資料夾的資料夾`Hearts`到該資料夾，然後儲存映像**Jack.png**中`Hearts`資料夾，`NSImage.ImageNamed("Card/Hearts/Jack.png")`會載入在映像執行階段。
- **資產目錄影像集合，而 [慣用]** -在 「 OS X El Capitan 」 中，加入**資產目錄映像集**包含的所有版本或映像的支援各種裝置和縮放因數所需的表示法您應用程式。 而不是依賴影像資產檔案名稱 (**@1x**， **@2x**)。

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>將映像新增至資產目錄映像集

如上面所述**資產目錄映像集**包含的所有版本或映像的支援各種裝置和縮放因數，為您的應用程式所需的表示法。 而不是依賴影像資產檔案名稱 （請參閱解析度獨立影像和上述的映像命名法），**映像集**使用資產編輯器來指定哪些裝置和/或解析屬於哪一個映像。

1. 在  **Solution Pad**，按兩下**appicon**檔案，以開啟它進行編輯： 

    ![選取 Assets.xcassets](image-images/imageset01.png "選取 Assets.xcassets")
2. 以滑鼠右鍵按一下**資產清單**，然後選取**集中新的映像**: 

    [![加入新的映像集](image-images/imageset02.png "加入新的映像集")](image-images/imageset02-large.png#lightbox)
3. 選取新的映像集，並將顯示編輯器： 

    [![選取新的映像組](image-images/imageset03.png "選取新的影像集")](image-images/imageset03-large.png#lightbox)
4. 從這裡我們可以針對每個不同的裝置和所需的解決方式拖曳影像中。 
5. 按兩下新的映像集合**名稱**中**資產清單**對其進行編輯： 

    [![編輯影像集名稱](image-images/imageset04.png "編輯映像設定名稱")](image-images/imageset04-large.png#lightbox)
    
特殊**向量**類別也已經加入至**映像集**，可讓我們納入_PDF_格式改為包括在個別的點陣圖檔案 casset 向量映像不同的解析度。 使用此方法，您提供的單一向量檔案 **@1x** 解析 （格式為向量的 PDF 檔案） 和 **@2x** 並 **@3x** 會在編譯時期產生和應用程式的組合中所包含的檔案版本。

[![映像設定編輯器介面](image-images/imageset05.png "映像設定編輯器的介面")](image-images/imageset05-large.png#lightbox)

例如，如果您包含`MonkeyIcon.pdf`150px x 150px，下列點陣圖編譯時，會在最終的應用程式套件組合中包含資產的解析度的媒介的資產目錄的檔案：

1. **MonkeyIcon@1x.png** -150px x 150px 解析。
2. **MonkeyIcon@2x.png** -300px x 300px 解析。
3. **MonkeyIcon@3x.png** -450px x 450px 解析。

下列應該列入考量時使用 PDF 向量影像資產目錄中：

- 這不是完整的向量支援 PDF 將會在編譯時期和最終的應用程式中隨附的點陣圖的點陣圖的點陣化。
- 設定資產目錄中之後，您無法調整影像的大小。 如果您嘗試調整大小的映像 （無論是在程式碼，或使用自動版面配置和大小類別） 將會扭曲映像，就像任何其他的點陣圖。

使用時**集中映像**在 Xcode 的 Interface Builder 中，您可以只選取集的名稱從下拉式清單中，在**屬性偵測器**:

![在 Xcode 的 Interface Builder 中選取影像設定](image-images/imageset06.png "在 Xcode 的 Interface Builder 中選取映像設定")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>加入新的資產集合

使用資產目錄中的映像時可能會想要建立新的集合，而不是新增所有您要的映像時的時間**appicon**集合。 例如，在設計上隨選資源時。

若要將新的資產目錄加入至專案：

1. 中的專案上按一下滑鼠右鍵**Solution Pad** ，然後選取**新增** > **新檔...**
2. 選取  **Mac** > **資產目錄**，輸入**名稱**集合，然後按一下 **新增**按鈕： 

    ![加入新的資產目錄](image-images/asset01.png "新增新的資產目錄")

從這裡您可以使用集合做為預設值相同的方式**appicon**自動包含在專案中的集合。


### <a name="adding-images-to-resources"></a>將映像新增至資源

> [!IMPORTANT]
> 在 macOS 應用程式中的映像所使用的這個方法已被取代，apple。 您應該使用[資產目錄映像集](#asset-catalogs)manager 應用程式的映像改。

您可以使用的映像檔在 Xamarin.Mac 應用程式 （無論是使用 C# 程式碼，或從介面產生器） 之前必須包含在專案中的**資源**的資料夾**配套資源**。 若要將檔案加入專案中，執行下列作業：

1. 以滑鼠右鍵按一下**資源**專案中的資料夾**Solution Pad** ，然後選取**新增** > **新增檔案...**: 

    ![將檔案新增](image-images/add01.png "加入檔案")
2. 從**加入檔案**對話方塊中，選取影像檔加入專案中，選取`BundleResource`如**覆寫建置動作**，按一下 **開啟**按鈕：

    [![選取要新增的檔案](image-images/add02.png "選取要新增的檔案")](image-images/add02-large.png#lightbox)
3. 如果檔案已經不在**資源**資料夾中，您需要如果您想要**複製**，**移動**或是**連結**檔案。 挑選其中每個花色您的需求，通常可**複製**:

    ![選取 [新增] 動作](image-images/add04.png "選取 [新增] 動作")
4. 新的檔案會包含在專案中，並用於讀取： 

    ![新的影像檔新增至 Solution Pad](image-images/add03.png "新的影像檔新增至 Solution Pad")
5. 如需的任何映像檔案重複此程序。

您可以為來源映像使用任何 png、 jpg 或 pdf 檔案，在 Xamarin.Mac 應用程式中。 在下一步 區段中，我們將探討加入我們的映像的高解析度版本和圖示，以支援 Retina 基礎 Mac。

> [!IMPORTANT]
> 如果您要新增至映像**資源**資料夾中，您可以保留**覆寫建置動作**設定為**預設**。 預設值為此資料夾的建置動作是`BundleResource`。

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>提供所有應用程式圖形資源的高解析度的版本

您將新增至 Xamarin.Mac 應用程式 （圖示、 自訂控制項、 自訂游標、 自訂的圖檔等） 的任何圖形資產需要有高解析度版本，除了其標準解析度版本。 這是必要的以便您的應用程式看起來最好 Retina 顯示器上的執行配備 Mac 電腦。


### <a name="adopt-the-2x-naming-convention"></a>採用@2x命名慣例

> [!IMPORTANT]
> 在 macOS 應用程式中的映像所使用的這個方法已被取代，apple。 您應該使用[資產目錄映像集](#asset-catalogs)manager 應用程式的映像改。

當您建立的標準和高解析度版本映像時，請遵循此映像配對的命名慣例時納入 Xamarin.Mac 專案中：

- **標準解析度**  - **ImageName.filename 擴充**(範例： **tags.png**)
- **高解析度**  -  **ImageName@2x.filename-extension** (範例： **tags@2x.png**)

新增至專案時，會出現，如下所示：

![影像檔，在 Solution Pad](image-images/add03.png "Solution Pad 中的映像檔案")

映像指派給介面產生器中的 UI 項目時，您將只挑選中的檔案_ImageName_**。**_副檔名_格式 (範例： **tags.png**)。 適用於 C# 程式碼中使用的映像，您將挑選中的檔案_ImageName_**。**_副檔名_格式。

在 Mac 上，執行您的 Xamarin.Mac 應用程式時_ImageName_**。**_副檔名_格式的映像將用於標準解析度顯示的畫面**ImageName@2x.filename-extension**映像會自動挑選 Retina 顯示器基底的 Mac。


## <a name="using-images-in-interface-builder"></a>使用 Interface Builder 中映像

您已新增至任何影像資源**資源**資料夾中您 Xamarin.Mac 專案，並已將建置動作設為**BundleResource**會自動顯示在介面產生器中，而且可以是（如果它會處理映像），請選取做為一部分的 UI 項目。

若要使用映像介面產生器 中，執行下列作業：

1. 新增映像**資源**資料夾中的，使用**建置動作**的`BundleResource`: 

     ![Solution Pad 中的影像資源](image-images/ib00.png "Solution Pad 中的影像資源")
2. 按兩下**Main.storyboard**以開啟它進行編輯介面產生器中的檔案： 

     [![編輯主要分鏡腳本](image-images/ib01.png "編輯主要分鏡腳本")](image-images/ib01-large.png#lightbox)
3. 將接受拖曳至設計介面上的映像的 UI 項目 (例如**影像的工具列項目**): 

     ![編輯工具列項目的](image-images/ib02.png "編輯工具列項目")
4. 選取您加入的映像**資源**中的資料夾**映像名稱**下拉式清單中： 

     [![選取工具列項目的影像](image-images/ib03.png "選取工具列項目的影像")](image-images/ib03-large.png#lightbox)
5. 在設計介面中，將會顯示選取的映像： 

     ![工具列編輯器中所顯示的影像](image-images/ib04.png "工具列編輯器中所顯示的影像")
6. 儲存變更並返回 Visual Studio for Mac 與 Xcode 同步處理。

上述的步驟適用於任何 UI 元素，讓其映像屬性設定**屬性偵測器**。 同樣地，如果您已加入 **@2x** 映像檔的版本，它會自動使用在 Retina 顯示器基礎 Mac。

> [!IMPORTANT]
> 如果未提供的映像**映像名稱**下拉式清單中，關閉.storyboard 專案在 Xcode 中的，並重新開啟它從 Visual Studio for mac。 如果映像仍無法使用，請確認其**建置動作**是`BundleResource`且已加入映像**資源**資料夾。

## <a name="using-images-in-c-code"></a>在 C# 程式碼中使用映像

當載入 Xamarin.Mac 應用程式中使用 C# 程式碼的記憶體中的映像，映像會儲存在`NSImage`物件。 如果映像檔案已包含在 Xamarin.Mac 應用程式套件 （包含在資源），請使用下列程式碼載入影像：

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

上述程式碼會使用靜態`ImageNamed("...")`方法`NSImage`類別，以從記憶體中載入指定的映像**資源**資料夾中，如果找不到映像，`null`會傳回。 例如 Interface Builder 中指派的影像，如果您已加入 **@2x** 映像檔的版本，它會自動使用在 Retina 顯示器基礎 Mac。

若要載入外部 （來自 Mac 檔案系統） 的應用程式的套件組合的映像，請使用下列程式碼：

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>使用範本映像

根據您的 macOS 應用程式的設計，有可能是當您需要自訂圖示或影像內的使用者介面，以比對色彩配置 （例如，根據使用者喜好設定） 中的變更。

若要達成此效果，請切換_呈現模式_的映像資產**範本映像**:

[![設定範本映像](image-images/templateimage01.png "設定範本映像")](image-images/templateimage01-large.png#lightbox)

在 Xcode 的 Interface Builder，將指派到 UI 控制項的影像資產：

![在 Xcode 的 Interface Builder 中選取映像](image-images/templateimage02.png "Xcode 的 Interface Builder 中選取影像")

或者選擇性地設定程式碼中的 映像來源：

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

檢視控制器中加入下列公用函式：

```csharp
public NSImage ImageTintedWithColor(NSImage sourceImage, NSColor tintColor)
    => NSImage.ImageWithSize(sourceImage.Size, false, rect => {
        // Draw the original source image
        sourceImage.DrawInRect(rect, CGRect.Empty, NSCompositingOperation.SourceOver, 1f);

        // Apply tint
        tintColor.Set();
        NSGraphics.RectFill(rect, NSCompositingOperation.SourceAtop);

        return true;
    });
```

> [!IMPORTANT]
> 特別是使用 macOS Mojave 中深模式問世，務必避免`LockFocus`API reating 自訂轉譯時`NSImage`物件。 這類映像會變成靜態，並將不會自動更新的外觀或顯示器的密度變更的帳戶。
>
> 利用上述的處理常式機制，重新轉譯動態條件會自動發生時`NSImage`裝載，例如，在`NSImageView`。

最後，用來設定濃淡範本映像，呼叫此函式對映像，以顏色標示：

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>使用 「 資料表檢視中使用映像

若要包含影像中的資料格的一部分`NSTableView`，您必須變更 [資料表] 檢視所傳回資料的方式`NSTableViewDelegate's``GetViewForItem`方法，以使用`NSTableCellView`而不是一般`NSTextField`。 例如: 

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

有重要的幾行。 首先，我們想要包含影像的資料行，我們建立的新`NSImageView`所需的大小和位置，我們也會建立新`NSTextField`，並將它是否我們所使用的映像為基礎的預設位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我們要在父代中包含新的映像檢視和文字欄位`NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最後，我們必須告訴它可以用來壓縮，並隨著資料表檢視儲存格的文字欄位：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

範例輸出：

[![範例應用程式中顯示影像](image-images/tables01.png "範例應用程式中顯示影像")](image-images/tables01-large.png#lightbox)

如需有關使用 「 資料表檢視的詳細資訊，請參閱我們[資料表檢視](~/mac/user-interface/table-view.md)文件。

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>使用 「 大綱檢視中使用映像

若要包含影像中的資料格的一部分`NSOutlineView`，您必須變更 [大綱] 檢視所傳回資料的方式`NSTableViewDelegate's``GetView`方法，以使用`NSTableCellView`而不是一般`NSTextField`。 例如: 

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

有重要的幾行。 首先，我們想要包含影像的資料行，我們建立的新`NSImageView`所需的大小和位置，我們也會建立新`NSTextField`，並將它是否我們所使用的映像為基礎的預設位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我們要在父代中包含新的映像檢視和文字欄位`NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最後，我們必須告訴它可以用來壓縮，並隨著資料表檢視儲存格的文字欄位：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

範例輸出：

[![在大綱檢視中所顯示之影像的範例](image-images/outline01.png "在大綱檢視中所顯示之影像的範例")](image-images/outline01-large.png#lightbox)

如需有關使用 「 大綱檢視的詳細資訊，請參閱我們[大綱檢視](~/mac/user-interface/outline-view.md)文件。


## <a name="summary"></a>總結

本文所深入了解在 Xamarin.Mac 應用程式中使用的影像和圖示。 我們會看到不同的類型，並使用映像、 如何使用 Xcode 的 Interface Builder 中的影像和圖示和如何在 C# 程式碼中使用影像和圖示。



## <a name="related-links"></a>相關連結

- [MacImages (範例)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料表檢視](~/mac/user-interface/table-view.md)
- [大綱檢視](~/mac/user-interface/outline-view.md)
- [macOS X 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [關於 OS X 的高解析度](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html) \(英文\)
