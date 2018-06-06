---
title: 拖放 Xamarin.iOS
description: 本文件說明如何實作拖曳和卸除使用中 iOS 11 導入的 Api Xamarin.iOS 應用程式中。 特別是，討論啟用拖放 UITableView。
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/05/2016
ms.openlocfilehash: 7c41f96dae88047e64ec1e74838e3efab55958cc
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34786960"
---
# <a name="drag-and-drop-in-xamarinios"></a>拖放 Xamarin.iOS

_實作拖放 ios 11_

iOS 11 包含拖曳和卸除來在 iPad 上的應用程式之間複製資料的支援。 使用者可以選取並拖曳所有類型的內容，從應用程式位於-並存，或藉由拖曳到 [應用程式] 圖示，其會觸發應用程式開啟，並允許資料被卸除：

![拖放入備忘稿應用程式的自訂應用程式範例](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> 拖放才可以在 iPhone 上的相同應用程式中使用。

考慮支援拖曳和卸除的作業任何位置建立或編輯內容：

- 文字控制項支援拖放，針對 iOS 11 中，而無需任何額外的工作所建立的所有應用程式。
- 資料表檢視與集合檢視併入 iOS 11，簡化新增拖曳和卸除行為中的增強功能。
- 任何其他檢視可能對支援拖曳和卸除與其他自訂。

當加入儲存拖放支援應用程式時，您可以提供不同層級的內容的精確度。例如，您可能會提供格式化的文字和純文字版本的資料，讓接收應用程式可以選擇這最適合拖曳目標。 它也可自訂的拖曳視覺效果，以及啟用一次拖放多個項目。

## <a name="drag-and-drop-with-text-controls"></a>將拖放文字控制項

`UITextView` 和`UITextField`會自動支援將選取的文字，拖曳和卸除文字中的內容。

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>將拖放與 UITableView

`UITableView` 有內建處理的拖曳和卸除資料表的資料列，要求只有一些方法，讓預設行為與互動。

有相關的兩個介面：

- `IUITableViewDragDelegate` – 拖曳資料表檢視中起始時，就會封裝資訊。
- `IUITableViewDropDelegate` – 處理資訊，則卸除時嘗試和已完成。

在[DragAndDropTableView 範例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)這兩個介面兩者實作上`UITableViewController`類別，以及委派和資料來源。 依指定在`ViewDidLoad`方法：

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

這兩個介面的最小必要的程式碼會如下所述。

### <a name="table-view-drag-delegate"></a>資料表檢視拖曳委派

唯一的方法_必要_支援從資料表檢視中拖曳資料列是`GetItemsForBeginningDragSession`。 如果使用者開始拖曳一個資料列時，就會呼叫這個方法。

實作如下所示。 它會擷取與拖曳的資料列相關聯的資料，編碼，以及設定`NSItemProvider`可判斷應用程式如何處理 「 置放 」 作業的一部分 (比方說，是否可以處理的資料類型、 `PlainText`，在範例中):

```csharp
public UIDragItem[] GetItemsForBeginningDragSession (UITableView tableView,
  IUIDragSession session, NSIndexPath indexPath)
{
  // gets the 'information' to be dragged
  var placeName = model.PlaceNames[indexPath.Row];
  // convert to NSData representation
  var data = NSData.FromString(placeName, NSStringEncoding.UTF8);
  // create an NSItemProvider to describe the data
  var itemProvider = new NSItemProvider();
  itemProvider.RegisterDataRepresentation(UTType.PlainText,
                                NSItemProviderRepresentationVisibility.All,
                                (completion) =>
  {
    completion(data, null);
    return null;
  });
  // wrap in a UIDragItem
  return new UIDragItem[] { new UIDragItem(itemProvider) };
}
```

可以實作以自訂拖放到行為，例如提供多個資料表示，這可以採用優點的應用程式為目標的拖曳委派上有許多選擇性方法 (例如格式化的文字，做為純文字格式或向量和點陣圖的版本繪圖）。 您也可以提供拖放在相同的應用程式時所要使用的自訂資料表示法。

### <a name="table-view-drop-delegate"></a>資料表檢視卸除委派

卸除在委派上的這些方法會呼叫在拖曳作業會透過資料表檢視中，或完成其上方時。 需要的方法會決定是否允許要卸除的資料，以及如果卸除已完成，會採取什麼動作：

- `CanHandleDropSession` – 當拖曳進行中，且可能正在卸除應用程式上，這個方法會決定是否要卸除允許正在拖曳的資料。
- `DropSessionDidUpdate` – 拖曳正在進行時，會呼叫這個方法來判斷哪些動作適。 從 [資料表] 檢視拖曳到橢圓形上、 拖放到工作階段和可能的索引路徑的資訊所有可用來判斷的行為與提供給使用者的視覺化回饋。
- `PerformDrop` – 使用者完成下拉式清單 （依提升其手指），這個方法會擷取正在拖曳的資料，並修改，將資料加入新的資料列 （或資料列） 中的資料表檢視。

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` 指出資料表檢視表是否可接受正在拖曳的資料。 在此程式碼片段中，`CanLoadObjects`用來確認此資料表檢視表可以接受字串資料。

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

`DropSessionDidUpdate`拖曳作業正在進行，以提供視覺提示給使用者時重複呼叫方法。

下列程式碼`HasActiveDrag`用來判斷作業是否產生目前的資料表檢視中。 若是如此，只有單一資料列可以移動。
如果拖曳是從其他來源，會指出複製作業：

```csharp
public UITableViewDropProposal DropSessionDidUpdate(UITableView tableView, IUIDropSession session, NSIndexPath destinationIndexPath)
{
  // The UIDropOperation.Move operation is available only for dragging within a single app.
  if (tableView.HasActiveDrag)
  {
    if (session.Items.Length > 1)
    {
        return new UITableViewDropProposal(UIDropOperation.Cancel);
    } else {
        return new UITableViewDropProposal(UIDropOperation.Move, UITableViewDropIntent.InsertAtDestinationIndexPath);
    }
  } else {
    return new UITableViewDropProposal(UIDropOperation.Copy, UITableViewDropIntent.InsertAtDestinationIndexPath);
  }
}
```

卸除作業可以是其中一個`Cancel`， `Move`，或`Copy`。

卸除意圖可以是插入新資料列，或加入/附加至現有的資料列的資料。

#### <a name="performdrop"></a>PerformDrop

`PerformDrop`使用者完成作業，然後修改以反映已卸除的資料的資料表檢視和資料來源時，呼叫方法。

```csharp
public void PerformDrop(UITableView tableView, IUITableViewDropCoordinator coordinator)
{
  NSIndexPath indexPath, destinationIndexPath;
  if (coordinator.DestinationIndexPath != null)
  {
    indexPath = coordinator.DestinationIndexPath;
    destinationIndexPath = indexPath;
  }
  else
  {
    // Get last index path of table view
    var section = tableView.NumberOfSections() - 1;
    var row = tableView.NumberOfRowsInSection(section);
    destinationIndexPath = NSIndexPath.FromRowSection(row, section);
  }
  coordinator.Session.LoadObjects(typeof(NSString), (items) =>
  {
    // Consume drag items
    List<string> stringItems = new List<string>();
    foreach (var i in items)
    {
      var q = NSString.FromHandle(i.Handle);
      stringItems.Add(q.ToString());
    }
    var indexPaths = new List<NSIndexPath>();
    for (var j = 0; j < stringItems.Count; j++)
    {
      var indexPath1 = NSIndexPath.FromRowSection(destinationIndexPath.Row + j, destinationIndexPath.Section);
      model.AddItem(stringItems[j], indexPath1.Row);
      indexPaths.Add(indexPath1);
    }
    tableView.InsertRows(indexPaths.ToArray(), UITableViewRowAnimation.Automatic);
  });
}
```

可以加入額外的程式碼，以非同步方式載入大量資料的物件。

### <a name="testing-drag-and-drop"></a>測試拖曳和卸除

您必須使用要測試的 iPad[範例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)。
開啟另一個應用程式 （例如，附註） 與範例，並拖曳它們之間的資料列和文字：

![拖曳作業正在進行中的螢幕擷取畫面](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>相關連結

- [拖曳和卸除人性化介面指導方針 (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [拖曳和卸除資料表檢視範例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)
- [拖曳和卸除集合檢視範例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
- [拖曳和卸除 (WWDC) （影片） 簡介](https://developer.apple.com/videos/play/wwdc2017/203/)
- [將拖放與集合和資料表檢視 (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/223/)
