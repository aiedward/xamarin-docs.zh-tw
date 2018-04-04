---
title: 影像
description: 本文涵蓋了使用映像和 Xamarin.Mac 應用程式中的圖示。 它說明建立和維護的映像，才能建立您的應用程式圖示，並使用 C# 程式碼和 Xcode 的介面產生器中的映像。
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/15/2017
ms.openlocfilehash: dc33dc78c09c0b5b7cb7533afdd2f95b8ebd9c4e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="images"></a>影像

_本文涵蓋了使用映像和 Xamarin.Mac 應用程式中的圖示。它說明建立和維護的映像，才能建立您的應用程式圖示，並使用 C# 程式碼和 Xcode 的介面產生器中的映像。_


## <a name="overview"></a>總覽

當 Xamarin.Mac 應用程式中使用 C# 和.NET，您可以存取相同的映像與圖示工具開發人員*OBJECTIVE-C*和*Xcode*沒有。

有幾種方式 macOS （之前稱為 Mac OS X） 應用程式內所使用的資產該映像。 只顯示影像做為您的應用程式 UI 的一部分提供圖示，將其指派至 UI 控制項，例如工具列或來源清單項目，從 Xamarin.Mac 輕鬆地加入 macOS 應用程式中的絕佳作品，以下列方式: 

- **UI 項目**-做為背景或在影像檢視中的應用程式可以顯示的映像 (`NSImageView`)。
- **按鈕**-映像可以顯示在按鈕 (`NSButton`)。
- **影像資料格**-設為基礎的資料表控制項的一部分 (`NSTableView`或`NSOutlineView`)，映像可用於影像資料格 (`NSImageCell`)。
- **工具列項目**-映像可以加入至工具列 (`NSToolbar`) 做為映像的工具列項目 (`NSToolbarItem`)。
- **來源清單圖示**-來源清單的一部分 (特殊格式化`NSOutlineView`)。
- **應用程式圖示**-一系列的映像可以一起分組到`.icns`集以及當做應用程式的圖示。 請參閱我們[應用程式圖示](~/mac/deploy-test/app-icon.md)文件的詳細資訊。

此外，macOS 提供一組預先定義可在整個應用程式的映像。

[![範例執行的應用程式](image-images/intro01.png "範例執行的應用程式")](image-images/intro01-large.png#lightbox)

在本文中，我們會在 Xamarin.Mac 應用程式中使用的影像和圖示的基本概念。 強烈建議您逐步[Hello、 Mac](~/mac/get-started/hello-mac.md)發行項的第一次，具體來說[Xcode 和介面產生器簡介](~/mac/get-started/hello-mac.md#Introduction_to_Xcode_and_Interface_Builder)和[插座和動作](~/mac/get-started/hello-mac.md#Outlets_and_Actions)區段中的，因為它涵蓋重要概念和技術，我們將在本文中使用。


## <a name="adding-images-to-a-xamarinmac-project"></a>Xamarin.Mac 專案中加入影像

加入時使用的映像 Xamarin.Mac 應用程式中，有數個位置和開發人員可以包含到專案的來源映像檔的方式：

- **[Deprecated] 的主要專案樹狀結構**-影像直接加入專案樹狀結構。 當呼叫映像儲存在程式碼的主要專案樹狀結構中，指定沒有資料夾位置。 例如：`NSImage image = NSImage.ImageNamed("tags.png");`。 
- **[Deprecated] 的 [資源] 資料夾**-特殊**資源**資料夾是映像 （或任何其他映像或開發人員的檔案，例如圖示、 啟動螢幕或一般的組合將成為應用程式的一部分的任何檔案想要新增）。 呼叫中所儲存影像時**資源**從程式碼的資料夾，就像映像儲存在主要專案樹狀目錄中，指定沒有資料夾位置。 例如：`NSImage.ImageNamed("tags.png")`。
- **自訂的資料夾或子資料夾 [deprecated]** -開發人員可以將自訂資料夾加入至專案的來源樹狀結構，並儲存映像那里。 這個檔案會加入其中的位置可以是巢狀進一步協助組織專案子資料夾中。 例如，如果開發人員加入`Card`專案資料夾的子資料夾`Hearts`到該資料夾，然後儲存映像**Jack.png**中`Hearts`資料夾，`NSImage.ImageNamed("Card/Hearts/Jack.png")`會載入的影像執行階段。
- **[慣用] 資產目錄映像集**-中 「 OS X El Capitan 」 中，加入**資產目錄映像集**包含所有版本或映像的支援各種裝置和縮放因數所需的表示法您應用程式。 而不是依賴影像資產檔案名稱 (**@1x**， **@2x**)。

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>將影像加入至資產目錄映像設定

如上所述，**資產目錄映像集**包含所有版本或映像的支援各種裝置和縮放因數，您的應用程式所需的表示法。 而不是依賴影像資產檔案名稱 （請參閱解析度獨立映像和映像上述的術語表），**映像集**使用資產編輯器來指定哪些裝置和/或解析屬於哪一個映像。

1. 在**方案板**，連按兩下**Assets.xcassets**檔案，以開啟它進行編輯： 

    ![選取 Assets.xcassets](image-images/imageset01.png "選取 Assets.xcassets")
2. 以滑鼠右鍵按一下**資產清單**選取**新映像設定**: 

    [![加入新的映像集](image-images/imageset02.png "加入一組新映像")](image-images/imageset02-large.png#lightbox)
3. 選取新的映像集，編輯器會隨即出現： 

    [![選取 新的映像設定](image-images/imageset03.png "選取新的映像集")](image-images/imageset03-large.png#lightbox)
4. 從這裡我們可以針對每個不同的裝置與需要的解析度來拖曳映像中。 
5. 按兩下新的映像集**名稱**中**資產清單**對其進行編輯： 

    [![編輯影像設定名稱](image-images/imageset04.png "編輯影像設定名稱")](image-images/imageset04-large.png#lightbox)
    
特殊**向量**類別也已經加入至**映像集**，可讓我們包含_PDF_格式化 casset 改為包括在個別的點陣圖檔案中的向量影像不同的解決方法。 使用這個方法時提供的單一向量檔案**@1x**解析度 （向量 PDF 檔案格式） 和**@2x**和**@3x**會產生編譯時期，應用程式的組合中所包含檔案的版本。

[![映像設定編輯器介面](image-images/imageset05.png "映像設定編輯器介面")](image-images/imageset05-large.png#lightbox)

例如，如果您包含`MonkeyIcon.pdf`檔案儲存為資產類別 」 的向量 150px x 150px，編譯時，會在最終的應用程式套件組合中包含資產的下列點陣圖的解析度：

1. **MonkeyIcon@1x.png** -150px x 150px 解析度。
2. **MonkeyIcon@2x.png** -300px x 300px 解析度。
3. **MonkeyIcon@3x.png** -450px x 450px 解析度。

下列應該列入考量時使用 PDF 向量影像資產類別目錄中：

- 當 PDF 將會在編譯階段和出貨最終的應用程式中的點陣圖的點陣圖點陣化，這不是支援完整的向量。
- 一旦設定資產類別中，您無法調整影像的大小。 如果您嘗試調整影像 （無論是在程式碼或使用自動配置和大小類別） 的大小影像會扭曲就像任何其他的點陣圖。

當使用**映像設定**在 Xcode 的介面產生器中，您可以只選取集的名稱的下拉式清單中從**屬性偵測器**: * *

![在 Xcode 的介面產生器中選取映像設定](image-images/imageset06.png "Xcode 的介面產生器中選取映像設定")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>加入新資產集合

當處理資產類別目錄中的映像可能會有當您想要建立新的集合，而不是新增所有您要的映像的時間**Assets.xcassets**集合。 例如，在設計上要求資源時。

若要將新的資產目錄加入至您的專案：

1. 中的專案上按一下滑鼠右鍵**方案板**選取**新增** > **新的檔案...**
2. 選取**Mac** > **資產目錄**，輸入**名稱**集合，然後按一下**新增**按鈕： 

    ![加入新的資產目錄](image-images/asset01.png "加入新的資產目錄")

從這裡您可以使用集合做為預設值相同的方式**Assets.xcassets**自動包含在專案中的集合。


### <a name="adding-images-to-resources"></a>將影像加入至資源

> [!IMPORTANT]
> 使用圖像 macOS 應用程式中的這個方法已被取代 apple。 您應該使用[資產目錄映像集](#asset-catalogs)管理員您的應用程式改為影像。

您可以使用的映像檔中 Xamarin.Mac 應用程式 （無論是使用 C# 程式碼，或從介面產生器） 之前它需要包含在專案中的**資源**資料夾**配套資源**。 若要將檔案加入專案中，執行下列作業：

1. 以滑鼠右鍵按一下**資源**資料夾中的專案中**方案板**選取**新增** > **加入檔案...**: 

    ![若要加入檔案](image-images/add01.png "加入檔案")
2. 從**加入檔案**對話方塊中，選取影像的檔案加入專案中，選取`BundleResource`如**覆寫的建置動作**按一下**開啟**按鈕：

    [![選取要加入的檔案](image-images/add02.png "選取要加入的檔案")](image-images/add02-large.png#lightbox)
3. 如果檔案已不在**資源**資料夾中，您將需要在您想要**複製**，**移動**或**連結**檔案。 挑選其中每個花色您的需求，通常可**複製**:

    ![選取 [新增] 動作](image-images/add04.png "選取 [新增] 動作")
4. 新的檔案會包含在專案中，並讀取供使用： 

    ![新的影像檔加入至方案板](image-images/add03.png "新的影像檔加入至方案填補")
5. 重複此程序的任何所需的映像檔。

您可以做為來源映像使用任何 png、 jpg 或 pdf 檔案，Xamarin.Mac 應用程式中。 在下一步 區段中，我們會探討加入我們的映像的高解析度版本和圖示，以支援 Retina Mac。

> [!IMPORTANT]
> 如果您要新增至映像**資源**資料夾中，您可以保留**覆寫的建置動作**設**預設**。 預設值為此資料夾的建置動作是`BundleResource`。

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>提供所有應用程式的圖形資源的高解析度的版本

任何您加入 Xamarin.Mac 應用程式圖示、 自訂控制項、 自訂游標、 自訂圖案 （等） 中的圖形資產需要高解析度版本除了其標準解析度的版本。 使 Retina 顯示器上的執行配備 Mac 電腦時，您的應用程式看起來效果最佳，這是必要的。


### <a name="adopt-the-2x-naming-convention"></a>採用@2x命名慣例

> [!IMPORTANT]
> 使用圖像 macOS 應用程式中的這個方法已被取代 apple。 您應該使用[資產目錄映像集](#asset-catalogs)管理員您的應用程式改為影像。

當您建立的標準和高解析度版本的映像時，請遵循此映像配對的命名慣例時包含在 Xamarin.Mac 專案：

- **標準解析度**  - **ImageName.filename 延伸**(範例： **tags.png**)
- **高解析度**  -  **ImageName@2x.filename-extension** (範例： **tags@2x.png**)

加入至專案時，它們會出現，如下所示：

![映像中的檔案方案板](image-images/add03.png "方案板中的影像檔")

介面產生器中的 UI 項目指派映像時只會挑選中的檔案_ImageName_**。**_檔案名稱副檔名_格式 (範例： **tags.png**)。 相同的 C# 程式碼中使用的映像，您會選擇的檔案中_ImageName_**。**_檔案名稱副檔名_格式。

當您 Xamarin.Mac 應用程式在 Mac 上，執行_ImageName_**。**_檔案名稱副檔名_格式影像將使用標準的解析度顯示**ImageName@2x.filename-extension**映像會自動挑選 Retina 顯示器的基底 Mac。


## <a name="using-images-in-interface-builder"></a>使用介面產生器中的映像

您已新增到任何影像資源**資源**資料夾中您 Xamarin.Mac 專案，並已設定的建置動作為**BundleResource**自動出現在介面產生器中，而且可以是選取的 UI 項目 （如果它會處理映像）。

若要在介面產生器中使用映像，執行下列作業：

1. 將映像加入**資源**資料夾**建置動作**的`BundleResource`: 

     ![方案板中的影像資源](image-images/ib00.png "方案板中的影像資源")
2. 按兩下**Main.storyboard**開啟介面產生器中進行編輯的檔案： 

     [![編輯主要腳本](image-images/ib01.png "編輯主要腳本")](image-images/ib01-large.png#lightbox)
3. 拖曳採用影像拖曳至設計介面的 UI 項目 (例如，**影像的工具列項目**): 

     ![編輯工具列項目](image-images/ib02.png "編輯工具列項目")
4. 選取您加入的映像**資源**資料夾中的**映像名稱**下拉式清單中： 

     [![選取影像的工具列項目](image-images/ib03.png "選取影像的工具列項目")](image-images/ib03-large.png#lightbox)
5. 在設計介面中，將會顯示所選映像： 

     ![工具列編輯器中所顯示的影像](image-images/ib04.png "工具列編輯器中所顯示的影像")
6. 儲存變更並返回 Visual Studio for Mac 使用 Xcode 進行同步處理。

上述的步驟適用於任何 UI 元素，讓您在中設定其映像屬性**屬性偵測器**。 同樣地，如果您已加入**@2x**映像檔的版本，它將自動用於 Retina 顯示器基礎 Mac。

> [!IMPORTANT]
> 如果映像將無法使用**映像名稱**下拉式清單中，關閉.storyboard 專案在 Xcode 中的，並重新開啟它從 Visual Studio for mac。 如果映像仍然無法使用，請確定其**建置動作**是`BundleResource`和影像加入**資源**資料夾。

## <a name="using-images-in-c-code"></a>在 C# 程式碼中使用映像

當影像載入記憶體 Xamarin.Mac 應用程式中使用 C# 程式碼時，影像會儲存在`NSImage`物件。 如果映像檔案已包含在 Xamarin.Mac 應用程式套件 （包含在資源），請使用下列程式碼載入影像：

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

上述程式碼會使用靜態`ImageNamed("...")`方法`NSImage`類別載入從記憶體中的特定映像**資源**資料夾中，如果找不到映像，`null`會傳回。 像介面產生器中中, 指派的映像，如果您已加入**@2x**映像檔的版本，它將自動用於 Retina 顯示器基礎 Mac。

若要載入外部的應用程式套件組合 （從 Mac 檔案系統） 的映像，請使用下列程式碼：

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>使用範本映像

根據 macOS 應用程式的設計，有些時候可能會當您需要自訂使用者介面，以符合的變更 （例如，根據使用者偏好設定） 的色彩配置內的影像或圖示。

若要達成這個效果，切換_呈現模式_的影像資產到**範本映像**:

[![設定範本映像](image-images/templateimage01.png "設定範本映像")](image-images/templateimage01-large.png#lightbox)

從 Xcode 的介面產生器中，將指派到 UI 控制項的影像資產：

![在 Xcode 的介面產生器中選取影像](image-images/templateimage02.png "Xcode 的介面產生器中選取映像")

或程式碼中，選擇性地設定影像來源：

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

將下列的公用函式加入至檢視控制器：

```csharp
public NSImage ImageTintedWithColor (NSImage image, NSColor tint)
{
    var tintedImage = image.Copy () as NSImage;
    var frame = new CGRect (0, 0, image.Size.Width, image.Size.Height);

    // Apply tint
    tintedImage.LockFocus ();
    tint.Set ();
    NSGraphics.RectFill (frame, NSCompositingOperation.SourceAtop);
    tintedImage.UnlockFocus ();
    tintedImage.Template = false;

    // Return tinted image
    return tintedImage;
}
```

最後，用來設定濃淡範本映像，呼叫此函式對來標示映像：

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>使用資料表檢視中的映像

要包含映像中的資料格的一部分`NSTableView`，您必須變更 [資料表] 檢視所傳回資料的`NSTableViewDelegate's``GetViewForItem`方法，以使用`NSTableCellView`而不是一般`NSTextField`。 例如: 

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

有幾個重要的行。 首先，我們想要包含影像的資料行，我們會建立新`NSImageView`所需的大小和位置中，我們也會建立新`NSTextField`，並將它是否我們使用映像為基礎的預設位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我們需要加入新的映像檢視和文字欄位父項中`NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最後，我們要告知文字欄位，可以壓縮和成長資料表的檢視資料格：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

範例輸出：

[![範例應用程式中顯示影像](image-images/tables01.png "範例應用程式中顯示的影像")](image-images/tables01-large.png#lightbox)

如需使用的資料表檢視的詳細資訊，請參閱我們[資料表檢視](~/mac/user-interface/table-view.md)文件。

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>大綱檢視中使用映像

要包含映像中的資料格的一部分`NSOutlineView`，您必須變更大綱檢視所傳回資料的`NSTableViewDelegate's``GetView`方法，以使用`NSTableCellView`而不是一般`NSTextField`。 例如: 

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

有幾個重要的行。 首先，我們想要包含影像的資料行，我們會建立新`NSImageView`所需的大小和位置中，我們也會建立新`NSTextField`，並將它是否我們使用映像為基礎的預設位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我們需要加入新的映像檢視和文字欄位父項中`NSTableCellView`:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最後，我們要告知文字欄位，可以壓縮和成長資料表的檢視資料格：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

範例輸出：

[![大綱檢視中所顯示之影像的範例](image-images/outline01.png "大綱檢視中所顯示之影像的範例")](image-images/outline01-large.png#lightbox)

如需有關使用大綱檢視的詳細資訊，請參閱我們[大綱檢視](~/mac/user-interface/outline-view.md)文件。


## <a name="summary"></a>總結

這篇文章已取得 Xamarin.Mac 應用程式中使用影像和圖示的詳細的檢視。 我們會看到不同的類型，並使用映像、 如何在 Xcode 的介面產生器中使用影像和圖示和如何在 C# 程式碼中使用影像和圖示。



## <a name="related-links"></a>相關連結

- [MacImages (範例)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料表檢視](~/mac/user-interface/table-view.md)
- [大綱檢視](~/mac/user-interface/outline-view.md)
- [macOS X 人性化介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [關於 OS X 的高解析度](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html) \(英文\)
