---
title: 在 Xamarin 中拖放
description: 本檔說明如何使用 iOS 11 中引進的 Api，在 Xamarin iOS 應用程式中執行拖放功能。 特別是，它會討論如何在 UITableView 中啟用拖放功能。
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/05/2017
ms.openlocfilehash: 2a59040efde59210152ca20b44df2a097904c9f9
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573751"
---
# <a name="drag-and-drop-in-xamarinios"></a>在 Xamarin 中拖放

_執行 iOS 11 的拖放_

iOS 11 包含拖放支援，可在 iPad 上的應用程式之間複製資料。 使用者可以從位於並排的應用程式中選取並拖曳所有類型的內容，或是藉由拖曳應用程式圖示來將觸發應用程式開啟並允許卸載資料：

![將自訂應用程式中的範例拖放到 Notes 應用程式](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> 只有在 iPhone 上的相同應用程式中才能使用拖放功能。

請考慮支援可建立或編輯內容的任何位置的拖放作業：

- 文字控制項支援針對 iOS 11 所建立之所有應用程式的拖放功能，而不需要任何額外的工作。
- 資料表視圖和集合視圖包括 iOS 11 中的增強功能，可簡化拖放行為的加入。
- 任何其他的視圖都可以進行其他自訂，以支援拖放作業。

將拖放支援新增至您的應用程式時，您可以提供不同層級的內容精確度;例如，您可以同時提供格式化的文字和純文字版本的資料，讓接收的應用程式可以選擇哪一個最適合用於拖曳目標。 也可以自訂拖曳視覺效果，同時啟用一次拖曳多個專案。

## <a name="drag-and-drop-with-text-controls"></a>使用文字控制項拖放

`UITextView`和 `UITextField` 會自動支援拖曳選取的文字，並在中放置文字內容。

<a name="uitableview"></a>

## <a name="drag-and-drop-with-uitableview"></a>使用 UITableView 拖放

`UITableView`具有與資料表資料列之拖放互動的內建處理，只需要幾個方法，即可啟用預設行為。

其中包含兩個介面：

- `IUITableViewDragDelegate`–在資料表視圖中起始拖曳時封裝資訊。
- `IUITableViewDropDelegate`–在嘗試和完成 drop 時處理資訊。

在[DragAndDropTableView 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview)中，這兩個介面同時在類別上執行 `UITableViewController` ，以及委派和資料來源。 它們是在方法中指派 `ViewDidLoad` ：

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

以下說明這兩個介面的最低必要程式碼。

### <a name="table-view-drag-delegate"></a>資料表視圖拖曳委派

支援從資料表視圖拖曳資料列的_唯一方法是_ `GetItemsForBeginningDragSession` 。 如果使用者開始拖曳資料列，就會呼叫這個方法。

執行如下所示。 它會抓取與拖曳的資料列相關聯的資料、將其編碼，並設定， `NSItemProvider` 以決定應用程式如何處理作業的「卸載」部分（例如，它們是否可以 `PlainText` 在範例中處理資料類型）：

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

可以執行的拖曳委派上有許多選擇性的方法，以自訂拖曳行為，例如提供多個可在目標應用程式中利用的資料標記法（例如格式化的文字和純文字，或是繪圖的向量和點陣圖版本）。 您也可以提供自訂資料標記法，以在相同應用程式內拖放時使用。

### <a name="table-view-drop-delegate"></a>資料表視圖卸載委派

當拖曳作業在資料表視圖上發生或在其上方完成時，會呼叫 drop 委派上的方法。 必要的方法會決定是否允許卸載資料，以及當卸載完成時，會採取哪些動作：

- `CanHandleDropSession`–當拖曳正在進行中，而且可能會在應用程式上卸載時，這個方法會決定是否允許卸載所拖曳的資料。
- `DropSessionDidUpdate`–正在進行拖曳時，會呼叫這個方法來決定要執行的動作。 資料表視圖中的資訊會拖曳到拖放上、拖曳會話和可能的索引路徑，全都用來判斷提供給使用者的行為和視覺效果意見反應。
- `PerformDrop`–當使用者完成卸載（藉由將其手指放）時，這個方法會解壓縮所拖曳的資料，並修改資料表視圖，以便在新的資料列（或列）中加入資料。

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession`指出資料表視圖是否可以接受所拖曳的資料。 在此程式碼片段中， `CanLoadObjects` 是用來確認此資料表視圖可以接受字串資料。

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

`DropSessionDidUpdate`當拖曳作業正在進行時，會重複呼叫方法，以提供視覺提示給使用者。

在下列程式碼中， `HasActiveDrag` 是用來判斷作業是否源自于目前資料表的視圖。 若是如此，則只允許移動單一資料列。
如果從另一個來源拖曳，則會顯示覆製作業：

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

Drop 作業可以是 `Cancel` 、或其中一個 `Move` `Copy` 。

Drop 意圖可以是插入新的資料列，或將資料加入或附加至現有的資料列。

#### <a name="performdrop"></a>PerformDrop

`PerformDrop`當使用者完成作業時，會呼叫方法，並修改資料表視圖和資料來源以反映捨棄的資料。

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

您可以加入額外的程式碼，以非同步方式載入大型資料物件。

### <a name="testing-drag-and-drop"></a>測試拖放

您必須使用 iPad 來測試[範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview)。
將範例與另一個應用程式（例如記事）一起開啟，然後在它們之間拖曳資料列和文字：

![拖曳操作進行中的螢幕擷取畫面](drag-and-drop-images/01-sml.png)

## <a name="related-links"></a>相關連結

- [拖放人工介面指導方針（Apple）](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [拖放資料表視圖範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddroptableview)
- [拖放集合視圖範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-draganddropcollectionview)
- [拖放簡介（WWDC）（影片）](https://developer.apple.com/videos/play/wwdc2017/203/)
- [具有集合和資料表視圖的拖放（WWDC）（影片）](https://developer.apple.com/videos/play/wwdc2017/223/)
