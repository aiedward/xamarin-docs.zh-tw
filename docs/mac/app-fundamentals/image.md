---
title: Xamarin 中的影像
description: '本文說明如何在 Xamarin 應用程式中使用影像和圖示。 它說明如何建立和維護建立應用程式圖示所需的映射，以及在 c # 程式碼和 Xcode 的 Interface Builder 中使用影像。'
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: 53a3b43347e51cfbc99b2b798f0ad5c787f04847
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435560"
---
# <a name="images-in-xamarinmac"></a>Xamarin 中的影像

_本文說明如何在 Xamarin 應用程式中使用影像和圖示。它說明如何建立和維護建立應用程式圖示所需的映射，以及在 c # 程式碼和 Xcode 的 Interface Builder 中使用影像。_

## <a name="overview"></a>概觀

在 Xamarin 應用程式中使用 c # 和 .NET 時，您可以存取開發人員在 *c* 和 *Xcode* 中運作的相同影像和圖示工具。

有幾種方式可在 macOS (中使用影像資產，之前稱為 Mac OS X) 應用程式。 從單純地將影像顯示為應用程式 UI 的一部分，並將其指派給 UI 控制項（例如工具列或來源清單專案），以提供圖示，Xamarin 可讓您輕鬆地以下列方式在 macOS 應用程式中新增絕佳的作品： 

- **UI 元素** -影像可以在影像視圖中顯示為背景或作為應用程式的一部分， (`NSImageView`) 。
- **按鈕** -影像可以顯示在 () 的按鈕中 `NSButton` 。
- **影像儲存格** -作為以資料表為基礎的控制項 (`NSTableView` 或) 的一部分 `NSOutlineView` ，可以在影像儲存格中使用影像 (`NSImageCell`) 。
- **工具列專案** ：您可以將影像加入工具列 (`NSToolbar`) 為影像工具列專案 (`NSToolbarItem`) 。
- **來源清單圖示** -作為來源清單的一部分， (特殊格式化的 `NSOutlineView`) 。
- **應用程式圖示** -一系列的影像可組合成一個 `.icns` 集合，並做為應用程式的圖示。 如需詳細資訊，請參閱我們的 [應用程式圖示](~/mac/deploy-test/app-icon.md) 檔。

此外，macOS 還提供一組預先定義的映射，可在整個應用程式中使用。

[![應用程式的範例執行](image-images/intro01.png "應用程式的範例執行")](image-images/intro01-large.png#lightbox)

在本文中，我們將討論如何在 Xamarin 應用程式中使用影像和圖示的基本概念。 強烈建議您先完成 [Hello，Mac](~/mac/get-started/hello-mac.md) 文章，特別是 [Xcode 和 Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) 和 [輸出和動作](~/mac/get-started/hello-mac.md#outlets-and-actions) 章節的簡介，因為它涵蓋了我們將在本文中使用的重要概念和技術。

## <a name="adding-images-to-a-xamarinmac-project"></a>將影像新增至 Xamarin. Mac 專案

在 Xamarin 應用程式中新增要使用的映射時，開發人員可以將影像檔包含在專案的來源中有幾個地方和方法：

- **主要專案樹狀結構 [已淘汰]** -可以直接將影像新增至專案樹狀結構。 從程式碼呼叫儲存在主要專案樹狀結構中的影像時，不會指定任何資料夾位置。 例如： `NSImage image = NSImage.ImageNamed("tags.png");` 。 
- **Resources 資料夾 [已淘汰]** -特殊 **資源** 資料夾適用于將成為應用程式套件組合一部分的任何檔案，例如圖示、啟動畫面或一般影像 (或開發人員想要新增) 的任何其他影像或檔案。 從程式碼呼叫儲存在 **Resources** 資料夾中的影像時（如同儲存在主要專案樹狀結構中的影像），並不會指定資料夾位置。 例如： `NSImage.ImageNamed("tags.png")` 。
- **自訂資料夾或子資料夾 [已淘汰]** -開發人員可以將自訂資料夾新增至專案來源樹狀結構，並將影像儲存在該處。 加入檔案的位置可以嵌套在子資料夾中，以進一步協助組織專案。 例如，如果開發人員將資料夾新增 `Card` 至專案，並將子資料夾加入 `Hearts` 至該資料夾，則會將影像 **Jack.png** 儲存在該資料夾中 `Hearts` ， `NSImage.ImageNamed("Card/Hearts/Jack.png")` 這會在執行時間載入映射。
- **資產目錄映射集 [慣用]** -已在 OS X El Capitan 中新增， **資產目錄映射集** 包含支援各種裝置的所有版本或標記法，以及為您的應用程式調整比例所需的影像。 不要依賴映射資產的檔案名 (**@1x** ， **@2x**) 。

<a name="asset-catalogs"></a>

### <a name="adding-images-to-an-asset-catalog-image-set"></a>將影像新增至資產目錄映射集

如上所述， **資產目錄映射集** 包含支援各種裝置的所有版本或標記法，以及為應用程式調整規模的各項需求。 映射集不會依賴映射資產的檔案名 (請參閱上述的解析度獨立映射和影像的命名法) ， **映射集會** 使用資產編輯器來指定哪個映射屬於哪個裝置及/或解析度。

1. 在 [ **Solution Pad**中，按兩下 **>appicon** 檔案以開啟它進行編輯： 

    ![選取資產。 >appicon](image-images/imageset01.png "選取資產。 >appicon")
2. 以滑鼠右鍵按一下 [ **資產] 清單** ，然後選取 [ **新增映射集**： 

    [![新增映射集](image-images/imageset02.png "新增映射集")](image-images/imageset02-large.png#lightbox)
3. 選取新的映射集，系統就會顯示編輯器： 

    [![選取新的映射集](image-images/imageset03.png "選取新的映射集")](image-images/imageset03-large.png#lightbox)
4. 從這裡，我們可以針對每個不同的裝置和所需的解析度拖曳影像。 
5. 按兩下 [**資產] 清單**中的新映射集**名稱**以進行編輯： 

    [![編輯映射集名稱](image-images/imageset04.png "編輯映射集名稱")](image-images/imageset04-large.png#lightbox)
    
新增至**影像集**的特殊**向量**類別，可讓我們在 casset 中包含_PDF_格式的向量影像，而不是在不同的解析度中包含個別點陣圖檔案。 使用這個方法時，您可以為解析度提供單一向量檔 **@1x** (格式為向量 PDF 檔案) ，而且檔案的 **@2x** 和 **@3x** 版本將會在編譯時期產生，並且包含在應用程式套件組合中。

[![影像集編輯器介面](image-images/imageset05.png "影像集編輯器介面")](image-images/imageset05-large.png#lightbox)

例如，如果您將檔案包含 `MonkeyIcon.pdf` 為資產目錄的向量，並解析為 150px x 150px，則在編譯時，最終的應用程式套件組合中將會包含下列點陣圖資產：

1. **MonkeyIcon@1x.png** -150px x 150px 解析度。
2. **MonkeyIcon@2x.png** -300px x 300px 解析度。
3. **MonkeyIcon@3x.png** -450px x 450px 解析度。

使用資產目錄中的 PDF 向量影像時，應考慮下列事項：

- 這並不是完整的向量支援，因為 PDF 會在編譯時期以點陣圖方式加入點陣圖，並在最終應用程式中寄出點陣圖。
- 在資產目錄中設定影像之後，您就無法調整影像的大小。 如果您嘗試以程式碼或使用自動設定和大小類別來調整影像 (，) 影像將會像任何其他點陣圖一樣失真。

使用 Xcode 的 Interface Builder 中的 **影像集** 時，您可以直接從 **屬性偵測器**的下拉式清單中選取集合的名稱：

![在 Xcode 的 Interface Builder 中選取映射集](image-images/imageset06.png "在 Xcode 的 Interface Builder 中選取映射集")

<a name="Adding-new-Assets-Collections"></a>

### <a name="adding-new-assets-collections"></a>加入新的資產集合

使用資產目錄中的影像時，有時候您可能會想要建立新的集合，而不是將所有影像新增至 **>appicon** 集合。 例如，在設計隨選資源時。

若要將新的資產目錄新增至您的專案：

1. 以滑鼠右鍵按一下**Solution Pad**中的專案，然後選取 [**加入**  >  **新**檔案]。
2. 選取 [ **Mac**  >  **資產目錄**]，輸入集合的**名稱**，然後按一下 [**新增**] 按鈕： 

    ![加入新的資產目錄](image-images/asset01.png "加入新的資產目錄")

從這裡開始，您可以使用與預設 **>appicon** 集合一樣的方式來處理集合。

### <a name="adding-images-to-resources"></a>將影像新增至資源

> [!IMPORTANT]
> Apple 已淘汰在 macOS 應用程式中使用影像的這個方法。 您應改為使用 [資產目錄映射集](#asset-catalogs) 來管理應用程式的映射。

您必須先將專案的 [資源] 資料夾中包含在專案的 [ **資源** ] 資料夾中，以作為套件組合 **資源**，才能在您的 Xamarin 應用程式中使用影像檔) Interface Builder (。 若要將檔案新增至專案，請執行下列動作：

1. 在**Solution Pad**中，以滑鼠右鍵按一下專案中的 [ **Resources** ] 資料夾，然後選取 [**新增**檔案  >  **...**： 

    ![新增檔案](image-images/add01.png "新增檔案")
2. 從 [ **新增** 檔案] 對話方塊中，選取要新增至專案的影像檔，選取 `BundleResource` [覆 **寫組建] 動作** ，然後按一下 [ **開啟** ] 按鈕：

    [![選取要新增的檔案](image-images/add02.png "選取要新增的檔案")](image-images/add02-large.png#lightbox)
3. 如果檔案尚未在 **Resources** 資料夾中，系統會詢問您是否要 **複製**、 **移動** 或 **連結** 檔案。 挑選每個都符合您的需求，通常會 **複製**：

    ![選取 [新增] 動作](image-images/add04.png "選取 [新增] 動作")
4. 新檔案將包含在專案中，並讀取以供使用： 

    ![新增至 Solution Pad 的新影像檔](image-images/add03.png "新增至 Solution Pad 的新影像檔")
5. 針對所需的任何影像檔重複此程式。

您可以使用任何 png、jpg 或 pdf 檔案，做為您 Xamarin. Mac 應用程式中的來源影像。 在下一節中，我們將探討如何新增影像和圖示的高解析度版本，以支援以 Retina 為基礎的 Mac。

> [!IMPORTANT]
> 如果您要將影像新增至 [ **資源** ] 資料夾，您可以將 [覆 **寫組建] 動作** 設為 [ **預設**]。 此資料夾的預設組建動作為 `BundleResource` 。

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>提供所有應用程式圖形資源的高解析度版本

您新增至 Xamarin 應用程式的任何圖形資產 (圖示、自訂控制項、自訂資料指標、自訂圖形等等 ) 除了標準解析度版本之外，還需要有高解析度版本。 這是必要的，如此一來，當您的應用程式在 Retina 顯示器配備的 Mac 電腦上執行時，您的應用程式就能

### <a name="adopt-the-2x-naming-convention"></a>採用 @2x 命名慣例

> [!IMPORTANT]
> Apple 已淘汰在 macOS 應用程式中使用影像的這個方法。 您應改為使用 [資產目錄映射集](#asset-catalogs) 來管理應用程式的映射。

當您建立映射的標準和高解析度版本時，請在您的 Xamarin 專案中包含此映射組的命名慣例：

- **標準解析度**   - **ImageName 副檔名** (範例： **tags.png**) 
- **高解析度**   -  **ImageName@2x.filename-extension** (範例： **tags@2x.png**) 

新增至專案時，會顯示如下：

![Solution Pad 中的影像檔案](image-images/add03.png "Solution Pad 中的影像檔案")

將影像指派給 Interface Builder 中的 UI 專案時，您只需要在 ImageName 中挑選檔案_ImageName_**。**_檔案名延伸_格式 (範例： **tags.png**) 。 使用 c # 程式碼中的影像時，您將會在_ImageName_中挑選檔案 **。**_檔案名延伸_格式。

當您在 Mac 上執行 Xamarin 的 Mac 應用程式時， _ImageName_**。**_檔案名-延伸_ 格式影像將用於標準解析度顯示， **ImageName@2x.filename-extension** 影像將會自動在 Retina 顯示庫 mac 上挑選。

## <a name="using-images-in-interface-builder"></a>在 Interface Builder 中使用影像

您已新增至 Xamarin 專案中 [ **資源** ] 資料夾的任何映射資源，並已將 [組建] 動作設為 [ **套件套件** ]，將會自動顯示在 Interface Builder 中，並且可以選取為 UI 元素的一部分 (如果它處理) 的影像。

若要在 interface builder 中使用影像，請執行下列動作：

1. 將映射新增至 [ **Resources** ] 資料夾，其 **組建動作** 為 `BundleResource` ： 

     ![Solution Pad 中的影像資源](image-images/ib00.png "Solution Pad 中的影像資源")
2. 按兩下 **主要** 的分鏡腳本檔案，在 Interface Builder 中開啟以進行編輯： 

     [![編輯主要分鏡腳本](image-images/ib01.png "編輯主要分鏡腳本")](image-images/ib01-large.png#lightbox)
3. 將拍攝影像的 UI 元素拖曳至設計介面 (例如， **影像工具列專案**) ： 

     ![編輯工具列專案](image-images/ib02.png "編輯工具列專案")
4. 在 [**映射名稱**] 下拉式清單中，選取您新增至**Resources**資料夾的映射： 

     [![選取工具列專案的影像](image-images/ib03.png "選取工具列專案的影像")](image-images/ib03-large.png#lightbox)
5. 選取的影像將會顯示在設計介面中： 

     ![顯示在工具列編輯器中的影像](image-images/ib04.png "顯示在工具列編輯器中的影像")
6. 儲存您的變更並返回 Visual Studio for Mac，以與 Xcode 同步。

上述步驟適用于允許在 **屬性偵測器**中設定其影像屬性的任何 UI 元素。 同樣地，如果您已包含 **@2x** 映射檔案的版本，它將會自動用於以 Retina 顯示為基礎的 mac。

> [!IMPORTANT]
> 如果映射 **名稱** 下拉式清單中未提供映射，請在 Xcode 中關閉您的分鏡腳本專案，然後從 Visual Studio for Mac 重新開啟。 如果映射仍然無法使用，請確定其 **組建動作** 為 `BundleResource` ，而且映射已新增至 **Resources** 資料夾。

## <a name="using-images-in-c-code"></a>使用 c # 程式碼中的影像

當您在 Xamarin. Mac 應用程式中使用 c # 程式碼將映射載入記憶體時，映射會儲存在 `NSImage` 物件中。 如果已將映射檔案包含在 (資源) 中的 Xamarin 應用程式配套中，請使用下列程式碼來載入映射：

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

上述程式碼會使用類別的靜態 `ImageNamed("...")` 方法 `NSImage` ，將指定的影像從 **Resources** 資料夾載入至記憶體中，如果找不到影像，則 `null` 會傳回。 就像 Interface Builder 中指派的影像一樣，如果您已包含映射檔案的 **@2x** 版本，它會自動用於 Retina 顯示器的 mac。

若要從 Mac 檔案系統) 載入應用程式配套 (外的映射，請使用下列程式碼：

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"></a>

## <a name="working-with-template-images"></a>使用範本映射

根據您的 macOS 應用程式設計，有時候您可能需要自訂消費者介面內的圖示或影像，以符合色彩配置的變更 (例如，根據使用者喜好設定) 。

若要達到此效果，請將影像資產的轉譯 _模式_ 切換為 **範本映射**：

[![設定範本映射](image-images/templateimage01.png "設定範本映射")](image-images/templateimage01-large.png#lightbox)

從 Xcode 的 Interface Builder，將影像資產指派給 UI 控制項：

![在 Xcode 的 Interface Builder 中選取影像](image-images/templateimage02.png "在 Xcode 的 Interface Builder 中選取影像")

或者，您也可以選擇在程式碼中設定影像來源：

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

將下列公用函式新增至您的 View 控制器：

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
> 尤其是在 macOS Mojave 中出現深色模式時，請務必避免在 `LockFocus` reating 自訂轉譯物件時使用 API `NSImage` 。 這類影像會變成靜態，且不會自動更新以考慮外觀或顯示密度變更。
>
> 藉由採用上述以處理常式為基礎的機制，當裝載時（例如在中），動態條件的重新呈現將會自動發生 `NSImage` `NSImageView` 。

最後，若要以色彩標示範本映射，請針對影像呼叫這個函式以進行著色：

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views"></a>

## <a name="using-images-with-table-views"></a>使用具有資料表視圖的影像

若要將影像包含在中的儲存格 `NSTableView` ，您必須變更資料表視圖的方法傳回資料的方式， `NSTableViewDelegate's` `GetViewForItem` 以使用 `NSTableCellView` 而非一般 `NSTextField` 。 例如：

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

這裡有幾行需要注意。 首先，針對我們想要包含影像的資料行，我們會建立一個新 `NSImageView` 的必要大小和位置，也 `NSTextField` 會根據我們是否使用影像來建立新的並放置其預設位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我們需要在父系中包含新的影像視圖和文字欄位 `NSTableCellView` ：

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最後，我們需要告訴文字欄位可以用資料表視圖資料格來壓縮和成長：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

範例輸出︰

[![在應用程式中顯示影像的範例](image-images/tables01.png "在應用程式中顯示影像的範例")](image-images/tables01-large.png#lightbox)

如需有關使用資料表視圖的詳細資訊，請參閱我們的 [表格視圖](~/mac/user-interface/table-view.md) 檔。

<a name="Using_Images_with_Outline_Views"></a>

## <a name="using-images-with-outline-views"></a>使用具有大綱視圖的影像

若要將影像包含在中的儲存格 `NSOutlineView` ，您必須變更大綱視圖的方法傳回資料的方式， `NSTableViewDelegate's` `GetView` 以使用 `NSTableCellView` 而非一般 `NSTextField` 。 例如：

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

這裡有幾行需要注意。 首先，針對我們想要包含影像的資料行，我們會建立一個新 `NSImageView` 的必要大小和位置，也 `NSTextField` 會根據我們是否使用影像來建立新的並放置其預設位置：

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

其次，我們需要在父系中包含新的影像視圖和文字欄位 `NSTableCellView` ：

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

最後，我們需要告訴文字欄位可以用資料表視圖資料格來壓縮和成長：

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

範例輸出︰

[![顯示于大綱視圖中的影像範例](image-images/outline01.png "顯示于大綱視圖中的影像範例")](image-images/outline01-large.png#lightbox)

如需使用大綱視圖的詳細資訊，請參閱我們的 [大綱視圖](~/mac/user-interface/outline-view.md) 檔。

## <a name="summary"></a>摘要

本文詳細說明如何在 Xamarin 應用程式中使用影像和圖示。 我們看到各種不同的影像類型和用途、如何在 Xcode 的 Interface Builder 中使用影像和圖示，以及如何在 c # 程式碼中使用影像和圖示。

## <a name="related-links"></a>相關連結

- [MacImages (範例)](/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [資料表視圖](~/mac/user-interface/table-view.md)
- [大綱視圖](~/mac/user-interface/outline-view.md)
- [macOS X 人類介面指導方針](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [關於 OS X 的高解析度](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html) \(英文\)