---
title: 其他 tvOS 10 架構變更
description: 本檔說明在 iOS 10 中對現有架構所做的微小變更和增強功能。 討論 AVFoundation、AVKit、核心資料、核心圖形、基礎、GameKit、GameplayKit 等等的更新。
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 99ee654b260a3a89b58578d352dd066a41753295
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769143"
---
# <a name="additional-tvos-10-frameworks-changes"></a>其他 tvOS 10 架構變更

除了 tvOS 的主要變更之外，Apple 也修改了 tvOS 10 中的數個現有架構。

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>新增 AVFoundation 架構

AVFoundation 架構包含下列增強功能：

- 在 tvOS 10 中，應用程式不再根據內容類型來執行不同的[AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem)行為。 只要設定`Rate`屬性，AVFoundation 就會決定是否有足夠的內容可供播放，而不需要停止。
- 新`AVPlayerLooper`的類別可讓您更輕鬆地在播放期間迴圈指定的媒體片段。

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>AVKit Framework 增強功能

AVKit 架構包含下列增強功能：

- 應用程式現在可以控制[AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller)的略過行為，因此略過手勢可能會移至播放清單中的下一個專案，或在目前專案中前進。

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>核心資料增強功能

tvOS 10 包含下列核心資料架構的增強功能：

- 根[NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援並行錯誤和未序列化的提取。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別會維護 SQLite 資料存放區的集區。
- WAL 記錄模式中具有 SQLite 資料存放區的[NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援新的查詢產生功能，其中 Managed 物件內容（MOC）可以釘選到特定的資料庫版本，以供未來提取和錯誤交易使用。
- 使用高階`NSPersistenceContainer`來`NSPersistentStoreCoordinator`參考、 [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心資料設定資源。
- 已新增`NSManagedObject`數個新的便利方法，讓您更輕鬆地執行提取和建立子類別。

如需詳細資訊，請參閱 Apple 的[核心資料架構參考](https://developer.apple.com/reference/coredata)。

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>核心圖形增強功能

tvOS 10 包含下列核心圖形架構的增強功能：

- 新的[CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref)類別可以用來執行一系列的色彩轉換。

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>核心映射增強功能

tvOS 10 對核心映射架構提供下列增強功能：

- [CIFilter 類別](https://developer.apple.com/reference/coreimage/cifilter)的方法可以用來將自訂處理插入篩選作業`ImageWithExtent`中。 核心映射會在處理輸出或顯示的影像時，叫用篩選之間的指定回呼。
- 應用程式現在可以在處理之前和之後來回轉換色彩空間，以在核心影像內容的工作色彩空間之外的色彩空間中處理影像。
- 在物件中`UIImage` `UIImageView` ，已對轉譯（受核心映射映射存放區支援）進行數個轉譯效能增強功能。 
- `UIImage`標記為寬範圍的物件會在`UIImageView`支援寬色彩的 iOS 裝置上，呈現為物件的寬範圍色彩。
- 核心影像核心程式代碼現在可以要求特定圖元輸出格式。

此外，已新增下列新的核心映射篩選：

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>基礎增強功能

已對 tvOS 10 的基礎架構進行下列增強功能：

- 使用新的[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別來進行日期和時間間隔計算（例如持續期間），以比較間隔和測試間隔交集。
- 已將數個新的屬性新增至[NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別，以取得本機資訊和可用的顯示格式。
- 使用新的[NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement)類別，在不同的測量單位（UOM）之間進行轉換，或在不同 UOMs 的值上執行計算。
- 使用新的[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)類別來格式化當地語系化的量測測量，以顯示給終端使用者。
- 使用新的[NSUnit](https://developer.apple.com/reference/foundation/nsunit)和[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)類別來代表特定 UOMs。

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>GameKit 增強功能

TvOS 10 中的 GameKit 架構已進行下列增強功能：

- [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別已實作為僅限 iCloud 的新帳戶類型。
- 新的[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)類別提供通用的解決方案來管理 Game Center 上的持續性資料存放區。 `GKGameSession`會維護一份玩家清單，而應用程式會負責執行如何以及何時儲存、抓取或交換玩家之間的參與者日期。 在許多情況下，遊戲會話可以取代現有的回合型比對、即時符合或持續的遊戲儲存方法。

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>GameplayKit 增強功能

TvOS 10 中的 GameplayKit 架構已進行下列增強功能：

- 已新增程式產生雜訊，並可用來增強自然外觀材質的真實性、增加攝影機移動的真實感，並協助產生豐富的遊戲世界。
- 使用空間分割來分割遊戲世界的資料，以進行有效率的搜尋。
- 已加入新的 Monte Carlo 策略家（[GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)），以取得徹底可能的移動計算。
- 新的決策樹 API 已新增（[GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)和[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)），以增強遊戲建立 AI。
- 已使用新的[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)和[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)類別，將3d 支援新增至現有的代理程式和路徑尋找行為。
- 使用新的[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)類別來提供高效能、自然外觀的路徑。
- 新的[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)和[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)類別結合了 GameplayKit 和 SpriteKit，使其比以往更容易。

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>金屬增強功能

TvOS 10 中的金屬架構已進行下列增強功能：

- 3D 應用程式和遊戲現在可以使用_鑲嵌_式，透過 GPU 有效率地呈現複雜的場景和幾何。
- 使用函數特製化，為場景建立高度優化的材質和光源組合功能集合。
- 使用資源堆積和 Memoryless 轉譯目標，提供更精細的資源配置控制，以將金屬型應用程式的效能優化。

若要深入瞭解，請參閱 Apple 的[金屬程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>金屬效能著色器增強功能

TvOS 10 中的金屬效能著色器架構已進行下列增強功能：

- 許多新的核心已新增至金屬效能著色器架構，可讓應用程式利用高優化的資料平行計算，例如色彩空間轉換和類神經網路作業。

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>ModelIO 增強功能

TvOS 10 中的 ModelIO 架構已進行下列增強功能：

- 現在支援 USD 檔案格式。
- 使用新`MDLMaterialPropertyGraph`的類別，輕鬆地支援模型的執行時間變更。
- 已將已簽署的距離欄位支援新增至[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)類別。
- 使用新`MDLLightProbeIrradianceDataSource`的類別來協助進行較輕的探查位置。

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>SceneKit 增強功能

TvOS 10 中的 SceneKit 架構已進行下列增強功能：

- SceneKit 現在包含新的實際轉譯（.PBR）系統，可提供更簡單的資產製作，以獲得更實際的結果。
- 使用新的[SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased)陰影模型來組成各式各樣的實際陰影效果，同時只需要三個基本屬性（`Diffuse`、 `Metalness`和`Roughness`）。
- 由於 .pbr 陰影最適合使用以環境為基礎的光源， `LightingEnvironment`因此，請使用屬性將影像型光源指派給 tan 整個場景。
- `IESProfileURL`使用屬性，即可匯入定義光源的真實世界光源，例如濃度（流明）和色溫度（以度為單位）。
- [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera)類別可使用 HDR 特性和效果來提供更高的真實性。 使用調適型曝光來建立自動效果，或使用 vignetting、色彩 fringing 和色彩評分，將 filmatic 效果新增到遊戲中。
- 與傳統轉譯技術相比，.PBR 和 HDR 攝影機功能提供更好的結果，因此，SceneKit 現在會線上性色彩空間中執行所有色彩計算（在寬色彩裝置顯示器上使用 P3 色彩色域）。
- [立即 SceneKit] 色彩會藉由閱讀色彩設定檔資訊來符合所有色彩。
- SceneKit 會針對所有著色器類型，以線性 RGB 色彩空間來解讀色彩元件值。
- 由於 SceneKit 會讀取和調整材質影像中的色彩設定檔資訊，因此請針對所有影像使用資產目錄，以確保提供這項資訊。
- 藉由在應用程式的`SCNDisableLinearSpaceRendering` `Info.plist`中指定和`SCNDisableWideGamut`鍵，可以停用線性色彩空間呈現和寬色彩。
- 建立任意多邊形 primates （從檔案載入或以程式設計方式產生），以使用新的[SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon)類別來指定幾何。

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>SpriteKit 增強功能

TvOS 10 中的 SpriteKit 架構已進行下列增強功能：

- `SKTileMapMode`Tilemaps 現在支援使用`SKTileGroup` `SKTileGroupRule` 、和`SKTileSet`類別之2d、2.5 d 和側邊滾動遊戲的正方形、六邊形和等式磚圖形。
- 使用新`SKWarpGeometry`的類別來延展或扭曲[SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode)或[SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode)轉譯。 新的[SKAction](https://developer.apple.com/reference/spritekit/skaction)類別可以用來建立變形效果之間的轉換動畫。
- 自訂著色器可以提供`SKAttribute`屬性（），您可以藉由提供屬性值（`SKAttributeValue`），分別由使用著色器的每個節點加以設定。
- [SKView](https://developer.apple.com/reference/spritekit/skview)類別提供數個新的方法，可讓您更精細地控制場景的呈現時機和方式。

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>UIKit 增強功能

TvOS 10 中的 UIKit 架構已進行下列增強功能：

- 焦點 API 已經過增強，可支援以外`UIViews`的非 view 專案焦點。 支援焦點的專案_必須_執行`IUIFocusItem`介面。
- 新`UIGraphicsRender`的類別會提供物件導向的方法，從 UIKit 轉譯或核心圖形建立點陣圖或 pdf，並取代已`UIGraphicsBeginImageContext`被取代的方法。
- 已`UIUserInterfaceStyle`新增類別，以判斷目前作用中的使用者介面主題（深色或淺色）。
- 新增完全互動式、以物件為基礎、可中斷的動畫支援，並將 van 連結至手勢。 Pleas 請參閱 Apple 的[UIViewAnimating 通訊協定參考](https://developer.apple.com/reference/uikit/uiviewanimating)、 [UIViewPropertyAnimator 類別參考](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)、 [UITimingCurveProvider 通訊協定參考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)、 [UICubicTimingParameters 類別參考](https://developer.apple.com/reference/uikit/uicubictimingparameters)和[UISpringTimingParameter 類別參考](https://developer.apple.com/reference/uikit/uispringtimingparameters)如需詳細資訊, 。
- 新`UIPreviewInteraction`的和`UIPreviewInteractionDelegate`可讓應用程式提供查看和 pop 作業的自訂介面。
- 新`UIAccessibilityCustomRotor`的類別可讓應用程式提供自訂的內容特定功能給輔助技術，例如 Voice。
- `UIAccessibilityIsAssistiveTouchRunning`使用和`UIAccessibilityAssistiveTouchStatusDidChangeNotification`符號來判斷是否已啟用 AssistiveTouch。
- `UIAccessibilityHearingDevicePairedEar`使用和`UIAccessibilityHearingDevicePairedEarDidChangeNotification`符號來取得任何配對的 MFi 聽覺輔助工具的狀態。
- 新的[UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API 會提供新的選項（例如存留期限制），並會自動為一般類別類型宣告相容的內容類型。
- 若要在標籤中支援動態類型，文字欄位和文字方塊會`PreferredFontForTextStyle`使用`UIFont`類別的新方法。
- 若要決定當裝置`UIContentSizeCategory`變更時，元素是否應該更新它的字型，請使用`UIContentSizeCategoryAdjusting`委派的`AdjustsFontForContentSizeCategory`屬性。
- 應用程式現在可以控制索引標籤欄專案的徽章外觀，例如文字和背景色彩。
- 所有捲軸和捲軸子類別中現在支援的重新整理控制項（例如`UICollectionView`）。
- 此類別`UIApplication`的方法會以非同步方式呼叫，現在支援在開啟完成後呼叫的`OpenURL`完成處理常式。
- 使用新`UICloudSharingController`的和`UICloudSharingControllerDelegate`類別，起始 CloudKit 共用並修改其屬性。
- 利用預先提取的資料格來改善`UICollectionViews`使用新`UICollectionViewDataSourcePrefetching`委派的的滾動體驗。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [TvOS 10 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
