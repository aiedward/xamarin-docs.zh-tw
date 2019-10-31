---
title: 其他 iOS 10 架構變更
description: 本檔說明在 iOS 10 中對現有架構所做的微小變更和增強功能，並討論如何在 Xamarin 中使用這些更新。
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: c25ea0878906b31028143ff1ad689db56b197458
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032260"
---
# <a name="additional-ios-10-frameworks-changes"></a>其他 iOS 10 架構變更

_本文涵蓋適用于 iOS 10 現有架構的其他、次要變更或增強功能。_

## <a name="av-foundation-framework-additions"></a>AV 基礎架構新增專案

AVFoundation 架構包含下列增強功能：

- 在 iOS 10 中，開發人員不再需要根據內容類型來執行不同的[AVPlayerItem](xref:AVFoundation.AVPlayerItem)行為。 只要設定 `Rate` 屬性，AVFoundation 就會判斷有足夠的內容可供播放而不停止。
- 新的[AVCapturePhotoOutput](xref:AVFoundation.AVCaptureFileOutput)類別會取代已被取代的 `AVCaptureStillImageOutput` 類別，並提供統一的方法來處理所有攝影工作流程，藉由提供複雜的控制和監視功能，並支援新功能，例如作為即時相片和原始捕捉格式。
- 新的 `AVPlayerLooper` 類別可讓您更輕鬆地在播放期間迴圈指定的媒體片段。
- `AVAssetDownloadURLSession` 類別可讓您下載及稍後播放 FairPlay 加密的 HLS 串流。
- 根據預設， [AVCaptureSession](xref:AVFoundation.AVCaptureSession)類別會在裝置硬體支援時，自動支援寬色彩的寬區捕捉。 如需詳細資訊，請參閱 Apple 的[IOS 裝置相容性參考](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599)。

## <a name="avkit-additions"></a>AVKit 新增專案

AVKit framework 現在包含新的 `UpdatesNowPlayingInfoCenter` 屬性，以指出何時應更新現在播放的資訊中心。

## <a name="core-data-enhancements"></a>核心資料增強功能

iOS 10 包含下列核心資料架構的增強功能：

- WAL 記錄模式中具有 SQLite 資料存放區的[NSManagedObjectCoNtext](xref:CoreData.NSManagedObjectContext)物件支援新的查詢產生功能，其中 Managed 物件內容（MOC）可以釘選到特定的資料庫版本，以供未來提取和錯誤交易使用。
- 根[NSManagedObjectCoNtext](xref:CoreData.NSManagedObjectContext)物件支援並行錯誤和未序列化的提取。
- [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator)類別會維護 SQLite 資料存放區的集區。
- 已新增數個新的便利方法，`NSManagedObject` 可讓您更輕鬆地執行提取和建立子類別。
- 使用高階 `NSPersistenceContainer` 來參考 `NSPersistentStoreCoordinator`、 [NSManagedObjectModel](xref:CoreData.NSManagedObjectModel)和其他核心資料設定資源。

如需詳細資訊，請參閱 Apple 的[核心資料架構參考](https://developer.apple.com/reference/coredata)。

## <a name="core-image-enhancements"></a>核心映射增強功能

iOS 10 對核心映射架構提供下列增強功能：

- 開發人員現在可以在處理之前和之後來回轉換色彩空間，以在核心影像內容的工作色彩空間之外的色彩空間中處理影像。
- 對於使用 A8 或 A9 Cpu 的 iOS 裝置，現在支援原始影像格式。 核心映射現在支援從內建的 iSight 攝影機或從協力廠商攝影機解碼原始影像。 使用 [CIFilter](xref:CoreImage.CIFilter) 類別`FilterWithImageURL`的或方法來處理原始影像`FilterWithImageData` 。
- 在 `UIImageView` 物件中，已對 `UIImage` 轉譯（以核心映射映射存放區支援）進行數個轉譯效能增強功能。 
- 標記為寬範圍的 `UIImage` 物件將會在支援寬色彩的 iOS 裝置上，于 `UIImageView` 物件中轉譯為寬範圍色彩。
- 核心影像核心程式代碼現在可以要求特定圖元輸出格式。
- [CIFilter](xref:CoreImage.CIFilter)類別的 `ImageWithExtent` 方法可以用來將自訂處理插入篩選作業中。 核心映射會在處理輸出或顯示的影像時，叫用篩選之間的指定回呼。

此外，已新增下列新的核心映射篩選：

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>核心動作新增

第一次使用 iOS 10 時，核心動作架構包含計步器事件，可讓應用程式在執行時，接收使用者暫停和繼續追蹤的快速、即時通知。 使用[CMPedometer](xref:CoreMotion.CMPedometer)來註冊前景或背景計步器事件。

## <a name="foundation-enhancements"></a>基礎增強功能

適用于 iOS 10 的基礎架構已進行下列增強功能：

- 使用新的[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)類別來格式化當地語系化的量測測量，以顯示給終端使用者。
- 使用新的[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別來進行日期和時間間隔計算（例如持續期間），以比較間隔和測試間隔交集。
- 使用新的[NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement)類別，在不同的測量單位（UOM）之間進行轉換，或在不同 UOMs 的值上執行計算。

- 使用新的[NSUnit](https://developer.apple.com/reference/foundation/nsunit)和[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)類別來代表特定 UOMs。
- 已將數個新的屬性新增至[NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別，以取得本機資訊和可用的顯示格式。

## <a name="gamekit-enhancements"></a>GameKit 增強功能

IOS 10 中的 GameKit 架構已進行下列增強功能：

- **Game Center 應用程式**已被取代，並已從 iOS 移除。 如果應用程式使用 GameKit，它_必須_提供自己的介面來顯示 GameKit 功能，例如排行榜等。 
- [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別已實作為僅限 iCloud 的新帳戶類型。
- 新的[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)類別提供通用的解決方案來管理 Game Center 上的持續性資料存放區。 `GKGameSession` 會維護一份玩家清單，而應用程式會負責執行在玩家之間儲存、抓取或交換參與者日期的方式和時機。 在許多情況下，遊戲會話可以取代現有的回合型比對、即時符合或持續的遊戲儲存方法。

## <a name="gameplaykit-enhancements"></a>GameplayKit 增強功能

IOS 10 中的 GameplayKit 架構已進行下列增強功能：

- 使用新的[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)類別來提供高效能、自然外觀的路徑。
- 已新增程式產生雜訊，並可用來增強自然外觀材質的真實性、增加攝影機移動的真實感，並協助產生豐富的遊戲世界。
- 使用空間分割來分割遊戲世界的資料，以進行有效率的搜尋。
- 已加入新的 Monte Carlo 策略家（[GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)），以取得徹底可能的移動計算。
- 已使用新的[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)和[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)類別，將3d 支援新增至現有的代理程式和路徑尋找行為。
- 新的[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)和[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)類別結合了 GameplayKit 和 SpriteKit，使其比以往更容易。
- 新的決策樹 API 已新增（[GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)和[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)），以增強遊戲建立 AI。

## <a name="healthkit-enhancements"></a>HealthKit 增強功能

IOS 10 中的 HealthKit 架構已進行下列增強功能：

- 已新增新的中繼資料索引鍵給氣象類型（例如 `HKWeatherConditionClear` 和 `HKWeatherConditionCloudy`）和健身類型（例如 `HKWorkoutActivityTypeFlexibility` 和 `HKWorkoutActivityTypeWheelchairRunPace`）。
- 已新增新的 `HKCDADocument` 類別，以代表臨床檔架構（CDA）格式的檔。
- 使用新的[HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration)類別來指定健身的 `ActivityType` 和 `LocationType`。
- 已新增[HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore)類別的新[HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject)和 `WheelchairUse` 方法，以使用輪椅相關的健全狀況資料。

## <a name="homekit-enhancements"></a>HomeKit 增強功能

IOS 10 中的 HomeKit 架構已進行下列增強功能：

- 已新增服務和特性。
- 您可以設定 iPad 做為 HomeKit 中樞，以提供遠端存取許可權、執行自動化觸發程式，以及啟用共用使用者權限。
- 已針對相機和 doorbell 配件新增支援。
- 為配件提供了更多的內容和設定。

如需詳細資訊，請參閱我們的[HomeKit 簡介](~/ios/platform/homekit.md)檔。

## <a name="metal-enhancements"></a>金屬增強功能

IOS 10 中的金屬架構已進行下列增強功能：

- 3D 應用程式和遊戲現在可以使用_鑲嵌_式，透過 GPU 有效率地呈現複雜的場景和幾何。
- 使用資源堆積和 Memoryless 轉譯目標，提供更精細的資源配置控制，以將金屬型應用程式的效能優化。
- 使用函數特製化，為場景建立高度優化的材質和光源組合功能集合。

若要深入瞭解，請參閱 Apple 的[金屬程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

## <a name="modelio-enhancements"></a>ModelIO 增強功能

IOS 10 中的 ModelIO 架構已進行下列增強功能：

- 現在支援 USD 檔案格式。
- 已將已簽署的距離欄位支援新增至[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)類別。
- 使用新的 `MDLLightProbeIrradianceDataSource` 類別來協助進行較少的探查位置。
- 使用新的 `MDLMaterialPropertyGraph` 類別，輕鬆地支援模型的執行時間變更。

## <a name="photos-enhancements"></a>相片增強功能

IOS 10 中的相片架構已進行下列增強功能：

- 使用[CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput)和[CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput)類別，利用新的核心映射處理器功能來執行編輯。
- 即時相片編輯現在適用于支援相片架構和相片編輯延伸模組（在相片和相機應用程式內使用）的應用程式。
- 使用新的[PHLivePhotoEditingCoNtext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext)類別，將編輯同時套用至影片，同時套用至即時相片的內容。

## <a name="replaykit-enhancements"></a>ReplayKit 增強功能

IOS 10 中的 ReplayKit 架構已進行下列增強功能：

- 使用[RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder)、 [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller)和[RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller)類別來支援透過協力廠商網站廣播錄製的媒體。
- 需要廣播 UI 和 Broadcast Upload 延伸模組，才能在應用程式中支援 ReplayKit 協力廠商廣播服務。

## <a name="scenekit-enhancements"></a>SceneKit 增強功能

IOS 10 中的 SceneKit 架構已進行下列增強功能：

- [SCNCamera](xref:SceneKit.SCNCamera)類別可使用 HDR 特性和效果來提供更高的真實性。 使用調適型曝光來建立自動效果，或使用 vignetting、色彩 fringing 和色彩評分，將 fillmatic 效果新增到遊戲中。
- SceneKit 現在包含新的實際轉譯（.PBR）系統，可提供更簡單的資產製作，以獲得更實際的結果。
- 使用新的[SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased)陰影模型來組成各式各樣的實際陰影效果，同時只需要三個基本屬性（`Diffuse`、`Metalness` 和 `Roughness`）。
- 由於 .PBR 陰影最適合使用以環境為基礎的光源，因此，請使用 `LightingEnvironment` 屬性將影像型光源指派給整個場景。
- 使用 [`IESProfileURL`] 屬性來匯入真實世界的光線裝置，其會根據真實世界的值來定義光源，例如濃度（流明）和色溫度（以度為單位）。
- 與傳統轉譯技術相比，.PBR 和 HDR 攝影機功能提供更好的結果，因此，SceneKit 現在會線上性色彩空間中執行所有色彩計算（在寬色彩裝置顯示器上使用 P3 色彩色域）。
- [立即 SceneKit] 色彩會藉由閱讀色彩設定檔資訊來符合所有色彩。
- SceneKit 會針對所有著色器類型，以線性 RGB 色彩空間來解讀色彩元件值。
- 您可以藉由在應用程式的 `Info.plist`中指定 `SCNDisableLinearSpaceRendering` 和 `SCNDisableWideGamut` 金鑰，來停用線性色彩空間呈現和寬色彩。
- 建立任意多邊形 primates （從檔案載入或以程式設計方式產生），以使用新的[SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon)類別來指定幾何。
- 由於 SceneKit 會讀取和調整材質影像中的色彩設定檔資訊，因此請針對所有影像使用資產目錄，以確保提供這項資訊。

## <a name="spritekit-enhancements"></a>SpriteKit 增強功能

IOS 10 中的 SpriteKit 架構已進行下列增強功能：

- 自訂著色器可以提供屬性（`SKAttribute`），您可以藉由提供屬性值（`SKAttributeValue`），分別由使用著色器的每個節點加以設定。
- Tilemaps 現在支援使用 `SKTileMapMode`、`SKTileGroup`、`SKTileGroupRule` 和 `SKTileSet` 類別的2D、2.5 D 和側邊滾動遊戲的正方形、六邊形和等式磚圖形。
- 使用新的 `SKWarpGeometry` 類別來延展或扭曲[SKSpriteNode](xref:SpriteKit.SKSpriteNode)或[SKEffectNode](xref:SpriteKit.SKEffectNode)轉譯。 新的[SKAction](xref:SpriteKit.SKAction)類別可以用來建立變形效果之間的轉換動畫。
- [SKView](xref:SpriteKit.SKView)類別提供數個新的方法，可讓您更精細地控制場景的呈現時機和方式。

## <a name="scrollview-enhancements"></a>ScrollView 增強功能

IOS 10.3 中的 ScrollView 控制項已進行下列增強功能：

- `UIScrollView` 現在包含 `IndexDisplayMode` 屬性，以控制當使用者滾動為 `UIScrollViewIndexDisplayMode` 時，如何顯示索引：
  - `Automatic`-索引顯示由作業系統控制。
  - `AlwaysHidden`-索引顯示一律會隱藏。

如需使用方式，請參閱[IOSTenThree 範例](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-iostenthree)。

## <a name="uikit-enhancements"></a>UIKit 增強功能

IOS 10 中的 UIKit 架構已進行下列增強功能：

- 新的[UIPasteboard](xref:UIKit.UIPasteboard) API 會提供新的選項（例如存留期限制），並會自動為一般類別類型宣告相容的內容類型。
- 新增完全互動式、以物件為基礎、可中斷的動畫支援，並且可以連結到筆勢。 請參閱 Apple 的[UIViewAnimating 通訊協定參考](https://developer.apple.com/reference/uikit/uiviewanimating)、 [UIViewPropertyAnimator 類別參考](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)、 [UITimingCurveProvider 通訊協定參考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)、 [UICubicTimingParameters 類別參考](https://developer.apple.com/reference/uikit/uicubictimingparameters)和[UISpringTimingParameter ](https://developer.apple.com/reference/uikit/uispringtimingparameters)如需詳細資訊, 類別參考。
- 新的 `UIPreviewInteraction` 和 `UIPreviewInteractionDelegate` 可讓開發人員應用程式提供查看和 pop 作業的自訂介面。
- 新的 `UIAccessibilityCustomRotor` 類別可讓應用程式提供自訂的內容特定功能給輔助技術，例如 Voice。
- 使用 `UIAccessibilityIsAssistiveTouchRunning` 和 `UIAccessibilityAssistiveTouchStatusDidChangeNotification` 符號來判斷是否已啟用 AssistiveTouch。
- 使用 `UIAccessibilityHearingDevicePairedEar` 和 `UIAccessibilityHearingDevicePairedEarDidChangeNotification` 符號來取得任何配對 MFi 聽覺輔助工具的狀態。
- 若要在標籤中支援動態類型，文字欄位和文字方塊會使用 `UIFont` 類別的新 `PreferredFontForTextStyle` 方法。
- 若要決定當裝置的 `UIContentSizeCategory` 變更時，元素是否應該更新其字型，請使用 `UIContentSizeCategoryAdjusting` 委派的 `AdjustsFontForContentSizeCategory` 屬性。
- `UIApplication` 類別的 `OpenURL` 方法是以非同步方式呼叫，現在支援在開啟動作完成後呼叫的完成處理常式。
- 使用新的 `UICloudSharingController` 和 `UICloudSharingControllerDelegate` 類別，起始 CloudKit 共用並修改其屬性。
- 利用預先提取的資料格，以新的 `UICollectionViewDataSourcePrefetching` 委派來改善 `UICollectionViews` 的滾動體驗。
- 開發人員現在可以控制索引標籤欄專案的徽章外觀（例如文字和背景色彩）。
- 所有捲軸和捲軸子類別中現在都支援重新整理控制項（例如 `UICollectionView`）。

## <a name="webkit-enhancements"></a>WebKit 增強功能

IOS 10 中的 WebKit 架構已進行下列增強功能：

- 已將查看和 pop 支援新增至 `WKWebView` 類別。 使用 `ShouldPreviewElement` 方法來判斷指定的 web view 是否應顯示預覽。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
- [IOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
