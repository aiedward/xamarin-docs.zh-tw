---
title: 在 Xamarin 中 PhotoKit
description: 本檔描述 PhotoKit、討論其模型物件、如何查詢模型資料，以及如何將變更儲存至相片媒體櫃。
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/14/2017
ms.openlocfilehash: 433e50632ce7334f7a815fb8952dda2dfc110578
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290517"
---
# <a name="photokit-in-xamarinios"></a>在 Xamarin 中 PhotoKit

PhotoKit 是新的架構，可讓應用程式查詢系統映射庫，並建立自訂使用者介面來查看和修改其內容。 其中包含一些代表影像和影片資產的類別，以及一系列的資產，例如專輯和資料夾。

## <a name="model-objects"></a>模型物件

PhotoKit 會在其呼叫模型物件時，代表這些資產。 代表相片和影片本身的模型物件屬於類型`PHAsset`。 `PHAsset`包含中繼資料，例如資產的媒體類型及其建立日期。
同樣地， `PHAssetCollection`和`PHCollectionList`類別會分別包含資產集合和集合清單的中繼資料。 資產集合是一組資產，例如指定年份的所有相片和影片。 同樣地，集合清單是資產集合的群組，例如依年份分組的相片和影片。

## <a name="querying-model-data"></a>查詢模型資料

PhotoKit 可讓您輕鬆地透過各種 fetch 方法來查詢模型資料。 例如，若要取出所有影像，您可以呼叫`PHAsset.Fetch`， `PHAssetMediaType.Image`傳遞媒體類型。

```csharp
PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);
```

然後`PHFetchResult` ，實例會包含代表影像`PHAsset`的所有實例。 若要取得映射本身，您可以使用`PHImageManager` （或快取`PHCachingImageManager`版本），藉由呼叫`RequestImageForAsset`來對影像提出要求。 例如，下列程式碼會針對中的`PHFetchResult`每個資產抓取影像，以顯示在 [集合視圖] 資料格中：

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
    imageMgr.RequestImageForAsset (
        (PHAsset)fetchResults [(uint)indexPath.Item],
        thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (),
        (img, info) => {
            imageCell.ImageView.Image = img;
        }
    );
    return imageCell;
}
```

這會產生影像方格，如下所示：

![](photokit-images/image4.png "顯示影像方格的執行中應用程式")

## <a name="saving-changes-to-the-photo-library"></a>儲存相片媒體櫃的變更

這就是處理查詢和讀取資料的方式。 您也可以將變更寫回程式庫。 因為有多個感興趣的應用程式可以與系統相片圖庫互動，所以您可以註冊觀察者，以使用 PhotoLibraryObserver 來通知變更。 然後，當變更時，您的應用程式就可以據以更新。 例如，以下是重載上述收集視圖的簡單執行：

```csharp
class PhotoLibraryObserver : PHPhotoLibraryChangeObserver
{
    readonly PhotosViewController controller;
    public PhotoLibraryObserver (PhotosViewController controller)

    {
        this.controller = controller;
    }

    public override void PhotoLibraryDidChange (PHChange changeInstance)
    {
        DispatchQueue.MainQueue.DispatchAsync (() => {
        var changes = changeInstance.GetFetchResultChangeDetails (controller.fetchResults);
        controller.fetchResults = changes.FetchResultAfterChanges;
        controller.CollectionView.ReloadData ();
        });
    }
}
```

若要實際從您的應用程式寫回變更，您可以建立變更要求。 每個模型類別都有相關聯的變更要求類別。 例如，若要變更 PHAsset，您可以建立 PHAssetChangeRequest。 執行會寫回相片媒體櫃並傳送給觀察者的步驟如下所示：

- 執行編輯作業。
- 將篩選過的影像資料儲存至 PHContentEditingOutput 實例。
- 進行變更要求，以發佈變更表單編輯輸出。

以下範例會將變更寫回套用核心映射 noir 篩選的映射：

```csharp
void ApplyNoirFilter (object sender, EventArgs e)
{

    Asset.RequestContentEditingInput (new PHContentEditingInputRequestOptions (), (input, options) => {

        // perform the editing operation, which applies a noir filter in this case
        var image = CIImage.FromUrl (input.FullSizeImageUrl);
        image = image.CreateWithOrientation((CIImageOrientation)input.FullSizeImageOrientation);
        var noir = new CIPhotoEffectNoir {
            Image = image
        };
        var ciContext = CIContext.FromOptions (null);
        var output = noir.OutputImage;
        var uiImage = UIImage.FromImage (ciContext.CreateCGImage (output, output.Extent));
        imageView.Image = uiImage;
        //
        // save the filtered image data to a PHContentEditingOutput instance
        var editingOutput = new PHContentEditingOutput(input);
        var adjustmentData = new PHAdjustmentData();
        var data = uiImage.AsJPEG();
        NSError error;
        data.Save(editingOutput.RenderedContentUrl, false, out error);
        editingOutput.AdjustmentData = adjustmentData;
        //
        // make a change request to publish the changes form the editing output
        PHPhotoLibrary.GetSharedPhotoLibrary.PerformChanges (() => {
            PHAssetChangeRequest request = PHAssetChangeRequest.ChangeRequest(Asset);
            request.ContentEditingOutput = editingOutput;
        },
        (ok, err) => Console.WriteLine ("photo updated successfully: {0}", ok));
    });
}
```

當使用者選取按鈕時，就會套用篩選準則：

![](photokit-images/image5.png "套用的篩選範例")

而且感謝 PHPhotoLibraryChangeObserver，當使用者導覽回來時，此變更會反映在 [集合] 視圖中：

![](photokit-images/image6.png "當使用者流覽後，此變更會反映在 [集合] 視圖中")
