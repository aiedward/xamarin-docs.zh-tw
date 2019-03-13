---
title: 其他 iOS 10 架構變更
description: 這份文件說明微幅的變更和增強功能來在 iOS 10 中的現有架構，並討論如何讓這些更新在 Xamarin.iOS 中使用。
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: a7b029aad69e65192d48d969dba2b9bb9a0d7a50
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233896"
---
# <a name="additional-ios-10-frameworks-changes"></a>其他 iOS 10 架構變更

_本文章涵蓋其他的次要變更或現有的架構，適用於 iOS 10 的增強功能。_

## <a name="av-foundation-framework-additions"></a>AV Foundation 架構新增項目

AVFoundation 架構包含下列增強功能：

- 在 iOS 10 中，開發人員再也不需要實作不同[AVPlayerItem](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/)行為取決於內容類型。 只要設定`Rate`屬性和 AVFoundation 會決定當足夠的內容是適用於播放不含懸置。
- 新[AVCapturePhotoOutput](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureFileOutput/)類別會取代已被取代`AVCaptureStillImageOutput`類別並且提供統一的方法來處理攝影的所有工作流程提供精密的控制和監視擷取處理序和支援新功能，例如 Live 相片和擷取未經處理格式。
- 新`AVPlayerLooper`類別可讓您更輕鬆地循環播放在播放期間，指定的媒體項目。
- `AVAssetDownloadURLSession`類別是用來下載和更新版本播放 FairPlay 加密的 HLS 串流。
- 根據預設， [AVCaptureSession](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureSession/)類別會自動支援整個色彩、 寬色域圖擷取時裝置硬體支援的情況。 請參閱 Apple [iOS 裝置相容性參考](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599)如需詳細資訊。

## <a name="avkit-additions"></a>AVKit 新增項目

AVKit framework 現在包含新`UpdatesNowPlayingInfoCenter`屬性，指出何時應該更新現在遊戲資訊安全中心。

## <a name="core-data-enhancements"></a>核心資料增強功能

iOS 10 包括下列增強功能的核心資料架構：

- [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/)物件與 SQLite WAL 日誌模式支援新的查詢產生的資料存放區功能，受管理物件內容 （模式） 可以釘選到未來擷取特定的資料庫版本和失敗的交易。
- 根[NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/)物件支援並行的判定為失敗，並擷取沒有序列化。
- [NSPersistentStoreCoordinator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/)類別維護的 SQLite 資料存放區集區。
- 已新增數個新的便利方法`NSManagedObject`更容易執行提取，並建立子類別。
- 使用高階`NSPersistenceContainer`參考`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectModel/)和其他核心資料設定資源。

如需詳細資訊，請參閱 Apple [Core Data Framework 參考](https://developer.apple.com/reference/coredata)。

## <a name="core-image-enhancements"></a>核心映像的增強功能

iOS 10 可讓 Core 映像架構的下列增強功能：

- 開發人員現在可以將核心映像內容的處理色彩空間之外的色彩空間中的映像處理之前和之後處理流入和流出的色彩空間轉換。
- 使用 A8 或 A9 Cpu 的 iOS 裝置，現在支援原始映像格式。 解碼原始的映像，從內建 iSight 攝影機或從第 3 個合作對象相機 core 映像現在會提供支援。 使用`FilterWithImageData`或是`FilterWithImageURL`種[CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/)類別來處理未經處理的影像。
- 數個轉譯效能增強功能進行了`UIImage`呈現 （當受 Core 映像的映像存放區） 中`UIImageView`物件。 
- `UIImage` 物件已加上標籤的範圍-色域圖會轉譯為寬色域圖中的色彩`UIImageView`支援各種色彩的 iOS 裝置上的物件。
- Core 映像的核心程式碼現在可以要求特定的像素輸出格式。
- `ImageWithExtent`方法[CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/)類別可以用來篩選作業中插入自訂處理。 Core 映像將會叫用指定的回撥處理輸出的映像時的篩選器之間，或顯示。

此外，已新增下列新的核心映像篩選器：

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Core 動作新增項目

新增至 iOS 10，核心影片 framework 包含 pedometer 事件可讓應用程式快速、 接收即時通知的使用者暫停和繼續執行時的追蹤。 使用[CMPedometer](https://developer.xamarin.com/api/type/CoreMotion.CMPedometer/)前景或背景 pedometer 事件註冊。

## <a name="foundation-enhancements"></a>Mfc 增強功能

對 iOS 10 的基礎架構，下列的增強功能所做的：

- 使用新[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)格式化當地語系化的度量單位，顯示給使用者的類別。
- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別，例如持續時間，來比較的間隔，並測試間隔交集的日期和時間間隔計算。
- 使用新[NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement)類別來轉換之間不同單位的量值 (UOM)，或在不同的 UOMs 中的值上執行計算。

- 使用新[NSUnit](https://developer.apple.com/reference/foundation/nsunit)並[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)類別，表示特定 UOMs。
- 已新增數個新屬性[NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別來取得本機資訊和可用的顯示格式。

## <a name="gamekit-enhancements"></a>GameKit 增強功能

在 iOS 10 GameKit framework，下列的增強功能所做的：

- **Game Center 應用程式**已被取代，並從 iOS 中移除。 如果應用程式使用 GameKit，它_必須_呈現它自己的介面，以顯示 GameKit 功能，例如排行榜等等。 
- 已實作新的僅限 iCloud 帳戶類型所[GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別。
- 新[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)類別管理在 Game Center 的持續性資料儲存體提供的通用的解決方案。 `GKGameSession` 維護一份清單的播放程式和應用程式會負責實作如何及何時儲存、 擷取或播放程式之間交換參與者的日期。 在許多情況下遊戲工作階段可以取代現有的回合式相符項目、 即時的相符項目或持續性方法儲存的遊戲。

## <a name="gameplaykit-enhancements"></a>GameplayKit 增強功能

在 iOS 10 GameplayKit framework，下列的增強功能所做的：

- 使用新[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)類別，以提供高效能、 自然尋找路徑。
- 程序的雜訊產生已新增，而且可用來增強自然尋找紋理中的真實性，加入真實性觀景窗移動並協助產生豐富的遊戲世界。
- 使用空間資料分割來分割的遊戲世界資料進行有效率的搜尋。
- 新的 Monte Carlo 策略家 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 已加入的詳盡的可能移動計算。
- 3D 支援已新增至現有的代理程式和使用新的路徑尋找行為[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)並[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)類別。
- 新[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)並[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)類別進行結合 GameplayKit 和 SpriteKit 比以往更容易。
- 已新增新的決策樹 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)並[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 來增強遊戲建置 AI。

## <a name="healthkit-enhancements"></a>HealthKit 增強功能

在 iOS 10 HealthKit framework，下列的增強功能所做的：

- 天氣類型已新增新的中繼資料索引鍵 (例如`HKWeatherConditionClear`並`HKWeatherConditionCloudy`) 和健身類型 (例如`HKWorkoutActivityTypeFlexibility`和`HKWorkoutActivityTypeWheelchairRunPace`) 已新增。
- 新`HKCDADocument`已新增類別來代表臨床文件架構 (CDA) 格式化文件。
- 使用新[HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration)類別，以指定`ActivityType`和`LocationType`的了。
- 新[HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject)並`WheelchairUse`方法[HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)類別已新增用於殘障人士相關健全狀況資料。

## <a name="homekit-enhancements"></a>HomeKit 增強功能

對 HomeKit 架構，在 iOS 10 中，下列的增強功能所做的：

- 已加入新的服務和特性。
- IPad 可以做為提供配件的遠端存取、 執行自動化觸發程序並啟用 HomeKit 中樞共用使用者權限設定。
- 已新增對數位相機和 doorbell 附屬應用程式的支援。
- 附屬應用程式已提供更多的內容和組態。

請參閱我們[HomeKit 簡介](~/ios/platform/homekit.md)文件的詳細資訊。

## <a name="metal-enhancements"></a>金屬的增強功能

在 iOS 10 裸機 framework，下列增強功能所做的：

- 現在可以使用 3D 應用程式和遊戲_鑲嵌式_來有效率地呈現複雜的場景和透過 GPU 的幾何。
- 提供更精細地控制資源配置，以最佳化效能的金屬為基礎的堆積使用資源的應用程式和 Memoryless 轉譯目標。
- 若要建立的 material 和場景光線的組合函式高度最佳化的集合中使用函式特製化。

若要進一步了解，請參閱 Apple [Metal 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

## <a name="modelio-enhancements"></a>ModelIO 增強功能

在 iOS 10 ModelIO framework，下列的增強功能所做的：

 - 現在支援美元檔案格式。
 - 帶正負號距離欄位已加入支援[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)類別。
 - 使用新`MDLLightProbeIrradianceDataSource`類別，以協助 Light 探查的放置。
 - 使用新`MDLMaterialPropertyGraph`類別，以輕鬆地支援執行階段變更模型。

## <a name="photos-enhancements"></a>相片的增強功能

相片 framework，在 iOS 10 中，下列的增強功能所做的：

- 使用[CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput)並[CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput)類別，以利用新的核心映像處理器功能，可執行編輯。
- 即時的相片編輯現已提供支援相片架構的應用程式和相片編輯擴充功能 (用於內部的相片和相機應用程式)。
- 使用新[PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext)類別，以同時適用於編輯影片和仍然 Live 相片的內容。

## <a name="replaykit-enhancements"></a>ReplayKit 增強功能

在 iOS 10 ReplayKit framework，下列的增強功能所做的：

- 使用[RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder)， [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller)並[RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller)類別，以支援廣播的記錄透過第 3 方的媒體站台。
- Broadcast UI 和廣播上傳延伸模組，才能支援 ReplayKit 第 3 方廣播的服務應用程式中。

## <a name="scenekit-enhancements"></a>SceneKit 增強功能

SceneKit framework，在 iOS 10 中，下列的增強功能所做的：

- [SCNCamera](xref:SceneKit.SCNCamera)類別可以使用 HDR 功能及特效，提供更高的真實性。 使用調適性的曝光度，來建立自動的效果或使用 vignetting、 fringing 色彩和色彩閱卷 fillmatic 效果加入遊戲。
- SceneKit 現在包含新的實際基礎呈現 (PBR) 系統，更簡單的資產編寫更實際的結果。
- 使用新[SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased)陰影模型產品各種不同具真實感的陰影效果的同時要求只有三個基本屬性 (`Diffuse`，`Metalness`和`Roughness`)。
- 因為 PBR 最陰影以環境為基礎的光源的運作方式，使用`LightingEnvironment`屬性，以將整個場景中的映像為基礎的光源。
- 使用`IESProfileURL`匯入定義光源的真實世界光線裝置的屬性，根據真實世界的值，例如強度 （以 lumens) 」 和 「 色溫 （以度為單位 Kelvin）。
- PBR 和 HDR 相機功能提供更好的結果，比傳統的呈現技術，如此一來，SceneKit 現在會執行所有色彩計算 （使用全彩裝置顯示器上的色域圖 P3） 的線性的色彩空間中。
- SceneKit 現在色彩符合所有色彩，請閱讀的色彩設定檔資訊。
- SceneKit 將解釋所有的著色器類型的線性 RGB 色彩空間中的色彩元件值。
- 線性兩者色彩空間轉譯及 wide 色彩可以藉由指定已停用`SCNDisableLinearSpaceRendering`並`SCNDisableWideGamut`的應用程式中的索引鍵`Info.plist`。
- 建立任意的多邊形靈長類動物 （無論是從檔案載入，或以程式設計方式產生） 來指定與新的幾何[SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon)類別。
- SceneKit 讀取，並調整紋理的影像中的色彩設定檔資訊，因為使用的所有映像的資產目錄以確保提供此資訊。

## <a name="spritekit-enhancements"></a>SpriteKit 的增強功能

SpriteKit framework，在 iOS 10 中，下列的增強功能所做的：

- 自訂著色器可以提供屬性 (`SKAttribute`)，藉由提供屬性值中使用著色器的每一個節點可以分別設定 (`SKAttributeValue`)。
- 2D、 2.5 D 和側邊捲動的遊戲使用 Tilemaps 現在支援的方形，六角等的並排顯示圖案`SKTileMapMode`， `SKTileGroup`，`SKTileGroupRule`和`SKTileSet`類別。
- 使用新`SKWarpGeometry`類別來自動縮放或扭曲[SKSpriteNode](https://developer.xamarin.com/api/type/SpriteKit.SKSpriteNode/)或是[SKEffectNode](https://developer.xamarin.com/api/type/SpriteKit.SKEffectNode/)轉譯。 新[SKAction](https://developer.xamarin.com/api/type/SpriteKit.SKAction/)類別可用來以動畫顯示 warp 效果之間的轉換。
- [SKView](https://developer.xamarin.com/api/type/SpriteKit.SKView/)類別提供數種新方法，來提供更細微的控制何時及如何呈現的場景。

## <a name="scrollview-enhancements"></a>ScrollView 增強功能

在 iOS 10.3 ScrollView 控制項，下列的增強功能所做的：

- `UIScrollView` 現在包含`IndexDisplayMode`屬性，來控制使用者可做為捲動時，如何顯示索引`UIScrollViewIndexDisplayMode`的：
    - `Automatic` -索引顯示是由作業系統控制。
    - `AlwaysHidden` -索引顯示一律會隱藏起來。

請參閱[iOSTenThree 範例](https://developer.xamarin.com/samples/monotouch/iOS10/iOSTenThree)使用量。

## <a name="uikit-enhancements"></a>UIKit 增強功能

若要在 iOS 10 的 UIKit 架構，下列增強功能所做的：

- 新[UIPasteboard](xref:UIKit.UIPasteboard) API 提供新的選項 （例如存留期限制），並會自動宣告類別上的常見的相容內容類型。
- 新的具全面互動性、 物件為基礎、 可中斷的動畫支援已加入，並可以連結至筆勢。 請參閱 Apple [UIViewAnimating 通訊協定參考](https://developer.apple.com/reference/uikit/uiviewanimating)， [UIViewPropertyAnimator 類別參考](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)， [UITimingCurveProvider 通訊協定參考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)， [UICubicTimingParameters 類別參考](https://developer.apple.com/reference/uikit/uicubictimingparameters)並[UISpringTimingParameter 類別參考](https://developer.apple.com/reference/uikit/uispringtimingparameters)如需詳細資訊。
- 新`UIPreviewInteraction`和`UIPreviewInteractionDelegate`允許開發人員應用程式，以查看和 pop 作業提供一種自訂介面。
- 新`UIAccessibilityCustomRotor`類別可讓應用程式提供自訂的特定內容的功能，例如 Voiceover 的輔助技術。
- 使用`UIAccessibilityIsAssistiveTouchRunning`和`UIAccessibilityAssistiveTouchStatusDidChangeNotification`符號來判斷是否已啟用 AssistiveTouch。
- 使用`UIAccessibilityHearingDevicePairedEar`和`UIAccessibilityHearingDevicePairedEarDidChangeNotification`符號，可取得的任何狀態配對 MFi 期待輔助工具。
- 若要支援動態型別在標籤中，文字欄位和文字方塊使用新`PreferredFontForTextStyle`方法的`UIFont`類別。
- 若要決定如果項目應該更新其字型時的裝置`UIContentSizeCategory`變更，使用`AdjustsFontForContentSizeCategory`屬性`UIContentSizeCategoryAdjusting`委派。
- `OpenURL`方法的`UIApplication`類別會以非同步方式呼叫，而且現在支援開啟動作完成後，會呼叫完成處理常式。
- 起始 CloudKit 共用，並修改其使用新的屬性`UICloudSharingController`和`UICloudSharingControllerDelegate`類別。
- 利用預先擷取的資料格，以改善的捲動體驗`UICollectionViews`與新`UICollectionViewDataSourcePrefetching`委派。
- 開發人員現在可以控制 （例如文字和背景色彩） 索引標籤列項目的徽章的外觀。
- 重新整理控制項現可支援所有的捲動檢視和捲軸檢視子類別 (例如`UICollectionView`)。

## <a name="webkit-enhancements"></a>WebKit 增強功能

WebKit framework，在 iOS 10 中，下列的增強功能所做的：

- 查看和 pop 的支援已新增至`WKWebView`類別。 使用`ShouldPreviewElement`方法，以判斷給定的 web 檢視是否應該顯示預覽。


## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://developer.xamarin.com/samples/ios/iOS10/)
- [在 iOS 10 最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
