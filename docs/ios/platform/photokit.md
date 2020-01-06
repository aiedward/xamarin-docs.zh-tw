---
title: 在 Xamarin 中 PhotoKit
description: 本檔描述 PhotoKit、討論其模型物件、如何查詢模型資料，以及如何將變更儲存至相片媒體櫃。
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: def34efd1fd48cc0e7dd802a6d3e843be1e156a4
ms.sourcegitcommit: 5ddb107b0a56bef8a16fce5bc6846f9673b3b22e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2019
ms.locfileid: "75558803"
---
# <a name="photokit-in-xamarinios"></a>在 Xamarin 中 PhotoKit

[![下載範例](~/media/shared/download.png) 下載程式代碼範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios11-samplephotoapp/)

PhotoKit 是一種架構，可讓應用程式查詢系統映射庫，並建立自訂使用者介面來查看和修改其內容。 其中包含一些代表影像和影片資產的類別，以及一系列的資產，例如專輯和資料夾。

## <a name="permissions"></a>權限

在您的應用程式可以存取相片媒體櫃之前，使用者會看到 [許可權] 對話方塊。 您必須在**plist**檔案中提供說明性文字，以說明您的應用程式如何使用相片媒體櫃，例如：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Applies filters to photos and updates the original image</string>
```

## <a name="model-objects"></a>模型物件

PhotoKit 會在其呼叫模型物件時，代表這些資產。 代表相片和影片本身的模型物件屬於 `PHAsset`類型。 `PHAsset` 包含資產媒體類型及其建立日期之類的中繼資料。
同樣地，`PHAssetCollection` 和 `PHCollectionList` 類別分別包含資產集合和集合清單的相關中繼資料。 資產集合是一組資產，例如指定年份的所有相片和影片。 同樣地，集合清單是資產集合的群組，例如依年份分組的相片和影片。

## <a name="querying-model-data"></a>查詢模型資料

PhotoKit 可讓您輕鬆地透過各種 fetch 方法來查詢模型資料。 例如，若要取出所有影像，您可以呼叫 `PHAsset.Fetch`，並傳遞 `PHAssetMediaType.Image` 媒體類型。

```csharp
PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);
```

`PHFetchResult` 實例會接著包含代表影像的所有 `PHAsset` 實例。 若要取得映射本身，您可以使用 `PHImageManager` （或快取版本，`PHCachingImageManager`）藉由呼叫 `RequestImageForAsset`來提出影像要求。 例如，下列程式碼會抓取 `PHFetchResult` 中每個資產的影像，以顯示在 [集合視圖] 儲存格中：

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

![](photokit-images/image4.png "The running app displaying a grid of images")

## <a name="saving-changes-to-the-photo-library"></a>儲存相片媒體櫃的變更

這就是處理查詢和讀取資料的方式。 您也可以將變更寫回程式庫。 因為有多個感興趣的應用程式可以與系統相片媒體櫃互動，所以您可以註冊觀察者，以使用 `PhotoLibraryObserver`來通知變更。 然後，當變更時，您的應用程式就可以據以更新。 例如，以下是重載上述收集視圖的簡單執行：

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

若要實際從您的應用程式寫回變更，您可以建立變更要求。 每個模型類別都有相關聯的變更要求類別。 例如，若要變更 `PHAsset`，請建立 `PHAssetChangeRequest`。 執行會寫回相片媒體櫃並傳送給觀察者的步驟如下所示：

1. 執行編輯作業。
2. 將篩選過的影像資料儲存至 `PHContentEditingOutput` 實例。
3. 提出變更要求，從編輯輸出發佈變更。

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

![兩個範例，顯示套用篩選前後的相片](photokit-images/image5.png)

由於 `PHPhotoLibraryChangeObserver`，因此當使用者導覽回來時，變更會反映在 [集合] 視圖中：

![顯示已修改相片的相片集合視圖](photokit-images/image6.png)
