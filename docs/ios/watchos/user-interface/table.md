---
title: Xamarin 中的 watchOS 資料表控制項
description: 本檔說明如何在 Xamarin 中使用 watchOS 資料表控制項。 它討論加入資料表、新增資料列控制器、建立和擴展資料列、回應點擊，以及其他更多。
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 21154ee5ed83d9d6af2c5d5f70bb64759de43137
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431508"
---
# <a name="watchos-table-controls-in-xamarin"></a>Xamarin 中的 watchOS 資料表控制項

WatchOS `WKInterfaceTable` 控制項比 iOS 對應項更簡單，但是會執行類似的角色。 它會建立可擁有自訂配置以及回應觸控事件的資料列滾動清單。

![監看資料表清單 ](table-images/table-list-sml.png) ![ 監看資料表詳細資料](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>新增資料表

將 [ **資料表** ] 控制項拖曳到場景中。 依預設，它看起來會像這樣 (顯示單一未指定的資料列版面配置) ：

[![加入資料表](table-images/add-table-sml.png)](table-images/add-table.png#lightbox)

在 [ **屬性** ] 面板的 [ **名稱** ] 方塊中為數據表命名，讓它可以在程式碼中參考。

## <a name="add-a-row-controller"></a>新增資料列控制器

資料表會自動包含單一資料列，該資料列會依預設包含 **群組** 控制項的資料列控制器表示。

若要設定資料列控制器的 **類別** ，請在 [ **檔大綱** ] 中選取資料列，然後在 [ **屬性** ] 面板中輸入類別名稱：

[![在 Properties pad 中輸入類別名稱](table-images/add-row-controller-sml.png)](table-images/add-row-controller.png#lightbox)

設定資料列控制器的類別之後，IDE 會在專案中建立對應的 c # 檔案。 將控制項 (例如) 的標籤拖曳至資料列，並為其命名，使其可在程式碼中參考。

## <a name="create-and-populate-rows"></a>建立和填入資料列

`SetNumberOfRows` 使用選取正確的資料列，建立每個資料列的資料列控制器類別 `Identifier` 。 如果您為您的資料列控制器提供自訂 `Identifier` ，請將下列程式碼片段中的 **預設值** 變更為您所使用的識別碼。 `RowController`當呼叫和顯示資料表時，會建立*每個資料列*的 `SetNumberOfRows` 。

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
    // loads row controller by identifier
```

> [!IMPORTANT]
> 資料表資料列不像在 iOS 中一樣虛擬化。 嘗試限制 (Apple 建議少於 20) 的資料列數目。

一旦建立資料列之後，您就必須將每個資料格 (填入，就像 `GetCell` 在 iOS) 中所做的一樣。 [WatchTables 範例](/samples/xamarin/ios-samples/watchos-watchtables)中的這個程式碼片段會更新每個資料列中的標籤

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> 使用 `SetNumberOfRows` 然後迴圈使用， `GetRowController` 會導致整個資料表傳送至監看式。 如果您需要加入或移除特定的資料列，請在資料表的後續視圖中使用， `InsertRowsAt` `RemoveRowsAt` 以獲得更好的效能。

## <a name="respond-to-taps"></a>回應點擊

您可以透過兩種不同的方式來回應資料列選取：

- `DidSelectRow`在介面控制器上執行方法，或
- 在腳本中建立 segue，並在 `GetContextForSegue` 您想要選取資料列來開啟另一個場景時執行。

### <a name="didselectrow"></a>DidSelectRow

若要以程式設計方式處理資料列選取，請執行 `DidSelectRow` 方法。 若要開啟新的場景，請使用 `PushController` 並傳遞場景的識別碼和要使用的資料內容：

```csharp
public override void DidSelectRow (WKInterfaceTable table, nint rowIndex)
{
    var rowData = rows [(int)rowIndex];
    Console.WriteLine ("Row selected:" + rowData);
    // if selection should open a new scene
    PushController ("secondInterface", rows[(int)rowIndex]);
}
```

### <a name="getcontextforsegue"></a>GetCoNtextForSegue

從您的資料表資料列將 segue 拖曳到另一個場景 (按住 **ctrl** 鍵，同時拖曳) 。
請務必選取 [segue]，並在 [ **屬性** ] 面板中提供其識別碼 (例如 `secondLevel` 下列範例) 。

在介面控制器中，請執行 `GetContextForSegue` 方法，並傳回應提供給 segue 所呈現場景的資料內容。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

這項資料會以其方法傳遞給目標分鏡腳本場景 `Awake` 。

## <a name="multiple-row-types"></a>多個資料列類型

根據預設，table 控制項有您可以設計的單一資料列類型。 若要加入更多資料列 [範本]，請使用**屬性**面板中的 [資料**列**] 方塊來建立更多資料列控制器：

![設定原型資料列的數目](table-images/prototype-rows1.png)

將 **Rows** 屬性設定為 **3** 將會建立額外的資料列預留位置，讓您將控制項拖曳到其中。 針對每個資料列，在 [**屬性**] 面板中設定**類別**名稱，以確保已建立資料列控制器類別。

![設計工具中的原型資料列](table-images/prototype-rows2.png)

若要填入具有不同資料列類型的資料表，請使用 `SetRowTypes` 方法來指定要針對資料表中的每個資料列使用的資料列控制器類型。 使用資料列的識別碼來指定每個資料列應使用的資料列控制器。

此陣列中的元素數目應符合您預期在資料表中的資料列數目：

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

使用多個資料列控制器擴展資料表時，您必須在填入 UI 時追蹤預期的類型：

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

## <a name="vertical-detail-paging"></a>垂直詳細資料分頁

watchOS 3 引進了資料表的新功能：能夠逐一查看與每個資料列相關的詳細資料頁面，而不需要回到資料表，然後選擇另一個資料列。 詳細資料畫面可透過向上或向下輕量，或使用 Digital Crown 來進行滾動。

![垂直詳細資料分頁範例](table-images/table-scroll-sml.png) ![垂直分頁詳細資料](table-images/table-detail-sml.png)

> [!IMPORTANT]
> 這項功能目前僅可透過編輯 Xcode Interface Builder 中的分鏡腳本來使用。

若要啟用這項功能，請 `WKInterfaceTable` 在設計介面上選取，並勾選 **垂直詳細資料分頁** 選項：

![選取垂直詳細資料分頁選項](table-images/vertical-detail-paging-sml.png)

如 [Apple 所述](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023) ，資料表導覽必須使用 segue，分頁功能才能運作。 重新撰寫任何使用來使用 segue 的現有程式碼 `PushController` 。

<a name="add_row_controller"></a>

## <a name="appendix-row-controller-code-example"></a>附錄：資料列控制器程式碼範例

在設計工具中建立資料列控制器時，IDE 會自動建立兩個程式碼檔案。 以下顯示這些所產生檔案中的程式碼，以供參考。

第一個會針對類別命名，例如 **RowController.cs**，如下所示：

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

**Designer.cs**檔案是部分類別定義，其中包含在設計工具介面上建立的輸出和動作，例如具有一個控制項的此範例 `WKInterfaceLabel` ：

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

您可以在程式碼中參考此處所宣告的輸出和動作，但不應該直接編輯 **designer.cs** 檔案。

## <a name="related-links"></a>相關連結

- [WatchTables (範例) ](/samples/xamarin/ios-samples/watchos-watchtables)
- [WatchKitCatalog (範例) ](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的資料表檔](https://developer.apple.com/reference/watchkit/wkinterfacetable)