---
title: 在 Xamarin.iOS 中 PhotoKit
description: 本文件說明 PhotoKit，討論其模型物件，如何查詢模型資料，以及將變更儲存到相片媒體櫃。
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: d59cac9403a244ce553d84e0590b8a9c3d4d2f30
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102707"
---
# <a name="photokit-in-xamarinios"></a>在 Xamarin.iOS 中 PhotoKit

PhotoKit 是新的架構，可讓應用程式來查詢系統映像庫，並建立自訂使用者介面來檢視和修改其內容。 它包含數個類別，代表影像和視訊資產，以及資產，例如專輯和資料夾的集合。

## <a name="model-objects"></a>模型物件

PhotoKit 表示這些資產，在它呼叫模型物件。 模型物件表示的相片和視訊本身都屬於型別`PHAsset`。 A`PHAsset`包含中繼資料，例如資產的媒體類型和其建立日期。
同樣地，`PHAssetCollection`和`PHCollectionList`類別分別包含資產集合和集合清單的相關中繼資料。 資產集合是資產，例如所有的相片和影片某一年的群組。 同樣地，集合清單是資產的集合，例如相片和視訊分組依年度的群組。

## <a name="querying-model-data"></a>查詢模型資料

PhotoKit 輕鬆地查詢模型資料透過不同的提取方法。 例如，若要擷取的所有映像，您可以呼叫`PFAsset.Fetch`，並傳遞`PHAssetMediaType.Image`媒體類型。

    PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);

`PHFetchResult`執行個體則會包含所有`PFAsset`代表映像的執行個體。 若要取得自己的映像，您使用`PHImageManager`(或快取的版本中， `PHCachingImageManager`) 進行映像的要求，藉由呼叫`RequestImageForAsset`。 例如，下列程式碼中擷取映像中的每個資產`PHFetchResult`中顯示的集合檢視儲存格：


    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
              var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
            imageMgr.RequestImageForAsset ((PHAsset)fetchResults [(uint)indexPath.Item], thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (), (img, info) => {
            imageCell.ImageView.Image = img;
        });
        return imageCell;
    }

這會產生在方格中的映像，如下所示：

![](photokit-images/image4.png "執行應用程式中顯示之影像的方格")
 
## <a name="saving-changes-to-the-photo-library"></a>將變更儲存到相片媒體櫃

這是如何處理查詢和讀取資料。 您也可以寫入變更回文件庫。 因為多個想要的應用程式都能夠與系統相片庫互動，您可以註冊使用 PhotoLibraryObserver 變更的通知觀察者。 然後，當有變更，據以更新您的應用程式。 例如，以下是簡單的實作，以重新載入集合檢視上述：

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
    
若要實際變更寫回從您的應用程式，您會建立變更要求。 每個模型類別都有相關聯的變更要求類別。 例如，若要變更 PHAsset，您會建立 PHAssetChangeRequest。 執行變更寫回至相片媒體櫃並傳送給觀察者，如同上面的步驟如下：

-   執行編輯作業。
-   篩選映像將資料儲存至 PHContentEditingOutput 執行個體。
-   請變更要求，以發佈變更表單的編輯輸出。

以下是將寫回變更 Core 映像作品篩選套用的映像的範例：

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
    
當使用者選取按鈕時，會套用篩選條件：

![](photokit-images/image5.png "正在套用濾波器的範例")
 
而且由於 PHPhotoLibraryChangeObserver，變更會反映在集合檢視當使用者導覽回到：

![](photokit-images/image6.png "此變更會反映集合檢視中，當使用者導覽回到")
