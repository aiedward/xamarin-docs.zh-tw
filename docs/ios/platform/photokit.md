---
title: PhotoKit
description: "相片套件可讓應用程式查詢系統映像庫，並建立自訂使用者介面來檢視和修改其內容。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 1f316979c3262883fc58df93de1afb1167fd36ce
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2018
---
# <a name="photokit"></a>PhotoKit

_相片套件可讓應用程式查詢系統映像庫，並建立自訂使用者介面來檢視和修改其內容。_

相片套件是一個新的架構，可讓應用程式來查詢系統映像庫建立自訂使用者介面來檢視和修改其內容。 它包含的數字，表示影像和視訊資產，以及資產，例如專輯和資料夾的集合類別。

## <a name="model-objects"></a>模型物件
相片套件表示在它呼叫模型物件的這些資產。 表示相片和視訊本身的模型物件屬於類型`PHAsset`。 A`PHAsset`包含中繼資料，例如資產的媒體類型和其建立日期。
同樣地，`PHAssetCollection`和`PHCollectionList`類別分別包含關於資產集合與集合清單的中繼資料。 資產集合是群組的資產，例如相片和視訊，針對特定的年份。 同樣地，集合清單是資產的集合，例如相片和視訊分組依年度的群組。

## <a name="querying-model-data"></a>查詢模型資料
相片套件可輕鬆查詢模型資料透過不同的提取方法。 例如，若要擷取的所有映像，您可以呼叫`PFAsset.Fetch`，並傳遞`PHAssetMediaType.Image`媒體類型。

    PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);

`PHFetchResult`執行個體則會包含所有`PFAsset`代表影像的執行個體。 若要取得其本身的映像，您使用`PHImageManager`(或快取的版本， `PHCachingImageManager`)，以便進行映像要求呼叫`RequestImageForAsset`。 例如，下列程式碼擷取的映像中的每個資產`PHFetchResult`中顯示的集合檢視儲存格：


    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
              var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
            imageMgr.RequestImageForAsset ((PHAsset)fetchResults [(uint)indexPath.Item], thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (), (img, info) => {
            imageCell.ImageView.Image = img;
        });
        return imageCell;
    }

結果格線中的映像，如下所示：

![](photokit-images/image4.png "執行應用程式中顯示之影像的方格")
 
## <a name="saving-changes-to-the-photo-library"></a>將變更儲存到相片程式庫

這是如何處理查詢和讀取資料。 您也可以寫入變更回文件庫。 由於多個想要的應用程式和系統相片程式庫互動，您可以註冊以使用 PhotoLibraryObserver 變更的通知觀察者。 然後，當有變更，據以更新您的應用程式。 例如，以下是要重新載入集合檢視上述的簡單實作：

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
    
若要實際變更寫回從您的應用程式，您可以建立變更要求。 每個模型類別具有相關聯的變更要求類別。 例如，若要變更 PHAsset，您會建立 PHAssetChangeRequest。 若要執行變更寫回至相片文件庫並傳送給觀察者如同上面的步驟如下：

-   執行編輯作業。
-   將已篩選的影像資料儲存至 PHContentEditingOutput 執行個體。
-   請變更要求，以發佈變更表單編輯輸出。

以下是將 Core 映像的作品篩選套用的映像寫回變更的範例：

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
    
當使用者選取按鈕時，套用篩選：

![](photokit-images/image5.png "正在套用濾波器的範例")
 
而且這點受惠 PHPhotoLibraryChangeObserver，變更會反映在集合檢視當使用者導覽回：

![](photokit-images/image6.png "此變更會反映集合檢視中，當使用者導覽回到")
