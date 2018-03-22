---
title: "表格控制項"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: c55ba4fb90181aaa1aa8ec52e2fcb3e2b2cc76d0
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/22/2018
---
# <a name="table-control"></a>表格控制項

WatchOS`WKInterfaceTable`控制會比其 iOS 對應項目，更簡單，但會執行類似的角色。 它會建立可以具有自訂版面配置，而且其回應觸控事件的資料列捲動清單。

![](table-images/table-list-sml.png "監看式資料表清單") ![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>加入資料表

拖曳**資料表**至場景的控制項。 根據預設，它看起來像此 （顯示未指定的單一資料列版面配置）：

[![](table-images/add-table-sml.png "加入資料表")](table-images/add-table.png#lightbox)

資料表的名稱命名在**屬性**填補的**名稱**方塊，以便它可以參考程式碼中。

## <a name="add-a-row-controller"></a>新增資料列的控制站

資料表會自動包含單一資料列，所包含的資料列控制站表示**群組**預設控制項。

若要設定**類別**針對資料列控制站中，選取中的資料列**文件大綱**輸入中的類別名稱**屬性**板：

[![](table-images/add-row-controller-sml.png "輸入屬性輸入板中的類別名稱")](table-images/add-row-controller.png#lightbox)

一旦設定資料列的控制站的類別，IDE 就會在專案中建立對應的 C# 檔案。 控制項 （例如標籤） 上拖曳資料列，並提供名稱給它們，因此，可以參考這些程式碼中。




## <a name="create-and-populate-rows"></a>建立及擴展資料列

`SetNumberOfRows` 控制器類別建立的資料列，每個資料列，使用`Identifier`來選取正確。 如果您提供給您的資料列控制站自訂`Identifier`，變更**預設**下在程式碼片段至您所使用的識別項。 `RowController` *每個資料列*時，會建立`SetNumberOfRows`稱為和顯示的資料表。

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> 似乎可在 iOS 中，不是虛擬化資料表資料列。 嘗試限制 （Apple 建議小於 20） 的資料列數目。

一旦已建立的資料列，您必須填入每個資料格 (例如`GetCell`在 iOS 中一樣)。 從這個程式碼片段[WatchTables 範例](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)更新每個資料列中的標籤

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> 使用`SetNumberOfRows`和迴圈使用`GetRowController`會讓整個資料表傳送到監看式。 在後續的表格檢視，如果您要新增或移除特定的資料列使用`InsertRowsAt`和`RemoveRowsAt`以提升效能。


## <a name="respond-to-taps"></a>點選回應

您可以回應資料列選取兩個不同的方式：

- 實作`DidSelectRow`介面控制站上的方法或
- 在腳本上建立 segue 並實作`GetContextForSegue`如果您想要開啟另一個場景的資料列選取。

### <a name="didselectrow"></a>DidSelectRow

若要以程式設計方式處理資料列選取項目，實作`DidSelectRow`方法。 若要開啟新的場景，使用`PushController`將場景的識別項和要使用的資料內容傳遞：

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

將 segue 分鏡腳本上的資料從您的資料表資料列拖曳到另一個場景 (按住**控制項**拖曳指標時，索引鍵)。
務必選取 segue 並指定其識別碼**屬性**板 (例如`secondLevel`在下列範例中)。

介面控制站，在實作`GetContextForSegue`方法，並傳回資料內容，您應該提供至 segue 呈現的場景。

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

根據預設資料表控制項具有單一資料列類型，您可以設計。 若要加入更多資料列 '範本' 使用**列**方塊中**屬性**板建立多個資料列的控制站：

![](table-images/prototype-rows1.png "設定原型資料列數目")

設定**列**屬性**3**會建立額外的資料列讓您拖曳至控制項的預留位置。 對於每個資料列中，設定**類別**中**屬性**板，以確保資料列控制器類別會建立。

![](table-images/prototype-rows2.png "原型中的資料列在設計工具")

若要填入具有不同的資料列型別使用的資料表`SetRowTypes`方法，以指定要用於資料表中每個資料列的資料列控制站類型。 使用資料列的識別項來指定哪個資料列的控制站應該用於每個資料列。

此陣列中的元素數目必須符合您希望為資料表中的資料列數目：

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

擴展時具有多個資料列的控制站的資料表，您必須以追蹤您預期為您填入 UI 哪種類型：

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


## <a name="vertical-detail-paging"></a>垂直的詳細資料的分頁

watchOS 3 導入資料表的新功能： 能夠捲動詳細資料頁面與相關每個資料列，而不需要移回至資料表，然後選擇另一個資料列。 上下撥動或使用數位皇冠可捲動的詳細資料畫面。

![](table-images/table-scroll-sml.png "垂直分頁的詳細資料範例") ![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> 這項功能目前僅提供的藉由編輯 Xcode 介面產生器中的分鏡腳本。

若要啟用這項功能，請選取`WKInterfaceTable`在設計介面及其刻度**垂直詳細分頁**選項：

![](table-images/vertical-detail-paging-sml.png "選取 [垂直分頁的詳細資料] 選項")

做為[說明的 Apple](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023)必須使用資料表瀏覽 segues 分頁功能，即可運作。 重新撰寫現有的程式碼會使用`PushController`改為使用 segues。

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>附錄： 資料列控制器程式碼範例

在設計工具中建立的資料列的控制站時，IDE 會自動建立兩個程式碼檔案。 這些產生的檔案中的程式碼如下所示的參考。

第一個的名稱會是類別，例如**RowController.cs**，如下所示：

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

其他**.designer.cs**檔案是包含插座和所建立的設計工具介面上，例如此範例使用其中一個動作的部分類別定義`WKInterfaceLabel`控制項：

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

插座及此宣告的動作都可以參考程式碼-不過**.designer.cs**應該直接編輯檔案。



## <a name="related-links"></a>相關連結

- [WatchTables （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchTables/)
- [WatchKitCatalog （範例）](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Apple 的資料表文件](https://developer.apple.com/reference/watchkit/wkinterfacetable)
