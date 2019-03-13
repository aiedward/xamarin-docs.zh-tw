---
title: watchOS 在 Xamarin 中的資料表控制項
description: 本文件說明如何在 Xamarin 中使用 watchOS 表格控制項。 它討論加入資料表、 新增資料列的控制站、 建立和填入資料列，回應點選，等等。
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: cd5e7299874bbfb1b652315a549b9d067d58e9a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109389"
---
# <a name="watchos-table-controls-in-xamarin"></a>watchOS 在 Xamarin 中的資料表控制項

WatchOS`WKInterfaceTable`控制項其 iOS 對應項目，比簡單多了，但是會執行類似的角色。 它會建立可自訂的版面配置，且其回應觸控事件的資料列的捲動清單。

![](table-images/table-list-sml.png "監看式 資料表清單") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>加入資料表

拖曳**資料表**場景的控制項。 根據預設，它看起來像這個 （顯示未指定的單一資料列版面配置）：

[![](table-images/add-table-sml.png "加入資料表")](table-images/add-table.png#lightbox)

提供資料表名稱**屬性**板**名稱**方塊，以便它可以參考程式碼中。

## <a name="add-a-row-controller"></a>新增資料列控制站

資料表會自動包含單一資料列，表示所包含的資料列控制站**群組**預設的控制項。

若要設定**類別**的資料列控制站中，選取中的資料列**文件大綱**輸入中的類別名稱**屬性**板：

[![](table-images/add-row-controller-sml.png "在 [屬性] 面板中輸入類別名稱")](table-images/add-row-controller.png#lightbox)

一旦設定資料列的控制站的類別，IDE 會建立對應C#專案中的檔案。 拖曳資料列中的控制項 （例如標籤），並為它們提供名稱，因此它們可以參考程式碼中。




## <a name="create-and-populate-rows"></a>建立和填入資料列

`SetNumberOfRows` 會建立資料列控制器類別，針對每個資料列，使用`Identifier`選取正確的密碼。 如果您提供給您的資料列控制站的自訂`Identifier`，變更**預設**中您所用的識別項程式碼片段如下。 `RowController` *每個資料列*時，會建立`SetNumberOfRows`稱為和顯示的資料表。

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> 像它們是在 iOS 中，不會模擬化資料表的資料列。 嘗試限制 （Apple 建議使用小於 20） 的資料列數目。

當資料列已建立之後時，您需要填入每個資料格 (例如`GetCell`在 iOS 中一樣)。 此程式碼片段[WatchTables 範例](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)更新每個資料列中的標籤

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> 使用`SetNumberOfRows`，然後使用`GetRowController`會導致整個資料表要傳送至監看式。 在後續的表格檢視，如果您要新增或移除特定的資料列使用`InsertRowsAt`和`RemoveRowsAt`以提升效能。


## <a name="respond-to-taps"></a>點選回應

您可以在兩個不同的方式回應資料列選取範圍：

- 實作`DidSelectRow`介面控制站上的方法或
- 建立分鏡腳本 segue，並實作`GetContextForSegue`如果您想要的資料列選取範圍，以開啟另一個場景。

### <a name="didselectrow"></a>DidSelectRow

若要以程式設計方式處理資料列選取範圍，實作`DidSelectRow`方法。 若要開啟新場景，請使用`PushController`並傳遞場景的識別項和要使用的資料內容：

```csharp
public override void DidSelectRow (WKInterfaceTable table, nint rowIndex)
{
    var rowData = rows [(int)rowIndex];
    Console.WriteLine ("Row selected:" + rowData);
    // if selection should open a new scene
    PushController ("secondInterface", rows[(int)rowIndex]);
}
```

### <a name="getcontextforsegue"></a>GetContextForSegue

將 segue 分鏡腳本上的資料從您的資料表資料列拖曳至另一個場景 (按住**控制**鍵同時拖曳)。
務必選取 segue 並指定其識別碼，**屬性**填補 (例如`secondLevel`在下列範例中)。

在介面控制器中，實作`GetContextForSegue`方法，並傳回資料內容，應提供至 segue 所呈現的場景。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

這項資料會傳遞至目標分鏡腳本場景中其`Awake`方法。

## <a name="multiple-row-types"></a>多個資料列型別

根據預設資料表控制項具有單一資料列型別，您可以設計。 若要新增更多資料列 '範本' 使用**資料列**方塊中**屬性**pad 將建立更多的資料列控制站：

![](table-images/prototype-rows1.png "設定原型的資料列數目")

設定**資料列**屬性設**3**會建立額外的資料列預留位置，讓您拖曳到控制項。 針對每個資料列中，設定**類別**名稱**屬性**板，以確保在建立資料列控制器類別。

![](table-images/prototype-rows2.png "原型中的資料列的設計工具")

要填入資料表不同的資料列型別使用`SetRowTypes`方法，以指定要用於資料表中的每個資料列的資料列控制站類型。 您可以使用 資料列的識別項來指定應該使用哪一個資料列控制站，每個資料列。

此陣列中的項目數應符合您預期要在資料表中的資料列數目：

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

填入具有多個資料列控制站的資料表，當您要追蹤的希望為您填入 UI 哪種類型：

```csharp
for (var i = 0; i < rows.Count; i++) {
    if (i == 0) {
        var elementRow = (Type1RowController)myTable.GetRowController (i);
        // populate UI controls
    } else if (i == 3) {
        var elementRow = (Type2RowController)myTable.GetRowController (i);
        // populate UI controls
    } else {
        var elementRow = (DefaultRowController)myTable.GetRowController (i);
        // populate UI controls
    }
}
```


## <a name="vertical-detail-paging"></a>垂直的詳細資料分頁

watchOS 3 導入資料表的新功能： 能夠捲動以查看詳細資料頁面與每個資料列，而不需移回至資料表，並選擇另一個資料列。 向上和向下撥動，或使用數位皇冠可捲動的詳細資料畫面。

![](table-images/table-scroll-sml.png "垂直詳細資料分頁的範例") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> 這項功能目前僅提供的編輯在 Xcode Interface Builder 分鏡腳本。

若要啟用這項功能，請選取`WKInterfaceTable`設計介面和刻度**垂直詳細資料分頁**選項：

![](table-images/vertical-detail-paging-sml.png "選取 [垂直分頁的詳細資料] 選項")

作為[說明 apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023)資料表瀏覽必須使用 segue 的分頁功能運作。 重新撰寫現有的程式碼會使用`PushController`改為使用 segue。

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>附錄： 資料列控制器程式碼範例

在設計工具中建立的資料列控制站時，IDE 會自動建立兩個程式碼檔案。 這些產生的檔案中的程式碼如下所示的參考。

第一個將會命名為類別，例如**RowController.cs**，如下所示：

```csharp
using System;
using Foundation;

namespace WatchTablesExtension
{
    public partial class RowController : NSObject
    {
        public RowController ()
        {
        }
    }
}
```

另 **。 designer.cs**檔案會包含輸出 」 和 「 動作所建立的設計工具介面上，例如此範例中使用其中的部分類別定義`WKInterfaceLabel`控制項：

```csharp
using Foundation;
using System;
using System.CodeDom.Compiler;
using UIKit;

namespace WatchTables.OnWatchExtension
{
    [Register ("RowController")]
    partial class RowController
    {
        [Outlet]
        [GeneratedCode ("iOS Designer", "1.0")]
        public WatchKit.WKInterfaceLabel MyLabel { get; set; }

        void ReleaseDesignerOutlets ()
        {
            if (MyLabel != null) {
                MyLabel.Dispose ();
                MyLabel = null;
            }
        }
    }
}
```

「 輸出 」 和此宣告的動作都可以參考中的程式碼-不過 **。 designer.cs**不可直接編輯檔案。



## <a name="related-links"></a>相關連結

- [WatchTables （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 的表格文件](https://developer.apple.com/reference/watchkit/wkinterfacetable)
