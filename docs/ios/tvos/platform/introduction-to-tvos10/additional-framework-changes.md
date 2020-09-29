---
title: 其他 tvOS 10 Framework 變更
description: 本檔說明在 iOS 10 中對現有架構所做的微小變更和增強功能。 其中討論 AVFoundation、AVKit、核心資料、核心圖形、Foundation、GameKit、GameplayKit 等的更新。
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 4eb81a074adb6d2b828bb74edfde9916d69960b1
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434975"
---
# <a name="additional-tvos-10-frameworks-changes"></a>其他 tvOS 10 Framework 變更

除了 tvOS 的重大變更之外，Apple 還針對 tvOS 10 中的數個現有架構進行了修改和改進。

<a name="AV-Foundation-Framework"></a>

## <a name="avfoundation-framework-additions"></a>AVFoundation Framework 新增專案

AVFoundation 架構包含下列增強功能：

- 在 tvOS 10 中，應用程式不再根據內容類型來實行不同的 [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) 行為。 只要設定 `Rate` 屬性，AVFoundation 就會判斷是否有足夠的內容可在沒有拖延的情況下播放。
- 新 `AVPlayerLooper` 類別可讓您更輕鬆地在播放期間迴圈指定的媒體。

<a name="AVKit-Framework-Enhancements"></a>

## <a name="avkit-framework-enhancements"></a>AVKit 架構的增強功能

AVKit 架構包含下列增強功能：

- 應用程式現在可以控制 [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) 的跳過行為，因此略過手勢可能會移至播放清單中的下一個專案，或在目前的專案內前進。

<a name="Core-Data-Enhancements"></a>

## <a name="core-data-enhancements"></a>核心資料增強功能

tvOS 10 包含下列核心資料架構的增強功能：

- 根 [NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) 物件支援並行錯誤和未序列化的提取。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別會維護 SQLite 資料存放區的集區。
- 在 WAL 記錄模式中具有 SQLite 資料存放區的 [NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) 物件，可支援新的查詢產生功能，其中的 Managed 物件內容 (MOC) 可以釘選到特定的資料庫版本，以供未來提取和錯誤交易之用。
- 使用高階 `NSPersistenceContainer` 參考 `NSPersistentStoreCoordinator` 、 [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) 和其他核心資料設定資源。
- 已加入數個新的便利方法，可 `NSManagedObject` 讓您更輕鬆地執行提取和建立子類別。

如需詳細資訊，請參閱 Apple 的 [核心資料架構參考](https://developer.apple.com/reference/coredata)。

<a name="Core-Graphics-Enhancements"></a>

## <a name="core-graphics-enhancements"></a>核心圖形增強功能

tvOS 10 包含下列核心圖形架構的增強功能：

- 新的 CGColorConverterRef 類別可以用來執行一連串的色彩轉換。

<a name="Core-Image-Enhancements"></a>

## <a name="core-image-enhancements"></a>核心映射增強功能

tvOS 10 對核心映射架構進行下列增強：

- `ImageWithExtent` [CIFilter](https://developer.apple.com/reference/coreimage/cifilter)類別的方法可以用來將自訂處理插入篩選作業中。 核心影像會在處理影像以供輸出或顯示時，在篩選之間叫用指定的回呼。
- 應用程式現在可以在處理之前和之後來回轉換色彩空間，在核心影像內容的工作色彩空間之外的色彩空間中處理影像。
- 有幾項轉譯效能增強功能，可讓您 `UIImage` 在由核心影像映射存放區) 的物件中受到核心的支援時，呈現 (`UIImageView` 。
- `UIImage` 標記寬範圍的物件會在 `UIImageView` 支援寬色彩的 iOS 裝置上，以寬範圍色彩呈現。
- 核心影像核心程式代碼現在可以要求特定的圖元輸出格式。

此外，已新增下列新的核心映射篩選器：

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

<a name="Foundation-Enhancements"></a>

## <a name="foundation-enhancements"></a>基礎增強功能

TvOS 10 的基礎架構已進行下列增強功能：

- 使用新的 [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) 類別來進行日期和時間間隔計算（例如持續時間），以比較間隔和測試間隔交集。
- [NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別已新增幾個新的屬性，以取得本機資訊和可用的顯示格式。
- 使用新的 [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) 類別，在不同的測量單位之間進行轉換 (UOM) 或在不同 UOMs 的值上執行計算。
- 使用新的 [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) 類別來格式化當地語系化的量值，以顯示給終端使用者。
- 使用新的 [NSUnit](https://developer.apple.com/reference/foundation/nsunit) 和 [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) 類別來代表特定的 UOMs。

<a name="GameKit-Enhancements"></a>

## <a name="gamekit-enhancements"></a>GameKit 增強功能

TvOS 10 中的 GameKit 架構已進行下列增強功能：

- [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別已實作為新的 iCloud 帳戶類型。
- 新的 [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) 類別提供通用的解決方案，可用來管理 Game Center 上的持續性資料存放區。 `GKGameSession` 會維護播放程式的清單，而應用程式會負責實作為參與者日期的儲存、抓取或在播放程式之間交換的方式和時間。 在許多情況下，遊戲會話可以取代現有的回合型相符專案、即時比對或持續性的遊戲儲存方法。

<a name="GameplayKit-Enhancements"></a>

## <a name="gameplaykit-enhancements"></a>GameplayKit 增強功能

TvOS 10 中的 GameplayKit 架構已進行下列增強功能：

- 已新增程式性雜訊產生，可用來增強自然的材質中的真實感、增加相機移動的真實感，以及協助產生豐富的遊戲世界。
- 使用空間分割來分割遊戲世界資料，以進行有效率的搜尋。
- 已新增新的 Monte Carlo 策略家 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) ，以進行徹底的移動計算。
- 新的決策樹 API 已新增 ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) 和 [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) ，以增強遊戲建立 AI。
- 已使用新的 [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) 和 [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) 類別，將3d 支援新增至現有的代理程式和路徑尋找行為。
- 使用新的 [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) 類別，以提供高效能且自然的路徑。
- 新的 [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) 和 [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) 類別讓 GameplayKit 和 SpriteKit 的結合比以往更容易。

<a name="Metal-Enhancements"></a>

## <a name="metal-enhancements"></a>金屬增強功能

TvOS 10 中的裸機架構已進行下列增強功能：

- 3D 應用程式和遊戲現在可以使用 _鑲嵌_ 來有效率地透過 GPU 呈現複雜的場景和幾何。
- 使用函數特製化，為場景建立高度優化的材質和輕量組合函式集合。
- 提供更精細的資源配置控制，以使用資源堆積和 Memoryless 轉譯目標，將裸機型應用程式的效能優化。

若要深入瞭解，請參閱 Apple 的 [裸機程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="Metal-Performance-Shaders-Enhancements"></a>

## <a name="metal-performance-shaders-enhancements"></a>金屬效能著色器的增強功能

TvOS 10 中的裸機效能著色器架構已進行下列增強：

- 許多新的核心已新增至裸機效能著色器架構，以允許應用程式利用高優化的資料平行計算，例如色彩空間轉換和類神經網路作業。

<a name="ModelIO-Enhancements"></a>

## <a name="modelio-enhancements"></a>ModelIO 增強功能

TvOS 10 中的 ModelIO 架構已進行下列增強功能：

- 現在支援 USD 檔案格式。
- 使用新 `MDLMaterialPropertyGraph` 類別可輕鬆支援模型的執行時間變更。
- 已將已簽署的距離欄位支援新增至 [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) 類別。
- 使用新的 `MDLLightProbeIrradianceDataSource` 類別來協助進行輕量探查放置。

<a name="SceneKit-Enhancements"></a>

## <a name="scenekit-enhancements"></a>SceneKit 增強功能

TvOS 10 中的 SceneKit 架構已進行下列增強功能：

- SceneKit 現在包含新的實體型轉譯 (.PBR) 系統，以較簡單的資產撰寫來提供更真實的結果。
- 使用新的 [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) 陰影模型來產生各式各樣的真實陰影效果，同時只需要三個基本屬性 (`Diffuse` `Metalness` 和 `Roughness`) 。
- 由於 .PBR 陰影與以環境為基礎的光源效果最佳，所以請使用 `LightingEnvironment` 屬性將以影像為基礎的光源指派給 tan 的整個場景。
- 您 `IESProfileURL` 可以使用屬性來匯入真實世界值（例如) 流明中的強度 (）和色溫 (（以角度為單位的) ）來定義光源的真實裝置。
- [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera)類別可以使用 HDR 功能和效果來提供更高的真實性。 使用調適型曝光來建立自動效果，或使用 vignetting、色彩 fringing 和色彩來將 filmatic 效果新增到遊戲中。
- .PBR 和 HDR 攝影機的功能提供比傳統轉譯技術更好的結果，因此，SceneKit 現在會線上性色彩空間中執行所有色彩計算 (使用單色裝置上的 P3 色彩範圍顯示) 。
- SceneKit 現在的色彩會藉由讀取色彩設定檔資訊來符合所有色彩。
- SceneKit 會以線性 RGB 色彩空間來解讀所有著色器類型的色彩元件值。
- 由於 SceneKit 會讀取及調整材質影像中的色彩設定檔資訊，請使用所有影像的資產目錄，以確保提供這項資訊。
- 您可以 `SCNDisableLinearSpaceRendering` 在應用程式中指定和索引鍵，以停用線性色彩空間轉譯和寬色彩 `SCNDisableWideGamut` `Info.plist` 。
- 建立任意多邊形 primates (從檔案載入或以程式設計方式產生) ，以使用新的 [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/documentation/scenekit/scngeometryprimitivetype/scngeometryprimitivetypepolygon) 類別來指定幾何。

<a name="SpriteKit-Enhancements"></a>

## <a name="spritekit-enhancements"></a>SpriteKit 增強功能

TvOS 10 中的 SpriteKit 架構已進行下列增強功能：

- Tilemaps 現在支援使用 `SKTileMapMode` 、 `SKTileGroup` `SKTileGroupRule` 和類別之2d、2.5 d 和側邊滾動遊戲的正方形、六邊形和等位磚圖形 `SKTileSet` 。
- 使用新 `SKWarpGeometry` 類別來延展或扭曲 [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) 或 [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) 轉譯。 新的 [SKAction](https://developer.apple.com/reference/spritekit/skaction) 類別可用來將變形效果之間的轉換動畫。
- 自訂著色器可以提供屬性 (`SKAttribute`) ，而這些屬性可由使用著色器的每個節點個別設定，方法是提供 () 的屬性值 `SKAttributeValue` 。
- [SKView](https://developer.apple.com/reference/spritekit/skview)類別提供數個新方法，可讓您更精細地控制場景的呈現時間和方式。

<a name="UIKit-Enhancements"></a>

## <a name="uikit-enhancements"></a>UIKit 增強功能

TvOS 10 中的 UIKit 架構已進行下列增強功能：

- 焦點 API 已增強，可支援非 view 專案的焦點，以及 `UIViews` 。 支援焦點的專案 _必須_ 執行 `IUIFocusItem` 介面。
- 新 `UIGraphicsRender` 類別提供物件導向的方法，從 UIKit 轉譯或核心圖形建立點陣圖或 pdf，並取代已被取代的 `UIGraphicsBeginImageContext` 方法。
- `UIUserInterfaceStyle`已加入類別，以判斷哪一個使用者介面主題 (深色或淺色) 目前為使用中狀態。
- 新增全互動式、以物件為基礎、可中斷的動畫支援，並 van 連結至手勢。 若如需詳細資訊，請參閱 Apple 的 [UIViewAnimating 通訊協定參考](https://developer.apple.com/reference/uikit/uiviewanimating)、 [UIViewPropertyAnimator 類別參考](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)、 [UITimingCurveProvider 通訊協定參考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)、 [UICubicTimingParameters 類別參考](https://developer.apple.com/reference/uikit/uicubictimingparameters) 和 [UISpringTimingParameter 類別參考](https://developer.apple.com/reference/uikit/uispringtimingparameters) 。
- 新的 `UIPreviewInteraction` 和 `UIPreviewInteractionDelegate` 可讓應用程式提供查看和 pop 作業的自訂介面。
- 新 `UIAccessibilityCustomRotor` 類別可讓應用程式針對輔助技術（例如語音）提供自訂的內容特定功能。
- 使用 `UIAccessibilityIsAssistiveTouchRunning` 和 `UIAccessibilityAssistiveTouchStatusDidChangeNotification` 符號來判斷是否已啟用 AssistiveTouch。
- 使用 `UIAccessibilityHearingDevicePairedEar` 和 `UIAccessibilityHearingDevicePairedEarDidChangeNotification` 符號來取得任何配對 MFi 聽力輔助的狀態。
- 新的 [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API 會提供新的選項 (例如) 的存留期限制，並且會自動宣告常見類別類型的相容內容類型。
- 若要在標籤中支援動態類型，文字欄位和文字方塊會使用類別的新 `PreferredFontForTextStyle` 方法 `UIFont` 。
- 若要決定當裝置變更時，元素是否應該更新字型 `UIContentSizeCategory` ，請使用 `AdjustsFontForContentSizeCategory` 委派的屬性 `UIContentSizeCategoryAdjusting` 。
- 應用程式現在可以控制索引標籤欄專案（例如文字和背景色彩）徽章的外觀。
- 中的重新整理控制項現在支援所有捲軸視圖和捲軸視圖子類別 (例如 `UICollectionView`) 。
- `OpenURL` `UIApplication` 以非同步方式呼叫類別的方法，現在支援在開啟完成之後呼叫的完成處理常式。
- 使用新的和類別，起始 CloudKit 共用和修改其屬性 `UICloudSharingController` `UICloudSharingControllerDelegate` 。
- 利用預先提取的儲存格，利用新的委派改善的滾動體驗 `UICollectionViews` `UICollectionViewDataSourcePrefetching` 。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [TvOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)