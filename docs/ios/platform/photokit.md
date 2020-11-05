---
title: 在 Xamarin 中 PhotoKit
description: 本檔將說明 PhotoKit、討論其模型物件、如何查詢模型資料，以及將變更儲存至照片媒體櫃。
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 5b9bcbd941c1f2d0a1e0e89d5fe34f09c7b5a0dc
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373758"
---
# <a name="photokit-in-xamarinios"></a>在 Xamarin 中 PhotoKit

[![下載範例 ](~/media/shared/download.png) 下載程式代碼範例](/samples/xamarin/ios-samples/ios11-samplephotoapp/)

PhotoKit 是一種架構，可讓應用程式查詢系統映射庫，並建立自訂使用者介面來查看和修改其內容。 它包含一些類別，這些類別代表影像和影片資產，以及專輯和資料夾等資產集合。

## <a name="permissions"></a>權限

在您的應用程式可以存取相片媒體櫃之前，使用者將會看到 [許可權] 對話方塊。 您必須在 **plist** 檔案中提供解說文字，以說明您的應用程式如何使用圖片庫，例如：

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Applies filters to photos and updates the original image</string>
```

## <a name="model-objects"></a>模型物件

PhotoKit 會在其呼叫模型物件的內容中代表這些資產。 代表相片和影片本身的模型物件屬於類型 `PHAsset` 。 `PHAsset`包含中繼資料，例如資產的媒體類型和其建立日期。
同樣地， `PHAssetCollection` 和 `PHCollectionList` 類別會分別包含資產集合和集合清單的相關中繼資料。 資產集合是資產的群組，例如指定年份的所有相片和影片。 同樣地，集合清單是資產集合的群組，例如依年份分組的相片和影片。

## <a name="querying-model-data"></a>查詢模型資料

PhotoKit 可讓您輕鬆地透過各種 fetch 方法來查詢模型資料。 例如，若要取出所有影像，您可以呼叫 `PHAsset.Fetch` ，傳遞 `PHAssetMediaType.Image` 媒體類型。

```csharp
PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);
```

`PHFetchResult`實例接著會包含 `PHAsset` 代表影像的所有實例。 若要取得影像本身，請使用 `PHImageManager` (或快取版本， `PHCachingImageManager`) 透過呼叫來為影像提出要求 `RequestImageForAsset` 。 例如，下列程式碼會針對中的每個資產抓取影像 `PHFetchResult` ，以顯示在集合視圖資料格中：

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

這會產生影像的方格，如下所示：

![正在執行的應用程式，顯示影像的方格](photokit-images/image4.png)

## <a name="saving-changes-to-the-photo-library"></a>儲存相片媒體櫃的變更

這就是處理查詢和讀取資料的方式。 您也可以將變更寫回程式庫。 由於有多個感興趣的應用程式能夠與系統相片圖庫互動，您可以註冊觀察者，以使用來通知變更 `PhotoLibraryObserver` 。 然後，當發生變更時，您的應用程式就可以據以更新。 例如，以下是可重載上述收集視圖的簡單執行：

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

若要實際從您的應用程式寫回變更，您可以建立變更要求。 每個模型類別都有相關聯的變更要求類別。 例如，若要變更 `PHAsset` ，您必須建立 `PHAssetChangeRequest` 。 執行將寫回照片媒體櫃並傳送給觀察者的步驟，如下所示：

1. 執行編輯作業。
2. 將已篩選的影像資料儲存至 `PHContentEditingOutput` 實例。
3. 發出變更要求，以從編輯輸出發佈變更。

以下範例會將變更回寫至套用核心映射 noir 篩選的映射：

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

當使用者選取按鈕時，就會套用篩選：

![兩個範例，顯示套用篩選之前和之後的相片](photokit-images/image5.png)

而且 `PHPhotoLibraryChangeObserver` ，由於這項變更會在使用者流覽回來時反映于收集視圖中：

![顯示已修改相片的相片集合視圖](photokit-images/image6.png)