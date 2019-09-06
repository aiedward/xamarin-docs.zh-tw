---
title: Xamarin. Mac 中的影像
description: 本文說明如何在 Xamarin. Mac 應用程式中使用影像和圖示。 其中說明如何建立和維護建立應用程式圖示所需的影像，以及在程式C#代碼和 Xcode 的 Interface Builder 中使用影像。
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 03/15/2017
ms.openlocfilehash: b014c84a7bb23e723fbfbb7ead5e8dc9d9fdc549
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281697"
---
# <a name="images-in-xamarinmac"></a>Xamarin. Mac 中的影像

_本文說明如何在 Xamarin. Mac 應用程式中使用影像和圖示。其中說明如何建立和維護建立應用程式圖示所需的影像，以及在程式C#代碼和 Xcode 的 Interface Builder 中使用影像。_

## <a name="overview"></a>總覽

在 Xamarin. C# Mac 應用程式中使用和 .net 時，您可以存取開發人員在*目標-C*和*Xcode*中工作的相同影像與圖示工具。

有數種方式可在 macOS （先前稱為 Mac OS X）應用程式內使用影像資產。 從只將影像顯示為應用程式 UI 的一部分，並將它指派給 UI 控制項（例如工具列或來源清單專案）以提供圖示，Xamarin 可讓您以下列方式輕鬆地在 macOS 應用程式中新增絕佳的作品。: 

- **UI 元素**-影像可以在影像視圖中顯示為背景或應用程式的一部分（`NSImageView`）。
- **按鈕**-影像可以在按鈕中顯示（`NSButton`）。
- **影像資料格**-當做以資料表為基礎的控制項`NSTableView` （ `NSOutlineView`或）的一部分，影像可以用於影像資料格`NSImageCell`（）。
- **工具列專案**-影像可以加入工具列（`NSToolbar`）做為影像工具列專案（`NSToolbarItem`）。
- **來源清單圖示**-做為來源清單的一部分（特殊格式`NSOutlineView`）。
- **應用程式圖示**-一系列的影像可以組合成一個`.icns`集合，並作為應用程式的圖示。 如需詳細資訊，請參閱我們的[應用程式圖示](~/mac/deploy-test/app-icon.md)檔。

此外，macOS 還提供一組預先定義的映射，可在整個應用程式中使用。

[![應用程式的範例執行](image-images/intro01.png "應用程式的範例執行")](image-images/intro01-large.png#lightbox)

在本文中，我們將討論在 Xamarin. Mac 應用程式中使用影像和圖示的基本概念。 強烈建議您先流覽[Hello，Mac](~/mac/get-started/hello-mac.md)文章，特別是[Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder)和「[輸出」和「動作](~/mac/get-started/hello-mac.md#outlets-and-actions)」區段的簡介，其中涵蓋了我們將在中使用的重要概念和技巧。本文。


## <a name="adding-images-to-a-xamarinmac-project"></a>將影像新增至 Xamarin. Mac 專案

新增要在 Xamarin. Mac 應用程式中使用的映射時，有幾個地方和方法可讓開發人員將影像檔案加入專案的來源：

- **主要專案樹狀結構 [取代]** -可以將影像直接加入至專案樹狀結構。 從程式碼呼叫儲存在主要專案樹狀結構中的影像時，未指定資料夾位置。 例如：`NSImage image = NSImage.ImageNamed("tags.png");`。 
- **Resources 資料夾 [取代]** -[特殊**資源**] 資料夾適用于將成為應用程式組合之一部分的任何檔案，例如圖示、啟動畫面或一般映射（或開發人員希望新增的任何其他映射或檔案）。 從程式碼呼叫儲存在**Resources**資料夾中的影像時，就像是儲存在主要專案樹狀結構中的影像一樣，並不會指定任何資料夾位置。 例如：`NSImage.ImageNamed("tags.png")`。
- **自訂資料夾或子資料夾 [取代]** -開發人員可以將自訂資料夾加入至專案來源樹狀結構，並在該處儲存影像。 新增檔案的位置可以嵌套在子資料夾中，以進一步協助組織專案。 `Card`例如，如果開發人員已將資料夾新增至專案，並將的`Hearts`子資料夾加入至該資料夾，則會在此`Hearts` **資料夾中儲存**影像， `NSImage.ImageNamed("Card/Hearts/Jack.png")`並在執行時間載入影像。
- **資產目錄映射集 [慣用]** -新增在 OS X El Capitan 中，**資產目錄影像集**包含支援應用程式的各種裝置和調整因素所需之映射的所有版本或標記法。 而不是依賴影像資產 filename （ **@1x** ， **@2x** ）。

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>將影像新增至資產目錄映射集

如上所述，**資產目錄影像集**包含影像的所有版本或標記法，以支援應用程式的各種裝置和調整因素。 **映射集**會使用「資產編輯器」來指定哪些映射屬於哪些裝置和/或解決方案，而不是依賴「映射資產」檔案名（請參閱上述的解決獨立映射和影像命名法）。

1. 在  **Solution Pad**中，按兩下**assets.xcassets**檔案以開啟它進行編輯： 

    ![選取 [資產]。 assets.xcassets](image-images/imageset01.png "選取 [資產]。 assets.xcassets")
2. 以滑鼠右鍵按一下 [**資產] 清單**，然後選取 [**新增映射集**]： 

    [![加入新的映射集](image-images/imageset02.png "加入新的映射集")](image-images/imageset02-large.png#lightbox)
3. 選取新的映射集，就會顯示編輯器： 

    [![選取新的映射集](image-images/imageset03.png "選取新的映射集")](image-images/imageset03-large.png#lightbox)
4. 在這裡，我們可以針對每個不同的裝置和所需的解析度來拖曳影像。 
5. 按兩下 [**資產] 清單**中的新映射集**名稱**以進行編輯： 

    [![編輯映射集名稱](image-images/imageset04.png "編輯映射集名稱")](image-images/imageset04-large.png#lightbox)
    
已新增至**影像集**的特殊**向量**類別，可讓我們在 casset 中包含_PDF_格式的向量影像，而不會在不同的解析度上包括個別的點陣圖檔案。 使用此方法，您提供的單一向量檔案 **@1x** 解析 （格式為向量的 PDF 檔案） 和 **@2x** 並 **@3x** 會在編譯時期產生和應用程式的組合中所包含的檔案版本。

[![映射集編輯器介面](image-images/imageset05.png "映射集編輯器介面")](image-images/imageset05-large.png#lightbox)

例如，如果您`MonkeyIcon.pdf`將檔案當做具有 150px x 150px 解析的資產目錄向量，則下列點陣圖資產會在編譯時包含在最終的應用程式套件組合中：

1. **MonkeyIcon@1x.png** -150px x 150px 解析。
2. **MonkeyIcon@2x.png** -300px x 300px 解析。
3. **MonkeyIcon@3x.png** -450px x 450px 解析。

在資產目錄中使用 PDF 向量影像時，應該考慮下列事項：

- 這不是完整的向量支援，因為在編譯時期，PDF 會柵格化至點陣圖，而在最後一個應用程式中傳送的點陣圖。
- 一旦在資產類別目錄中設定映射後，您就無法調整其大小。 如果您嘗試調整影像大小（在程式碼中，或使用自動設定和大小類別），影像就會扭曲，就像任何其他點陣圖一樣。

在 Xcode 的 Interface Builder 中使用**影像集**時，您可以直接從**屬性偵測器**的下拉式清單中選取集合的名稱：

![在 Xcode 的 Interface Builder 中選取映射集](image-images/imageset06.png "在 Xcode 的 Interface Builder 中選取映射集")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>加入新的資產集合

使用資產目錄中的影像時，有時您可能會想要建立新的集合，而不是將所有影像新增至**assets.xcassets**集合。 例如，在設計隨選資源時。

若要將新的資產目錄新增至您的專案：

1. 以滑鼠右鍵按一下  **Solution Pad**中的專案，然後選取 **加入** > **新檔案 ...** 。
2. 選取 [ **Mac**  > **資產目錄**]，輸入集合的**名稱**，然後按一下 [**新增**] 按鈕： 

    ![加入新的資產目錄](image-images/asset01.png "加入新的資產目錄")

從這裡，您可以使用與預設**assets.xcassets**集合自動包含在專案中相同的方式來處理集合。


### <a name="adding-images-to-resources"></a>將影像新增至資源

> [!IMPORTANT]
> Apple 已淘汰在 macOS 應用程式中使用影像的這種方法。 您應該改為使用[資產目錄映射集](#asset-catalogs)來管理應用程式的映射。

您必須先將映射檔案包含在專案的 [**資源**] 資料夾中， C#以做為組合**資源**，才能在 Xamarin. Mac 應用程式中使用影像檔案（不論是在程式碼中或從 Interface Builder）。 若要將檔案新增至專案，請執行下列動作：

1. 在**Solution Pad**中，以滑鼠右鍵按一下專案中的 [**資源**] 資料夾，然後選取 [**新增** > ] [**新增**檔案]： 

    ![新增]檔案(image-images/add01.png "新增")檔案
2. 從 [**新增**檔案] 對話方塊中，選取要新增至專案的影像檔案，選取`BundleResource` [覆**寫組建] 動作**，然後按一下 [**開啟**] 按鈕：

    [![選取要新增的]檔案(image-images/add02.png "選取要新增的")檔案](image-images/add02-large.png#lightbox)
3. 如果檔案尚未在**Resources**資料夾中，系統會詢問您是否要**複製**、**移動**或**連結**檔案。 選擇每個符合您需求的，通常會**複製**：

    ![選取 [新增] 動作](image-images/add04.png "選取 [新增] 動作")
4. 新檔案會包含在專案中，並讀取以供使用： 

    ![新增至 Solution Pad 的新影像檔]案(image-images/add03.png "新增至 Solution Pad 的新影像檔")案
5. 針對任何需要的影像檔重複此程式。

您可以使用任何 png、jpg 或 pdf 檔案做為 Xamarin. Mac 應用程式中的來源影像。 在下一節中，我們將探討如何新增影像和圖示的高解析度版本，以支援以 Retina 為基礎的 Mac。

> [!IMPORTANT]
> 如果您要將影像新增至 [**資源**] 資料夾，您可以將 [覆**寫組建] 動作**設定為 [**預設**]。 此資料夾的預設組建動作是`BundleResource`。

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>提供所有應用程式圖形資源的高解析度版本

除了其標準解析版本外，您新增至 Xamarin Mac 應用程式的任何圖形資產（圖示、自訂控制項、自訂游標、自訂插圖等等）都必須具有高解析度版本。 這是必要的，讓您的應用程式在 Retina 顯示配備的 Mac 電腦上執行時，能發揮最佳效果。


### <a name="adopt-the-2x-naming-convention"></a>@2x採用命名慣例

> [!IMPORTANT]
> Apple 已淘汰在 macOS 應用程式中使用影像的這種方法。 您應該改為使用[資產目錄映射集](#asset-catalogs)來管理應用程式的映射。

當您建立映射的標準和高解析度版本時，請在您的 Xamarin. Mac 專案中包含映射組時，遵循此命名慣例：

- **標準解析度**  -  **ImageName. 副檔名**（例如：**標記 .png**）
- **高解析度**  -  **tags@2x.png** （**範例：）ImageName@2x.filename-extension**

新增至專案時，會顯示如下：

![Solution Pad 中的影像檔]案(image-images/add03.png "Solution Pad 中的影像檔")案

將影像指派給中的 UI 專案時 Interface Builder 您只需在_ImageName_中挑選檔案 **。** _檔案名-延伸_格式（範例：**標記 .png**）。 在程式碼中C#使用影像的方式相同，您會在_ImageName_中挑選檔案 **。** _檔案名-延伸_模組格式。

當您在 Mac 上執行 Xamarin. Mac 應用程式時， _ImageName_ **。** _檔案名-延伸_格式影像將會用於標準解析度顯示， **ImageName@2x.filename-extension** 並會自動在 Retina 顯示基底 mac 上選取影像。


## <a name="using-images-in-interface-builder"></a>在 Interface Builder 中使用影像

您已在 Xamarin. Mac 專案中新增至**Resources**資料夾並將組建動作設定為**BundleResource**的任何影像資源，都會自動顯示在 Interface Builder 中，而且可以選取為 UI 元素的一部分（如果它處理影像）。

若要在 interface builder 中使用影像，請執行下列動作：

1. 將影像新增至 [**資源**] 資料夾，並將 [ `BundleResource`**建立] 動作**設為： 

     ![Solution Pad 中的影像資源](image-images/ib00.png "Solution Pad 中的影像資源")
2. 按兩下**主要**的分鏡腳本檔案，將它開啟，以便在 Interface Builder 中進行編輯： 

     [![編輯主要]分鏡腳本(image-images/ib01.png "編輯主要")分鏡腳本](image-images/ib01-large.png#lightbox)
3. 將接受影像的 UI 專案拖曳到設計介面上（例如，**影像工具列專案**）： 

     ![編輯工具列專案](image-images/ib02.png "編輯工具列專案")
4. 選取您在 [**映射名稱**] 下拉式清單中新增至 [**資源**] 資料夾的映射： 

     [![選取工具列專案的影像](image-images/ib03.png "選取工具列專案的影像")](image-images/ib03-large.png#lightbox)
5. 選取的影像將會顯示在設計介面中： 

     ![顯示在工具列編輯器中的影像](image-images/ib04.png "顯示在工具列編輯器中的影像")
6. 儲存您的變更，並返回 Visual Studio for Mac 以與 Xcode 同步。

上述步驟適用于可在**屬性偵測器**中設定其影像屬性的任何 UI 元素。 同樣地，如果您已加入 **@2x** 映像檔的版本，它會自動使用在 Retina 顯示器基礎 Mac。

> [!IMPORTANT]
> 如果 [**映射名稱**] 下拉式清單中的影像無法使用，請在 Xcode 中關閉您的分鏡腳本專案，並從 Visual Studio for Mac 將它重新開啟。 如果影像仍然無法使用，請確定其**組建動作**為`BundleResource` ，而且已將映射新增至**Resources**資料夾。

## <a name="using-images-in-c-code"></a>在程式碼C#中使用影像

使用C# Xamarin. Mac 應用程式中的程式碼將影像載入記憶體時，影像會儲存在`NSImage`物件中。 如果影像檔案已包含在 Xamarin. Mac 應用程式套件組合中（包含在資源中），請使用下列程式碼來載入映射：

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

上述程式碼`ImageNamed("...")`會使用`NSImage`類別的靜態方法，從**Resources**資料夾將指定的影像載入記憶體中，如果找不到影像， `null`則會傳回。 例如 Interface Builder 中指派的影像，如果您已加入 **@2x** 映像檔的版本，它會自動使用在 Retina 顯示器基礎 Mac。

若要將影像載入應用程式組合的外部（從 Mac 檔案系統），請使用下列程式碼：

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>使用範本映射

根據您的 macOS 應用程式的設計，有時候您可能需要自訂使用者介面內的圖示或影像，以符合色彩配置中的變更（例如，根據使用者喜好設定）。

若要達到此效果，請將影像資產的轉譯_模式_切換至**範本映射**：

[![設定範本映射](image-images/templateimage01.png "設定範本映射")](image-images/templateimage01-large.png#lightbox)

從 Xcode 的 Interface Builder，將影像資產指派給 UI 控制項：

![在 Xcode 的 Interface Builder 中選取影像](image-images/templateimage02.png "在 Xcode 的 Interface Builder 中選取影像")

或者，選擇性地在程式碼中設定影像來源：

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

將下列公用函式新增至您的 View Controller：

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
> 特別是在 macOS Mojave 中出現深色模式時，請務必在 reating 自訂`LockFocus`轉譯`NSImage`的物件時，避免使用 API。 這類影像會變成靜態，而且不會自動更新以考慮外觀或顯示密度變更。
>
> 藉由採用上述以處理常式為基礎的機制，在裝載時（例如， `NSImage` `NSImageView`在中），動態條件的重新呈現會自動發生。

最後，若要為範本影像著色，請針對影像呼叫此函式以著色：

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>搭配資料表視圖使用影像

若要將影像加入至中`NSTableView`的儲存格，您必須變更資料表視圖的`NSTableViewDelegate's` `GetViewForItem`方法傳回資料的方式，以使用`NSTableCellView` ，而不是一般`NSTextField`的。 例如：

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

這裡有幾行需要注意。 首先，對於我們要包含影像的資料行，我們會建立`NSImageView`新的所需大小和位置，我們也會建立新`NSTextField`的，並根據我們是否使用影像來放置其預設位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我們必須在父系`NSTableCellView`中包含新的影像視圖和文字欄位：

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最後，我們需要告訴文字欄位，它可以使用資料表視圖儲存格縮小並成長：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

範例輸出︰

[![在應用程式中顯示影像的範例](image-images/tables01.png "在應用程式中顯示影像的範例")](image-images/tables01-large.png#lightbox)

如需使用資料表視圖的詳細資訊，請參閱我們的[資料表視圖](~/mac/user-interface/table-view.md)檔。

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>使用具有大綱視圖的影像

若要將影像納入中`NSOutlineView`的儲存格，您必須變更大綱視圖的`NSTableViewDelegate's` `GetView`方法傳回資料的方式，以使用`NSTableCellView` ，而不是一般`NSTextField`的。 例如：

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

這裡有幾行需要注意。 首先，對於我們要包含影像的資料行，我們會建立`NSImageView`新的所需大小和位置，我們也會建立新`NSTextField`的，並根據我們是否使用影像來放置其預設位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我們必須在父系`NSTableCellView`中包含新的影像視圖和文字欄位：

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最後，我們需要告訴文字欄位，它可以使用資料表視圖儲存格縮小並成長：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

範例輸出︰

[![在大綱視圖中顯示的影像範例](image-images/outline01.png "在大綱視圖中顯示的影像範例")](image-images/outline01-large.png#lightbox)

如需使用大綱視圖的詳細資訊，請參閱我們的[大綱視圖](~/mac/user-interface/outline-view.md)檔。


## <a name="summary"></a>總結

本文已詳細探討如何在 Xamarin. Mac 應用程式中使用影像和圖示。 我們看到影像的不同類型和用途，如何在 Xcode 的 Interface Builder 中使用影像和圖示，以及如何在程式碼中C#處理影像和圖示。



## <a name="related-links"></a>相關連結

- [MacImages (範例)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料表視圖](~/mac/user-interface/table-view.md)
- [大綱視圖](~/mac/user-interface/outline-view.md)
- [macOS X 人力介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [關於 OS X 的高解析度](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html) \(英文\)
