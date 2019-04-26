---
title: 將拖放在 Xamarin.iOS 中
description: 本文件說明如何實作拖放和卸除在使用 iOS 11 中導入的 Api 的 Xamarin.iOS 應用程式中。 特別是，它會討論啟用拖放 UITableView。
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/05/2017
ms.openlocfilehash: aa93e015a399e733a2bb52f087a1e482bc23a00a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61169631"
---
# <a name="drag-and-drop-in-xamarinios"></a>將拖放在 Xamarin.iOS 中

_實作適用於 iOS 11 功能_

iOS 11 包含拖曳和卸除在 iPad 上的應用程式之間複製資料的支援。 使用者可以選取，並將所有類型的內容，從放置的應用程式的並存，，或藉由拖曳 [應用程式] 圖示，其會觸發此應用程式開啟，以及允許卸除的資料：

![拖放到資訊應用程式的自訂應用程式範例](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> 拖曳和置放才可用在 iPhone 上的相同應用程式。

請考慮支援拖曳和卸除的作業任何地方建立或編輯內容：

- 文字控制項支援拖放，建置 iOS 11，而不需要任何其他工作的所有應用程式。
- 資料表檢視 」 和 「 集合檢視包含增強功能，在 iOS 11 可簡化將拖曳並拖放行為。
- 任何其他檢視中可以對支援拖曳和卸除與其他自訂。

當新增拖曳和置放支援您的應用程式時，您可以提供不同精確度層級的內容;例如，您可能會提供格式化的文字和純文字版的資料，讓其符合至拖曳目標的最佳選擇接收應用程式。 它也是可以自訂拖曳視覺效果，並啟用一次將多個項目。

## <a name="drag-and-drop-with-text-controls"></a>將拖放文字控制項

`UITextView` 和`UITextField`會自動支援 將選取的文字，拖放文字中的內容。

<a name="uitableview" />

## <a name="drag-and-drop-with-uitableview"></a>將拖放與 UITableView

`UITableView` 有內建處理拖曳和卸除資料表的資料列，要求只有幾個方法，以啟用預設的行為與互動。

有相關的兩個介面：

- `IUITableViewDragDelegate` – [資料表] 檢視中起始拖放時，就會封裝資訊。
- `IUITableViewDropDelegate` – 處理資訊時卸除正在嘗試進行已完成。

在  [DragAndDropTableView 範例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)這兩個介面都實作上`UITableViewController`類別，以及委派和資料來源。 在其指派`ViewDidLoad`方法：

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

這兩個介面的最小必要的程式碼會如下所述。

### <a name="table-view-drag-delegate"></a>資料表檢視拖曳委派

唯一的方法_必要_若要支援從資料表檢視中拖曳資料列`GetItemsForBeginningDragSession`。 如果使用者開始拖曳的資料列時，就會呼叫這個方法。

實作如下所示。 擷取拖曳的資料列相關聯的資料、 進行編碼，並設定`NSItemProvider`可判斷應用程式將如何處理作業的"drop"部分 (比方說，不論它們能夠處理的資料類型、 `PlainText`，在範例中):

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

可自訂的拖放行為，例如提供多個資料表示法可所採取的優點在目標應用程式中實作拖放在委派上有許多選擇性方法 (例如純文字，以及為向量的格式化文字和點陣圖版本的繪圖）。 您也可以提供拖放在相同應用程式時要使用的自訂資料表示法。

### <a name="table-view-drop-delegate"></a>資料表檢視卸除委派

在拖曳作業會透過資料表檢視中，或完成其上方時，會呼叫置放在委派上的方法。 所需的方法會決定是否要卸除，允許的資料，以及要採取哪些動作，如果卸除已完成：

- `CanHandleDropSession` – 雖然拖曳正在進行中，並可能正在卸除應用程式，這個方法會判斷所拖曳的資料是否可卸除。
- `DropSessionDidUpdate` – 拖曳正在進行時，會呼叫這個方法來判斷哪些動作的用途。 資料表檢視拖曳到橢圓形上，拖曳工作階段中，與可能的索引路徑的資訊所有可用來判斷視覺化回饋提供給使用者的行為。
- `PerformDrop` – 當使用者完成拖放 （藉由提升他們的手指），這個方法會擷取所拖曳的資料，並修改的資料表檢視，以加入新的資料列 （或資料列） 中的資料。

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` 指出資料表檢視表是否可接受拖曳的資料。 在此程式碼片段中，`CanLoadObjects`用來確認此資料表檢視，可以接受字串資料。

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

`DropSessionDidUpdate`拖曳作業正在進行，以提供視覺提示給使用者時重複呼叫方法。

在下面的程式碼中`HasActiveDrag`用來判斷作業是否源自目前的資料表檢視中。 若是如此，只有單一資料列可以移動。
如果拖曳另一個來源，將會指出複製作業：

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

卸除意圖可以插入新的資料列，或新增/附加至現有的資料列的資料。

#### <a name="performdrop"></a>PerformDrop

`PerformDrop`使用者完成作業，並修改以反映卸除的資料的資料表檢視和資料來源時，會呼叫方法。

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

可以新增其他程式碼，以非同步方式載入大型資料物件。

### <a name="testing-drag-and-drop"></a>測試拖曳和置放

您必須用來測試的 iPad[範例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)。
開啟另一個應用程式 （例如附註） 與範例，並拖曳它們之間的資料列和文字：

![拖放作業正在進行中的螢幕擷取畫面](drag-and-drop-images/01-sml.png)


## <a name="related-links"></a>相關連結

- [將拖放人性化介面指導方針 (Apple)](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [將拖放資料表檢視範例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropTableView/)
- [將拖放集合檢視範例](https://developer.xamarin.com/samples/monotouch/ios11/DragAndDropCollectionView)
- [拖曳和卸除 (WWDC) （影片） 簡介](https://developer.apple.com/videos/play/wwdc2017/203/)
- [將拖放與集合和資料表檢視 (WWDC) （影片）](https://developer.apple.com/videos/play/wwdc2017/223/)
