---
title: 其他 iOS 10 架構變更
description: 本檔說明在 iOS 10 中對現有架構所做的微小變更和增強功能，並討論如何在 Xamarin 中使用這些更新。
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: b5dbfe4de21cd359e89d84db310a1e1905ddbb1a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434592"
---
# <a name="additional-ios-10-frameworks-changes"></a>其他 iOS 10 架構變更

_本文涵蓋適用于 iOS 10 之現有 framework 的其他、次要變更或增強功能。_

## <a name="av-foundation-framework-additions"></a>新增 AV 基礎架構

AVFoundation 架構包含下列增強功能：

- 在 iOS 10 中，開發人員不再需要根據內容類型來執行不同的 [AVPlayerItem](xref:AVFoundation.AVPlayerItem) 行為。 只要設定 `Rate` 屬性，AVFoundation 就會判斷是否有足夠的內容可在沒有拖延的情況下播放。
- 新的 [AVCapturePhotoOutput](xref:AVFoundation.AVCaptureFileOutput) 類別會取代已被取代的 `AVCaptureStillImageOutput` 類別，並提供整合的方法來處理所有攝影工作流程，方法是提供完善的控制和監視功能，並支援即時相片和原始的捕捉格式等新功能。
- 新 `AVPlayerLooper` 類別可讓您更輕鬆地在播放期間迴圈指定的媒體。
- `AVAssetDownloadURLSession`類別可讓您下載及稍後播放 FairPlay 加密的 HLS 資料流程。
- 根據預設， [AVCaptureSession](xref:AVFoundation.AVCaptureSession) 類別會在裝置硬體支援時自動支援寬色彩的寬範圍捕捉。 如需詳細資訊，請參閱 Apple 的 [IOS 裝置相容性參考](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) 。

## <a name="avkit-additions"></a>AVKit 新增專案

AVKit framework 現在包含新的 `UpdatesNowPlayingInfoCenter` 屬性，以指出何時應更新立即播放資訊中心。

## <a name="core-data-enhancements"></a>核心資料增強功能

iOS 10 包含下列核心資料架構的增強功能：

- 在 WAL 記錄模式中具有 SQLite 資料存放區的 [NSManagedObjectCoNtext](xref:CoreData.NSManagedObjectContext) 物件，可支援新的查詢產生功能，其中的 Managed 物件內容 (MOC) 可以釘選到特定的資料庫版本，以供未來提取和錯誤交易之用。
- 根 [NSManagedObjectCoNtext](xref:CoreData.NSManagedObjectContext) 物件支援並行錯誤和未序列化的提取。
- [NSPersistentStoreCoordinator](xref:CoreData.NSPersistentStoreCoordinator)類別會維護 SQLite 資料存放區的集區。
- 已加入數個新的便利方法，可 `NSManagedObject` 讓您更輕鬆地執行提取和建立子類別。
- 使用高階 `NSPersistenceContainer` 參考 `NSPersistentStoreCoordinator` 、 [NSManagedObjectModel](xref:CoreData.NSManagedObjectModel) 和其他核心資料設定資源。

如需詳細資訊，請參閱 Apple 的 [核心資料架構參考](https://developer.apple.com/reference/coredata)。

## <a name="core-image-enhancements"></a>核心映射增強功能

iOS 10 對核心映射架構進行下列增強：

- 開發人員現在可以在處理之前和之後來回轉換色彩空間，在核心影像內容的工作色彩空間之外的色彩空間中處理影像。
- 若是使用 A8 或 A9 Cpu 的 iOS 裝置，現在支援原始影像格式。 核心映射現在可支援從內建的 iSight 攝影機或從協力廠商相機解碼原始影像。 使用 `FilterWithImageData` `FilterWithImageURL` [CIFilter](xref:CoreImage.CIFilter) 類別的或方法來處理原始影像。
- 有幾項轉譯效能增強功能，可讓您 `UIImage` 在由核心影像映射存放區) 的物件中受到核心的支援時，呈現 (`UIImageView` 。
- `UIImage` 標記寬範圍的物件會在 `UIImageView` 支援寬色彩的 iOS 裝置上，以寬範圍色彩呈現。
- 核心影像核心程式代碼現在可以要求特定的圖元輸出格式。
- `ImageWithExtent` [CIFilter](xref:CoreImage.CIFilter)類別的方法可以用來將自訂處理插入篩選作業中。 核心影像會在處理影像以供輸出或顯示時，在篩選之間叫用指定的回呼。

此外，已新增下列新的核心映射篩選器：

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>新增核心動畫

最新的 iOS 10，核心動作架構包含 pedometer 事件，可讓應用程式在執行時，接收使用者暫停和繼續追蹤的快速即時通知。 使用 [CMPedometer](xref:CoreMotion.CMPedometer) 來註冊前景或背景 pedometer 事件。

## <a name="foundation-enhancements"></a>基礎增強功能

適用于 iOS 10 的基礎架構已進行下列增強功能：

- 使用新的 [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) 類別來格式化當地語系化的量值，以顯示給終端使用者。
- 使用新的 [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) 類別來進行日期和時間間隔計算（例如持續時間），以比較間隔和測試間隔交集。
- 使用新的 [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) 類別，在不同的測量單位之間進行轉換 (UOM) 或在不同 UOMs 的值上執行計算。

- 使用新的 [NSUnit](https://developer.apple.com/reference/foundation/nsunit) 和 [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) 類別來代表特定的 UOMs。
- [NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別已新增幾個新的屬性，以取得本機資訊和可用的顯示格式。

## <a name="gamekit-enhancements"></a>GameKit 增強功能

IOS 10 中的 GameKit 架構已進行下列增強功能：

- **Game Center 的應用程式**已被取代，並已從 iOS 移除。 如果應用程式使用 GameKit，它 _必須_ 有自己的介面，以顯示 GameKit 功能，例如排行榜等等。
- [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別已實作為新的 iCloud 帳戶類型。
- 新的 [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) 類別提供通用的解決方案，可用來管理 Game Center 上的持續性資料存放區。 `GKGameSession` 會維護播放程式的清單，而應用程式會負責執行在播放程式之間的儲存、取出或交換的方式和時間。 在許多情況下，遊戲會話可以取代現有的回合型相符專案、即時比對或持續性的遊戲儲存方法。

## <a name="gameplaykit-enhancements"></a>GameplayKit 增強功能

IOS 10 中的 GameplayKit 架構已進行下列增強功能：

- 使用新的 [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) 類別，以提供高效能且自然的路徑。
- 已新增程式性雜訊產生，可用來增強自然的材質中的真實感、增加相機移動的真實感，以及協助產生豐富的遊戲世界。
- 使用空間分割來分割遊戲世界資料，以進行有效率的搜尋。
- 已新增新的 Monte Carlo 策略家 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) ，以進行徹底的移動計算。
- 已使用新的 [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) 和 [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) 類別，將3d 支援新增至現有的代理程式和路徑尋找行為。
- 新的 [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) 和 [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) 類別讓 GameplayKit 和 SpriteKit 的結合比以往更容易。
- 新的決策樹 API 已新增 ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) 和 [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) ，以增強遊戲建立 AI。

## <a name="healthkit-enhancements"></a>HealthKit 增強功能

IOS 10 中的 HealthKit 架構已進行下列增強功能：

- 已新增新的中繼資料金鑰給氣象型別 (例如 `HKWeatherConditionClear` 和 `HKWeatherConditionCloudy`) 和測驗類型 (例如 `HKWorkoutActivityTypeFlexibility` 和 `HKWorkoutActivityTypeWheelchairRunPace`) 已加入。
- 已 `HKCDADocument` 新增類別以代表臨床檔架構 (CDA) 格式化的檔。
- 使用新的 [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) 類別來指定 `ActivityType` 測驗的和 `LocationType` 。
- 已新增 [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) 和 `WheelchairUse` [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) 類別的方法，以使用輪椅相關的健康情況資料。

## <a name="homekit-enhancements"></a>HomeKit 增強功能

IOS 10 中的 HomeKit 架構已進行下列增強功能：

- 已新增新的服務和特性。
- IPad 可以設定為 HomeKit 中樞，以提供遠端配件存取、執行自動化觸發程式，以及啟用共用的使用者許可權。
- 已為相機和門鈴配件新增支援。
- 為配件提供了更多的內容和設定。

如需詳細資訊，請參閱 HomeKit 檔的 [簡介](~/ios/platform/homekit.md) 。

## <a name="metal-enhancements"></a>金屬增強功能

IOS 10 中的裸機架構已進行下列增強功能：

- 3D 應用程式和遊戲現在可以使用 _鑲嵌_ 來有效率地透過 GPU 呈現複雜的場景和幾何。
- 提供更精細的資源配置控制，以使用資源堆積和 Memoryless 轉譯目標，將裸機型應用程式的效能優化。
- 使用函數特製化，為場景建立高度優化的材質和輕量組合函式集合。

若要深入瞭解，請參閱 Apple 的 [裸機程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

## <a name="modelio-enhancements"></a>ModelIO 增強功能

IOS 10 中的 ModelIO 架構已進行下列增強功能：

- 現在支援 USD 檔案格式。
- 已將已簽署的距離欄位支援新增至 [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) 類別。
- 使用新的 `MDLLightProbeIrradianceDataSource` 類別來協助進行輕量探查放置。
- 使用新 `MDLMaterialPropertyGraph` 類別可輕鬆支援模型的執行時間變更。

## <a name="photos-enhancements"></a>相片增強功能

IOS 10 中的相片架構已進行下列增強功能：

- 使用 [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) 和 [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) 類別，以利用新的核心映射處理器功能來執行編輯。
- 即時照片編輯現在適用于支援相片架構的應用程式，以及相片編輯延伸模組 (可在相片和相機應用程式) 內使用。
- 使用新的 [PHLivePhotoEditingCoNtext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) 類別，將編輯套用至影片，並同時套用至即時相片的內容。

## <a name="replaykit-enhancements"></a>ReplayKit 增強功能

IOS 10 中的 ReplayKit 架構已進行下列增強功能：

- 使用 [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder)、 [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) 和 [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) 類別，以支援透過協力廠商網站廣播錄製的媒體。
- 需要廣播 UI 和 Broadcast Upload 擴充功能，才能在應用程式中支援 ReplayKit 協力廠商廣播服務。

## <a name="scenekit-enhancements"></a>SceneKit 增強功能

IOS 10 中的 SceneKit 架構已進行下列增強功能：

- [SCNCamera](xref:SceneKit.SCNCamera)類別可以使用 HDR 功能和效果來提供更高的真實性。 使用調適型曝光來建立自動效果，或使用 vignetting、色彩 fringing 和色彩來將 fillmatic 效果新增到遊戲中。
- SceneKit 現在包含新的實體型轉譯 (.PBR) 系統，以較簡單的資產撰寫來提供更真實的結果。
- 使用新的 [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) 陰影模型來產生各式各樣的真實陰影效果，同時只需要三個基本屬性 (`Diffuse` `Metalness` 和 `Roughness`) 。
- 由於 .PBR 陰影與以環境為基礎的光源效果最佳，所以請使用屬性將以 `LightingEnvironment` 影像為基礎的光源指派給整個場景。
- 您 `IESProfileURL` 可以使用屬性，根據真實世界的值來匯入定義光源的真實世界，例如 (的亮度) 和顏色溫度 (以度數為單位) 。
- .PBR 和 HDR 攝影機的功能提供比傳統轉譯技術更好的結果，因此，SceneKit 現在會線上性色彩空間中執行所有色彩計算 (使用單色裝置上的 P3 色彩範圍顯示) 。
- SceneKit 現在的色彩會藉由讀取色彩設定檔資訊來符合所有色彩。
- SceneKit 會以線性 RGB 色彩空間來解讀所有著色器類型的色彩元件值。
- 您可以 `SCNDisableLinearSpaceRendering` 在應用程式中指定和索引鍵，以停用線性色彩空間轉譯和寬色彩 `SCNDisableWideGamut` `Info.plist` 。
- 建立任意多邊形 primates (從檔案載入或以程式設計方式產生) ，以使用新的 [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/documentation/scenekit/scngeometryprimitivetype/scngeometryprimitivetypepolygon) 類別來指定幾何。
- 由於 SceneKit 會讀取及調整材質影像中的色彩設定檔資訊，請使用所有影像的資產目錄，以確保提供這項資訊。

## <a name="spritekit-enhancements"></a>SpriteKit 增強功能

IOS 10 中的 SpriteKit 架構已進行下列增強功能：

- 自訂著色器可以提供屬性 (`SKAttribute`) ，而這些屬性可由使用著色器的每個節點個別設定，方法是提供 () 的屬性值 `SKAttributeValue` 。
- Tilemaps 現在支援使用 `SKTileMapMode` 、 `SKTileGroup` `SKTileGroupRule` 和類別之2d、2.5 d 和側邊滾動遊戲的正方形、六邊形和等位磚圖形 `SKTileSet` 。
- 使用新 `SKWarpGeometry` 類別來延展或扭曲 [SKSpriteNode](xref:SpriteKit.SKSpriteNode) 或 [SKEffectNode](xref:SpriteKit.SKEffectNode) 轉譯。 新的 [SKAction](xref:SpriteKit.SKAction) 類別可用來將變形效果之間的轉換動畫。
- [SKView](xref:SpriteKit.SKView)類別提供數個新方法，可讓您更精細地控制場景的呈現時間和方式。

## <a name="scrollview-enhancements"></a>ScrollView 增強功能

IOS 10.3 中的 ScrollView 控制項已進行下列增強功能：

- `UIScrollView` 現在包含 `IndexDisplayMode` 屬性，以控制當使用者將滾動為時，如何顯示索引 `UIScrollViewIndexDisplayMode` ：
  - `Automatic` -索引顯示由作業系統控制。
  - `AlwaysHidden` -索引顯示一律為隱藏。

如需使用方式，請參閱 [IOSTenThree 範例](/samples/xamarin/ios-samples/ios10-iostenthree) 。

## <a name="uikit-enhancements"></a>UIKit 增強功能

IOS 10 中的 UIKit 架構已進行下列增強功能：

- 新的 [UIPasteboard](xref:UIKit.UIPasteboard) API 會提供新的選項 (例如) 的存留期限制，並且會自動宣告常見類別類型的相容內容類型。
- 加入了新的互動式、物件型、可中斷的動畫支援，而且可以連結到筆勢。 如需詳細資訊，請參閱 Apple 的 [UIViewAnimating 通訊協定參考](https://developer.apple.com/reference/uikit/uiviewanimating)、 [UIViewPropertyAnimator 類別參考](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)、 [UITimingCurveProvider 通訊協定參考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)、 [UICubicTimingParameters 類別參考](https://developer.apple.com/reference/uikit/uicubictimingparameters) 和 [UISpringTimingParameter 類別參考](https://developer.apple.com/reference/uikit/uispringtimingparameters) 。
- 新的 `UIPreviewInteraction` 和 `UIPreviewInteractionDelegate` 可讓開發人員應用程式提供用於查看和 pop 作業的自訂介面。
- 新 `UIAccessibilityCustomRotor` 類別可讓應用程式針對輔助技術（例如語音）提供自訂的內容特定功能。
- 使用 `UIAccessibilityIsAssistiveTouchRunning` 和 `UIAccessibilityAssistiveTouchStatusDidChangeNotification` 符號來判斷是否已啟用 AssistiveTouch。
- 使用 `UIAccessibilityHearingDevicePairedEar` 和 `UIAccessibilityHearingDevicePairedEarDidChangeNotification` 符號來取得任何配對 MFi 聽力輔助的狀態。
- 若要在標籤中支援動態類型，文字欄位和文字方塊會使用類別的新 `PreferredFontForTextStyle` 方法 `UIFont` 。
- 若要決定元素是否應該在裝置變更時更新其字型 `UIContentSizeCategory` ，請使用 `AdjustsFontForContentSizeCategory` 委派的屬性 `UIContentSizeCategoryAdjusting` 。
- `OpenURL`類別的方法 `UIApplication` 會以非同步方式呼叫，現在支援在開啟的動作完成後呼叫的完成處理常式。
- 使用新的和類別，起始 CloudKit 共用和修改其屬性 `UICloudSharingController` `UICloudSharingControllerDelegate` 。
- 利用預先提取的儲存格，利用新的委派改善的滾動體驗 `UICollectionViews` `UICollectionViewDataSourcePrefetching` 。
- 開發人員現在可以控制索引標籤欄專案的徽章外觀 (例如文字和背景色彩) 。
- 所有捲軸視圖和捲軸 (（例如) ）現在都支援重新整理控制項 `UICollectionView` 。

## <a name="webkit-enhancements"></a>WebKit 增強功能

IOS 10 中的 WebKit 架構已進行下列增強功能：

- 已將預覽和 pop 支援新增至 `WKWebView` 類別。 您 `ShouldPreviewElement` 可以使用方法來判斷指定的網頁視圖是否應該顯示預覽。

## <a name="related-links"></a>相關連結

- [iOS 10 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [IOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)