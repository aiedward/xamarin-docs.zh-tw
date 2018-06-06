---
title: 其他 iOS 10 架構變更
description: 本文件描述次要變更和增強功能對 iOS 10 中的現有架構，並討論如何進行這些更新 Xamarin.iOS 中使用。
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/29/2017
ms.openlocfilehash: 4b9a230157593b66446e2949e57a925d94208752
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34787555"
---
# <a name="additional-ios-10-frameworks-changes"></a>其他 iOS 10 架構變更

_本文涵蓋其他次要變更或增強現有的架構，適用於 iOS 10。_

## <a name="av-foundation-framework-additions"></a>AV 基礎架構新增項目

AVFoundation 架構包含下列增強功能：

- 在 iOS 10，開發人員不會再有實作不同[AVPlayerItem](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/)行為會根據內容類型。 只要設定`Rate`屬性而且 AVFoundation 會決定當足夠的內容是可供播放不一。
- 新[AVCapturePhotoOutput](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureFileOutput/)類別會取代已被取代`AVCaptureStillImageOutput`類別，並提供統一的方法，來處理所有攝影工作流程就提供複雜的控制和監視擷取處理序和新功能，例如 Live 相片和擷取未經處理格式的支援。
- 新`AVPlayerLooper`類別可讓您更輕鬆地在播放期間迴圈指定的媒體項目。
- `AVAssetDownloadURLSession`類別可讓下載和更新播放 FairPlay 加密 HLS 資料流。
- 根據預設， [AVCaptureSession](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureSession/)類別會自動支援全彩、 寬一系列擷取時裝置硬體支援它。 請參閱 Apple [iOS 裝置的相容性參考](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599)如需詳細資訊。

## <a name="avkit-additions"></a>AVKit 新增項目

AVKit framework 現在包含新`UpdatesNowPlayingInfoCenter`屬性，指出現在遊戲資訊中心應已更新。

## <a name="core-data-enhancements"></a>核心資料增強功能

iOS 10 包含核心資料架構的下列增強功能：

- [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/)物件與 SQLite WAL 日誌模式支援新的查詢產生的資料存放區功能在受管理物件內容 （木） 可以固定到未來擷取特定的資料庫版本和判定為失敗的交易。
- 根[NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/)物件支援並行判定為失敗，和擷取沒有序列化。
- [NSPersistentStoreCoordinator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/)類別會維護 SQLite 資料存放區集區。
- 已加入數個新的便利方法`NSManagedObject`以便更容易執行提取及建立子類別。
- 使用高階`NSPersistenceContainer`參考`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectModel/)和其他資源的核心資料設定。

如需詳細資訊，請參閱 Apple[核心資料架構參考](https://developer.apple.com/reference/coredata)。

## <a name="core-image-enhancements"></a>Core 映像的增強功能

iOS 10 會進行 Core 映像架構的下列增強功能：

- 開發人員現在可以藉由之前和之後處理內外的色彩空間轉換處理 Core 映像內容的工作色彩空間以外的色彩空間中的映像。
- 針對使用 A8 或 A9 Cpu 的 iOS 裝置，現在支援未經處理的影像格式。 解碼原始映像，從內建 iSight 攝影機或從第 3 個合作對象相機 core 映像現在會提供支援。 使用`FilterWithImageData`或`FilterWithImageURL`方法[CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/)類別來處理未經處理的映像。
- 數個轉譯效能增強功能已對`UIImage`中呈現 （當 Core 映像的映像存放區所支援）`UIImageView`物件。 
- `UIImage` 物件已加上標籤的整個範圍將會呈現為寬色域圖中的色彩`UIImageView`支援寬色彩的 iOS 裝置上的物件。
- Core 映像核心程式碼現在可以要求特定像素輸出格式。
- `ImageWithExtent`方法[CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/)類別可以用來篩選作業中插入自訂處理。 Core 映像將會叫用指定的回呼之間篩選處理輸出的映像時，或顯示。

此外，已新增下列新 Core 映像篩選器：

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>核心影片新增項目

新增 ios 10 Core 影片架構包含 pedometer 事件可讓應用程式接收快速、 暫停和繼續執行時追蹤之使用者的即時通知。 使用[CMPedometer](https://developer.xamarin.com/api/type/CoreMotion.CMPedometer/)以註冊前景或背景 pedometer 事件。

## <a name="foundation-enhancements"></a>Foundation 增強功能

IOS 10 的基礎架構已做了下列增強功能：

- 使用新[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)類別來格式化終端使用者顯示當地語系化的度量單位。
- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別，例如持續時間，來比較間隔及測試間隔交集的日期和時間間隔計算。
- 使用新[NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement)轉換之間不同單位的量值 (UOM)，或在不同的 UOMs 值上執行計算的類別。

- 使用新[NSUnit](https://developer.apple.com/reference/foundation/nsunit)和[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)類別，表示特定 UOMs。
- 已加入數個新屬性[NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別取得本機資訊以及可用的顯示格式。

## <a name="gamekit-enhancements"></a>GameKit 增強功能

在 iOS 10 GameKit 架構已做了下列增強功能：

- **Game Center 應用程式**已被取代，從 iOS 中移除。 如果應用程式使用 GameKit，它_必須_呈現自己的介面，以顯示 GameKit 功能，例如排行榜等等。 
- 新的僅限 iCloud 帳戶類型由[GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別。
- 新[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)類別提供的管理永續性資料儲存在遊戲中心的一般化的解決方案。 `GKGameSession` 會維護的播放清單和應用程式會負責實作如何及何時儲存、 擷取或播放程式之間交換參與者的日期。 在許多情況下遊戲工作階段可以取代現有的開啟相符項，即時的相符項目或持續性方法儲存的遊戲。

## <a name="gameplaykit-enhancements"></a>GameplayKit 增強功能

在 iOS 10 GameplayKit 架構已做了下列增強功能：

- 使用新[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)提供高效能、 自然尋找路徑的類別。
- 程序的雜訊產生已經加入，而且可用來增強自然尋找紋理中的真實性，加入真實度相機移動並說明產生豐富的遊戲世界。
- 使用空間資料分割的有效搜尋的遊戲世界資料分割。
- 新的 Monte Carlo 策劃師 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 已加入的獨占可能移動計算。
- 3D 支援已新增至現有的代理程式和使用新的路徑尋找行為[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)和[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)類別。
- 新[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)和[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)類別產生結合 GameplayKit 和 SpriteKit 比以往更為容易。
- 已加入新的決策樹 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)和[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 來增強遊戲建置 AI。

## <a name="healthkit-enhancements"></a>HealthKit 增強功能

在 iOS 10 HealthKit 架構已做了下列增強功能：

- 新的中繼資料金鑰已經加入的天氣類型 (例如`HKWeatherConditionClear`和`HKWeatherConditionCloudy`) 和健身類型 (例如`HKWorkoutActivityTypeFlexibility`和`HKWorkoutActivityTypeWheelchairRunPace`) 已加入。
- 新`HKCDADocument`類別已新增至代表臨床文件架構 (CDA) 格式化文件。
- 使用新[HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration)類別，以指定`ActivityType`和`LocationType`健身。
- 新[HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject)和`WheelchairUse`方法[HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)類別已新增了殘障人士使用相關的健全狀況資料。

## <a name="homekit-enhancements"></a>HomeKit 增強功能

在 iOS 10 HomeKit 架構已做了下列增強功能：

- 已加入新的服務和特性。
- IPad 可以設定為做為 HomeKit 中樞提供配件的遠端存取、 執行自動化觸發程序，並啟用共用的使用者權限。
- 相機與 doorbell 附屬應用程式已新增支援。
- 多個內容和組態已提供的附屬應用程式。

請參閱我們[簡介 HomeKit](~/ios/platform/homekit.md)文件的詳細資訊。

## <a name="metal-enhancements"></a>裸機增強功能

在 iOS 10 中的金屬 framework 已做了下列增強功能：

- 現在可以使用 3D 應用程式和遊戲_鑲嵌式_來有效率地呈現複雜的場景和透過 GPU 的幾何。
- 請提供更精細地控制資源配置，來最佳化效能的金屬堆積使用資源的應用程式和 Memoryless 呈現目標。
- 若要建立獲得高度最佳化的場景的淺組合函式和資料，集合中使用函式特製化。

若要進一步了解，請參閱 Apple[金屬程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

## <a name="modelio-enhancements"></a>ModelIO 增強功能

在 iOS 10 ModelIO 架構已做了下列增強功能：

 - 現在支援 USD 檔案格式。
 - 帶正負號距離的欄位已加入支援[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)類別。
 - 使用新`MDLLightProbeIrradianceDataSource`類別，以協助 Light 探查位置中。
 - 使用新`MDLMaterialPropertyGraph`類別，以輕鬆地支援執行階段變更模型。

## <a name="photos-enhancements"></a>相片的增強功能

在 iOS 10 中的相片 framework 已做了下列增強功能：

- 使用[CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput)和[CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput)類別，以充分利用新 Core 映像處理器功能，來執行編輯。
- 即時相片編輯現已提供支援相片架構的應用程式和相片編輯擴充功能 (使用內部的相片和相機應用程式)。
- 使用新[PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext)類別，以編輯套用至視訊和仍然 Live 相片的內容。

## <a name="replaykit-enhancements"></a>ReplayKit 增強功能

在 iOS 10 ReplayKit 架構已做了下列增強功能：

- 使用[RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder)， [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller)和[RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller)類別，來支援廣播的記錄透過第 3 個合作對象的媒體站台。
- 應用程式中支援 ReplayKit 3rd 合作對象廣播的服務所需的廣播 UI 和廣播上傳擴充功能。

## <a name="scenekit-enhancements"></a>SceneKit 增強功能

在 iOS 10 SceneKit 架構已做了下列增強功能：

- [SCNCamera](https://developer.xamarin.com/api/type/SceneKit.SCNCamera/)類別可以使用 HDR 功能和效果提供更大的真實性。 使用自動調整曝光建立自動效果或使用 vignetting、 fringing 色彩及色彩閱卷 fillmatic 效果加入遊戲。
- SceneKit 現在包含新的實際基礎呈現 (PBR) 系統與撰寫簡單的資產比較實際的結果。
- 使用新[SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased)網底模型產品各種不同的具真實感的陰影效果同時需要只有三個基本屬性 (`Diffuse`，`Metalness`和`Roughness`)。
- PBR 與環境為基礎的光源最佳陰影運作，因為使用`LightingEnvironment`屬性，以將映像為基礎的光源指派給整個場景。
- 使用`IESProfileURL`要匯入定義光源的真實世界照明設備屬性根據強度 （以 lumens) 和色溫 （以度為單位開氏） 的真實世界等值。
- PBR 和 HDR 相機功能提供更好的結果，與傳統的呈現技術，如此一來，SceneKit 現在便會執行所有色彩計算線性色彩空間 （wide 色彩裝置顯示畫面上使用 P3 色域圖） 中。
- 依讀取的色彩設定檔資訊 SceneKit 現在色彩比對的所有色彩。
- SceneKit 解譯所有著色器類型線性 RGB 色彩空間中的色彩元件值。
- 線性兩者色彩空間轉譯，可以透過指定停用整個色彩`SCNDisableLinearSpaceRendering`和`SCNDisableWideGamut`的應用程式中的索引鍵`Info.plist`。
- 建立任意多邊形靈長類動物 （無論是從檔案載入或透過程式設計方式產生） 來指定與新的 geometry [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon)類別。
- 由於 SceneKit 讀取，並且調整紋理映像中的色彩設定檔資訊，使用所有影像資產目錄以確保在提供此資訊。

## <a name="spritekit-enhancements"></a>SpriteKit 增強功能

在 iOS 10 SpriteKit 架構已做了下列增強功能：

- 自訂的著色器可以提供屬性 (`SKAttribute`)，藉由提供屬性值使用著色器的每一個節點可以分別設定 (`SKAttributeValue`)。
- 2D、 2.5 D 和使用的側邊捲動遊戲 Tilemaps 現在支援的正方形、 六角和等並排顯示圖形`SKTileMapMode`， `SKTileGroup`，`SKTileGroupRule`和`SKTileSet`類別。
- 使用新`SKWarpGeometry`類別來自動縮放或扭曲[SKSpriteNode](https://developer.xamarin.com/api/type/SpriteKit.SKSpriteNode/)或[SKEffectNode](https://developer.xamarin.com/api/type/SpriteKit.SKEffectNode/)轉譯。 新[SKAction](https://developer.xamarin.com/api/type/SpriteKit.SKAction/)類別可以用來建立動畫 warp 效果之間的轉換。
- [SKView](https://developer.xamarin.com/api/type/SpriteKit.SKView/)類別提供數種新方法，來提供更細微的控制時機和方式呈現的場景。

## <a name="scrollview-enhancements"></a>ScrollView 增強功能

在 iOS 10.3 ScrollView 控制項已做了下列增強功能：

- `UIScrollView` 現在包含`IndexDisplayMode`屬性，即可控制使用者做為可捲動時，如何顯示索引`UIScrollViewIndexDisplayMode`的：
    - `Automatic` -索引顯示是由作業系統所控制的。
    - `AlwaysHidden` -索引顯示永遠呈隱藏狀態。

請參閱[iOSTenThree 範例](https://developer.xamarin.com/samples/monotouch/iOS10/iOSTenThree)使用量。

## <a name="uikit-enhancements"></a>UIKit 增強功能

UIKit 架構，在 iOS 10 中已做了下列增強功能：

- 新[UIPasteboard](https://developer.xamarin.com/api/type/UIKit.UIPasteboard/) API 會提供新選項 （例如存留時間限制），並會自動宣告相容的一般類別類型的內容類型。
- 已加入新的完全互動式、 以物件為基礎、 可中斷動畫支援而且必須可連結手勢。 請參閱 Apple 的[UIViewAnimating 通訊協定參考](https://developer.apple.com/reference/uikit/uiviewanimating)， [UIViewPropertyAnimator 類別參考](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)， [UITimingCurveProvider 通訊協定參考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)， [UICubicTimingParameters 類別參考](https://developer.apple.com/reference/uikit/uicubictimingparameters)和[UISpringTimingParameter 類別參考](https://developer.apple.com/reference/uikit/uispringtimingparameters)如需詳細資訊。
- 新`UIPreviewInteraction`和`UIPreviewInteractionDelegate`允許開發人員應用程式，以查看和 pop 作業提供自訂的介面。
- 新`UIAccessibilityCustomRotor`類別可讓應用程式，以提供自訂的特定內容的輔助技術，例如透過語音功能。
- 使用`UIAccessibilityIsAssistiveTouchRunning`和`UIAccessibilityAssistiveTouchStatusDidChangeNotification`符號來判斷是否已啟用 AssistiveTouch。
- 使用`UIAccessibilityHearingDevicePairedEar`和`UIAccessibilityHearingDevicePairedEarDidChangeNotification`符號來取得狀態的任何成對 MFi 聽覺輔助工具。
- 若要支援動態類型標籤中，文字欄位與文字方塊使用新`PreferredFontForTextStyle`方法`UIFont`類別。
- 若要決定如果項目應該更新其字型時裝置的`UIContentSizeCategory`變更，使用`AdjustsFontForContentSizeCategory`屬性`UIContentSizeCategoryAdjusting`委派。
- `OpenURL`方法`UIApplication`類別在以非同步方式呼叫，而且現在支援稱為開啟動作完成之後完成處理常式。
- 起始 CloudKit 共用，並修改其屬性，使用新`UICloudSharingController`和`UICloudSharingControllerDelegate`類別。
- 利用預先擷取的資料格，以改善捲動經驗的`UICollectionViews`與新`UICollectionViewDataSourcePrefetching`委派。
- 開發人員現在可以控制索引標籤列項目 （例如文字和背景的色彩） 徽章的外觀。
- 重新整理控制項現可支援所有的捲動檢視和捲軸檢視子類別 (例如`UICollectionView`)。

## <a name="webkit-enhancements"></a>WebKit 增強功能

在 iOS 10 WebKit 架構已做了下列增強功能：

- 查看和 pop 的支援已新增至`WKWebView`類別。 使用`ShouldPreviewElement`方法，以判斷給定的 web 檢視是否應該顯示預覽。


## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [在 iOS 10 最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
