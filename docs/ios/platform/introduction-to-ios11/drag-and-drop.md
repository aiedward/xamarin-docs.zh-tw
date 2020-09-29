---
title: 在 Xamarin 中拖放
description: 本檔說明如何使用 iOS 11 中引進的 Api，在 Xamarin 應用程式中執行拖放功能。 尤其是，它會討論如何在 Ios uitableview 範例中啟用拖放功能。
ms.prod: xamarin
ms.assetid: 0D39C4C3-D169-42F8-B3FA-7F98CF0B6F1F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/05/2017
ms.openlocfilehash: bbdfe9682474352fab7c0ae3b1cd7d949cbca0b4
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437172"
---
# <a name="drag-and-drop-in-xamarinios"></a>在 Xamarin 中拖放

_執行 iOS 11 的拖放_

iOS 11 包含拖放支援，可在 iPad 上的應用程式之間複製資料。 使用者可以從並存的應用程式中選取並拖曳所有類型的內容，或拖曳到應用程式圖示上，以觸發應用程式開啟並允許卸載資料：

![從自訂應用程式拖放範例至 Notes 應用程式](drag-and-drop-images/drag-drop-sml.png)

> [!NOTE]
> 只有在 iPhone 上的相同應用程式中，才可以使用拖放功能。

請考慮支援可在任何位置建立或編輯內容的拖放作業：

- 文字控制項支援針對 iOS 11 所建立的所有應用程式的拖放功能，而不需要任何額外的工作。
- 資料表視圖和集合視圖包含 iOS 11 的增強功能，可簡化新增拖放行為。
- 您可以使用其他的自訂來進行拖放，以支援任何其他的觀點。

將拖放支援新增至您的應用程式時，您可以提供不同層級的內容精確度;例如，您可以同時提供格式化的文字和純文字版本的資料，讓接收應用程式可以選擇最適合拖曳目標的應用程式。 您也可以自訂拖曳視覺效果，也可以同時啟用多個專案的拖曳。

## <a name="drag-and-drop-with-text-controls"></a>使用文字控制項拖放

`UITextView` 和 `UITextField` 會自動支援拖曳選取的文字，並將文字內容放在中。

<a name="uitableview"></a>

## <a name="drag-and-drop-with-uitableview"></a>使用 Ios uitableview 範例拖放

`UITableView` 具有與資料表資料列的拖放互動的內建處理，只需要幾個方法來啟用預設行為。

有兩個相關的介面：

- `IUITableViewDragDelegate` –在資料表視圖中起始拖曳時的封裝資訊。
- `IUITableViewDropDelegate` –在嘗試和完成卸載時處理資訊。

在 [DragAndDropTableView 範例](/samples/xamarin/ios-samples/ios11-draganddroptableview) 中，這兩個介面都是在 `UITableViewController` 類別上執行，以及委派和資料來源。 它們是在方法中指派 `ViewDidLoad` ：

```csharp
this.TableView.DragDelegate = this;
this.TableView.DropDelegate = this;
```

以下說明這兩個介面的最基本必要程式碼。

### <a name="table-view-drag-delegate"></a>資料表視圖拖曳委派

支援從資料表視圖拖曳資料列 _所需_ 的唯一方法是 `GetItemsForBeginningDragSession` 。 如果使用者開始拖曳資料列，則會呼叫這個方法。

執行如下所示。 它會抓取與拖曳資料列相關聯的資料、將其編碼，並設定，以 `NSItemProvider` 決定應用程式將如何處理作業的「卸載」部分 (例如，是否可以 `PlainText` 在範例) 中處理資料類型：

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

拖曳委派上有許多選擇性方法，可加以實作為自訂拖曳行為，例如提供多個可利用目標應用程式中的資料標記法 (例如格式化的文字以及純文字，或是繪圖) 的向量和點陣圖版本。 您也可以提供在相同應用程式中拖曳和卸載時使用的自訂資料標記法。

### <a name="table-view-drop-delegate"></a>資料表視圖 Drop Delegate

當拖曳作業在資料表視圖上進行，或在其上方完成時，會呼叫 drop 委派上的方法。 必要的方法會決定是否允許卸載資料，以及卸載完成時要採取的動作：

- `CanHandleDropSession` –雖然拖曳正在進行中，而且可能會在應用程式上卸載，但此方法會決定是否允許卸載所拖曳的資料。
- `DropSessionDidUpdate` –進行拖曳時，會呼叫這個方法來判斷要採取什麼動作。 要拖曳的資料表視圖中的資訊、拖曳會話，以及可能的索引路徑都可以用來判斷提供給使用者的行為和視覺效果意見反應。
- `PerformDrop` –當使用者藉由將手指) 拉 (完成 drop 時，這個方法會將拖曳的資料解壓縮，並修改資料表視圖，以將資料加入新的資料列 (或資料列) 。

#### <a name="canhandledropsession"></a>CanHandleDropSession

`CanHandleDropSession` 指出資料表視圖是否可以接受所拖曳的資料。 在此程式碼片段中， `CanLoadObjects` 是用來確認這個資料表視圖可以接受字串資料。

```csharp
public bool CanHandleDropSession(UITableView tableView, IUIDropSession session)
{
  return session.CanLoadObjects(typeof(NSString));
}
```

#### <a name="dropsessiondidupdate"></a>DropSessionDidUpdate

`DropSessionDidUpdate`當拖曳作業正在進行時，方法會重複呼叫，以提供視覺提示給使用者。

在下列程式碼中， `HasActiveDrag` 是用來判斷作業是否源自于目前的資料表視圖。 如果是，則只允許移動單一資料列。
如果拖曳來自另一個來源，則會顯示覆製作業：

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

Drop 作業可以是、或的其中一個 `Cancel` `Move` `Copy` 。

Drop 意圖可以是插入新的資料列，或將資料加入至現有的資料列。

#### <a name="performdrop"></a>PerformDrop

`PerformDrop`當使用者完成作業時，會呼叫方法，並修改資料表視圖和資料來源以反映已卸載的資料。

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

您可以新增額外的程式碼，以非同步方式載入大型資料物件。

### <a name="testing-drag-and-drop"></a>測試拖放

您必須使用 iPad 來測試 [範例](/samples/xamarin/ios-samples/ios11-draganddroptableview)。
將範例與另一個應用程式一起開啟 (例如附注) ，然後在兩者之間拖曳資料列和文字：

![拖曳作業進行中的螢幕擷取畫面](drag-and-drop-images/01-sml.png)

## <a name="related-links"></a>相關連結

- [拖放人類介面指導方針 (Apple) ](https://developer.apple.com/ios/human-interface-guidelines/interaction/drag-and-drop/)
- [拖放資料表視圖範例](/samples/xamarin/ios-samples/ios11-draganddroptableview)
- [拖放收集視圖範例](/samples/xamarin/ios-samples/ios11-draganddropcollectionview)
- [拖放 (WWDC)  (影片的簡介) ](https://developer.apple.com/videos/play/wwdc2017/203/)
- [使用集合和資料表視圖拖放 (WWDC)  (影片) ](https://developer.apple.com/videos/play/wwdc2017/223/)