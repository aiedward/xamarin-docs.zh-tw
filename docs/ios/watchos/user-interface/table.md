---
title: Xamarin 中的 watchOS Table 控制項
description: 本檔說明如何在 Xamarin 中使用 watchOS 資料表控制項。 它討論加入資料表、加入資料列控制器、建立和填入資料列、回應分和其他。
ms.prod: xamarin
ms.assetid: 7C14126D-9591-4387-A588-3C4521F11C55
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 079c72ee25f3b01c02b854a8a1eb258c885f2a96
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642803"
---
# <a name="watchos-table-controls-in-xamarin"></a>Xamarin 中的 watchOS Table 控制項

WatchOS `WKInterfaceTable`控制項比它的 iOS 對應更簡單, 但會執行類似的角色。 它會建立可具有自訂配置的資料列滾動清單, 並回應觸控事件。

![](table-images/table-list-sml.png "監看表清單")![](table-images/table-detail-sml.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="adding-a-table"></a>加入資料表

將 [**資料表**] 控制項拖曳到場景中。 根據預設, 它看起來會像這樣 (顯示單一未指定的資料列版面配置):

[![](table-images/add-table-sml.png "加入資料表")](table-images/add-table.png#lightbox)

在**Properties** pad 的 [**名稱**] 方塊中, 為數據表命名, 讓它可以在程式碼中參考。

## <a name="add-a-row-controller"></a>新增資料列控制器

資料表會自動包含單一資料列, 依預設包含**群組**控制項的資料列控制器表示。

若要設定資料列控制器的**類別**, 請在 [**檔大綱**] 中選取資料列, 然後在**Properties** pad 中輸入類別名稱:

[![](table-images/add-row-controller-sml.png "在 Properties pad 中輸入類別名稱")](table-images/add-row-controller.png#lightbox)

一旦設定了資料列控制器的類別, IDE 就會在專案中建立對應C#的檔案。 將控制項 (例如標籤) 拖曳到資料列上, 並為其命名, 讓它們可以在程式碼中參考。




## <a name="create-and-populate-rows"></a>建立和填入資料列

`SetNumberOfRows`建立每個資料列的資料列控制器類別, `Identifier`並使用來選取正確的。 如果您為數據列控制器提供自`Identifier`定義, 請將下列程式碼片段中的**預設值**變更為您所使用的識別碼。 當`RowController`呼叫並顯示資料表時`SetNumberOfRows` , 就會建立*每個資料列*的。

```csharp
myTable.SetNumberOfRows ((nint)rows.Count, "default");
        // loads row controller by identifier
```

> [!IMPORTANT]
> 資料表資料列不會像在 iOS 中一樣進行虛擬化。 請嘗試限制資料列數目 (Apple 建議少於20個)。

建立資料列之後, 您必須填入每個資料格 (就像`GetCell`在 iOS 中一樣)。 [WatchTables 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)中的這個程式碼片段會更新每個資料列中的標籤

```csharp
for (var i = 0; i < rows.Count; i++) {
    var elementRow = (RowController)myTable.GetRowController (i);
    elementRow.myRowLabel.SetText (rows [i]);
}
```

> [!IMPORTANT]
> 使用`SetNumberOfRows` , 然後透過使用`GetRowController`迴圈, 就會將整個資料表傳送到 watch。 在資料表的後續視圖中, 如果您需要加入或移除特定資料列, `InsertRowsAt`請`RemoveRowsAt`使用, 以獲得更好的效能。


## <a name="respond-to-taps"></a>回應點擊

您可以透過兩種不同的方式來回應資料列選取:

- 在介面控制器上執行方法,或`DidSelectRow`
- 如果您想要讓資料列選取`GetContextForSegue`範圍開啟另一個場景, 請在腳本上建立 segue 並進行執行。

### <a name="didselectrow"></a>DidSelectRow

若要以程式設計方式處理資料`DidSelectRow`列選取, 請執行方法。 若要開啟新的場景, `PushController`請使用並傳遞場景的識別碼和要使用的資料內容:

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

將分鏡腳本中的 segue 從您的資料表列拖曳至另一個場景 (拖曳時按住**控制**鍵)。
請務必選取 segue, 並在**Properties** pad 中提供其識別碼 (如`secondLevel`下列範例所示)。

在介面控制器中, 執行`GetContextForSegue`方法, 並傳回應該提供給 segue 所呈現之場景的資料內容。

```csharp
public override NSObject GetContextForSegue (string segueIdentifier, WKInterfaceTable table, nint rowIndex)
{
    if (segueIdentifier == "secondLevel") {
        return new NSString (rows[(int)rowIndex]);
    }
    return null;
}
```

這項資料會在其`Awake`方法中傳遞至目標分鏡腳本場景。

## <a name="multiple-row-types"></a>多個資料列類型

根據預設, 資料表控制項有一個您可以設計的單一資料列類型。 若要加入更多資料列的「範本」, 請使用**Properties** pad 中的 [資料**列**] 方塊來建立更多資料列控制器:

![](table-images/prototype-rows1.png "設定原型資料列的數目")

將**Rows**屬性設為**3** , 將會建立額外的資料列預留位置, 讓您將控制項拖曳到其中。 針對每個資料列, 在**Properties** pad 中設定**類別**名稱, 以確保會建立資料列控制器類別。

![](table-images/prototype-rows2.png "設計工具中的原型資料列")

若要填入具有不同資料列類型的資料表`SetRowTypes` , 請使用方法來指定要用於資料表中每個資料列的資料列控制器類型。 使用資料列的識別碼, 指定每個資料列應該使用的資料列控制器。

此陣列中的元素數目應符合您預期在資料表中的資料列數目:

```csharp
myTable.SetRowTypes (new [] {"type1", "default", "default", "type2", "default"});
```

當您以多個資料列控制器填入資料表時, 您必須在擴展 UI 時追蹤預期的類型:

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

watchOS 3 為數據表引進了一項新功能: 能夠逐一查看與每個資料列相關的詳細頁面, 而不需要回到資料表並選擇另一個資料列。 詳細資料畫面可以透過向上和向下或使用 Digital Crown 來滾動。

![](table-images/table-scroll-sml.png "垂直詳細資料分頁範例")![](table-images/table-detail-sml.png)

> [!IMPORTANT]
> 這項功能目前僅適用于在 Xcode Interface Builder 中編輯分鏡腳本。

若要啟用這項功能, `WKInterfaceTable`請選取設計介面上的, 並勾選 [**垂直詳細資料分頁**] 選項:

![](table-images/vertical-detail-paging-sml.png "選取垂直詳細資料分頁選項")

如[Apple 所述](https://developer.apple.com/reference/watchkit/wkinterfacetable#1682023), 資料表導覽必須使用 segue, 才能讓分頁功能正常執行。 請重新撰寫任何現有的程式碼`PushController` , 使用來改用 segue。

<a name="add_row_controller" />

## <a name="appendix-row-controller-code-example"></a>附錄：資料列控制器程式碼範例

在設計工具中建立資料列控制器時, IDE 會自動建立兩個程式碼檔案。 以下顯示這些產生檔案中的程式碼以供參考。

系統會為類別命名第一個, 例如**RowController.cs**, 如下所示:

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

另一個**designer.cs**檔案是部分類別定義, 其中包含在設計工具介面上建立的輸出和動作, 例如具有一個`WKInterfaceLabel`控制項的此範例:

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

接著可以在程式碼中參考此處所宣告的輸出和動作-不過, 不應直接編輯**designer.cs**檔案。



## <a name="related-links"></a>相關連結

- [WatchTables （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables)
- [WatchKitCatalog （範例）](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Apple 的資料表檔](https://developer.apple.com/reference/watchkit/wkinterfacetable)
