---
title: 其他 tvOS 10 架構變更
description: 本文件說明微幅的變更和對現有的架構，在 iOS 10 中的增強功能。 它討論 AVFoundation、 AVKit、 核心資料、 Core 圖形、 Foundation、 GameKit、 GameplayKit，及更新多個。
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: ab6236198d0a5826fc613d1f3839bafdb980d235
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865651"
---
# <a name="additional-tvos-10-frameworks-changes"></a>其他 tvOS 10 架構變更

TvOS 的主要變更，除了 Apple 方面所做的修改以及數個現有的架構的改善 tvOS 10。

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>AVFoundation Framework 新增功能

AVFoundation 架構包含下列增強功能：

- TvOS 10，在應用程式不再實作不同[AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem)行為取決於內容類型。 只要設定`Rate`屬性和 AVFoundation 會決定當足夠的內容是適用於播放不含懸置。
- 新`AVPlayerLooper`類別可讓您更輕鬆地循環播放在播放期間，指定的媒體項目。

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>AVKit Framework 增強功能

AVKit 架構包含下列增強功能：

- 應用程式現在已略過的行為控制[AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller)因此略過的動作可能會移至下一個項目，在播放清單或在目前的項目中的進階。

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>核心資料增強功能

tvOS 10 包括下列增強功能的核心資料架構：

- 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援並行的判定為失敗，並擷取沒有序列化。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別維護的 SQLite 資料存放區集區。
- [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件與 SQLite WAL 日誌模式支援新的查詢產生的資料存放區功能，受管理物件內容 （模式） 可以釘選到未來擷取特定的資料庫版本和失敗的交易。
- 使用高階`NSPersistenceContainer`參考`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心資料設定資源。
- 已新增數個新的便利方法`NSManagedObject`更容易執行提取，並建立子類別。

如需詳細資訊，請參閱 Apple [Core Data Framework 參考](https://developer.apple.com/reference/coredata)。

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>Core 圖形加強功能

tvOS 10 包括下列增強功能的核心圖形架構：

- 新[CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref)類別可以用來執行一系列的色彩轉換。

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>核心映像的增強功能

tvOS 10 可讓 Core 映像架構的下列增強功能：

- `ImageWithExtent`方法[CIFilter](https://developer.apple.com/reference/coreimage/cifilter)類別可以用來篩選作業中插入自訂處理。 Core 映像將會叫用指定的回撥處理輸出的映像時的篩選器之間，或顯示。
- 應用程式現在可以將核心映像內容的處理色彩空間之外的色彩空間中的映像處理之前和之後處理流入和流出的色彩空間轉換。
- 數個轉譯效能增強功能進行了`UIImage`呈現 （當受 Core 映像的映像存放區） 中`UIImageView`物件。 
- `UIImage` 物件已加上標籤的範圍-色域圖會轉譯為寬色域圖中的色彩`UIImageView`支援各種色彩的 iOS 裝置上的物件。
- Core 映像的核心程式碼現在可以要求特定的像素輸出格式。

此外，已新增下列新的核心映像篩選器：

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Mfc 增強功能

下列增強功能已對 tvOS 10 的基礎架構：

- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別，例如持續時間，來比較的間隔，並測試間隔交集的日期和時間間隔計算。
- 已新增數個新屬性[NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別來取得本機資訊和可用的顯示格式。
- 使用新[NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement)類別來轉換之間不同單位的量值 (UOM)，或在不同的 UOMs 中的值上執行計算。
- 使用新[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)格式化當地語系化的度量單位，顯示給使用者的類別。
- 使用新[NSUnit](https://developer.apple.com/reference/foundation/nsunit)並[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)類別，表示特定 UOMs。

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>GameKit 增強功能

下列增強功能已對 tvOS 10 GameKit 架構：

- 已實作新的僅限 iCloud 帳戶類型所[GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別。
- 新[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)類別管理在 Game Center 的持續性資料儲存體提供的通用的解決方案。 `GKGameSession` 維護一份清單的播放程式和應用程式是負責的形式實作如何及何時會儲存參與者的日期，擷取或播放程式之間交換。 在許多情況下遊戲工作階段可以取代現有的回合式相符項目、 即時的相符項目或持續性方法儲存的遊戲。

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>GameplayKit 增強功能

下列增強功能已對 tvOS 10 GameplayKit 架構：

- 程序的雜訊產生已新增，而且可用來增強自然尋找紋理中的真實性，加入真實性觀景窗移動並協助產生豐富的遊戲世界。
- 使用空間資料分割來分割的遊戲世界資料進行有效率的搜尋。
- 新的 Monte Carlo 策略家 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 已加入的詳盡的可能移動計算。
- 已新增新的決策樹 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)並[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 來增強遊戲建置 AI。
- 3D 支援已新增至現有的代理程式和使用新的路徑尋找行為[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)並[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)類別。
- 使用新[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)類別，以提供高效能、 自然尋找路徑。
- 新[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)並[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)類別進行結合 GameplayKit 和 SpriteKit 比以往更容易。

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>金屬的增強功能

下列增強功能已對 tvOS 10 的裸機架構：

- 現在可以使用 3D 應用程式和遊戲_鑲嵌式_來有效率地呈現複雜的場景和透過 GPU 的幾何。
- 若要建立的 material 和場景光線的組合函式高度最佳化的集合中使用函式特製化。
- 提供更精細地控制資源配置，以最佳化效能的金屬為基礎的堆積使用資源的應用程式和 Memoryless 轉譯目標。

若要進一步了解，請參閱 Apple [Metal 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>金屬效能著色器增強功能

下列增強功能已對 tvOS 10 的 Metal 效能著色器架構：

- 已新增許多新的核心給 Metal 效能著色器架構，以允許應用程式，以利用高最佳化、 資料平行計算，例如色彩空間轉換和類神經網路作業。

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>ModelIO 增強功能

下列增強功能已對 tvOS 10 ModelIO 架構：

- 現在支援美元檔案格式。
- 使用新`MDLMaterialPropertyGraph`類別，以輕鬆地支援執行階段變更模型。
- 帶正負號距離欄位已加入支援[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)類別。
- 使用新`MDLLightProbeIrradianceDataSource`類別，以協助 Light 探查的放置。

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>SceneKit 增強功能

下列增強功能進行了 tvOS 10 的 SceneKit 架構：

- SceneKit 現在包含新的實際基礎呈現 (PBR) 系統，更簡單的資產編寫更實際的結果。
- 使用新[SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased)陰影模型產品各種不同具真實感的陰影效果的同時要求只有三個基本屬性 (`Diffuse`，`Metalness`和`Roughness`)。
- 因為 PBR 最陰影以環境為基礎的光源的運作方式，使用`LightingEnvironment`將映像為基礎來 tan 整個場景的照明的屬性。
- 使用`IESProfileURL`匯入實際燈座，定義基底的光源強度 （以 lumens) 等的實際值和色彩的溫度 （以度為單位 Kelvin） 的屬性。
- [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera)類別可以使用 HDR 功能及特效，提供更高的真實性。 使用調適性的曝光度，來建立自動的效果或使用 vignetting、 fringing 色彩和色彩閱卷 filmatic 效果加入遊戲。
- PBR 和 HDR 相機功能提供更好的結果，比傳統的呈現技術，如此一來，SceneKit 現在會執行所有色彩計算 （使用全彩裝置顯示器上的色域圖 P3） 的線性的色彩空間中。
- SceneKit 現在色彩符合所有色彩，請閱讀的色彩設定檔資訊。
- SceneKit 將解釋所有的著色器類型的線性 RGB 色彩空間中的色彩元件值。
- SceneKit 讀取，並調整紋理的影像中的色彩設定檔資訊，因為使用的所有映像的資產目錄以確保提供此資訊。
- 線性兩者色彩空間轉譯及 wide 色彩可以藉由指定已停用`SCNDisableLinearSpaceRendering`並`SCNDisableWideGamut`的應用程式中的索引鍵`Info.plist`。
- 建立任意的多邊形靈長類動物 （無論是從檔案載入，或以程式設計方式產生） 來指定與新的幾何[SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon)類別。

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>SpriteKit 的增強功能

下列增強功能進行了 tvOS 10 的 SpriteKit 架構：

- 2D、 2.5 D 和側邊捲動的遊戲使用 Tilemaps 現在支援的方形，六角等的並排顯示圖案`SKTileMapMode`， `SKTileGroup`，`SKTileGroupRule`和`SKTileSet`類別。
- 使用新`SKWarpGeometry`類別來自動縮放或扭曲[SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode)或是[SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode)轉譯。 新[SKAction](https://developer.apple.com/reference/spritekit/skaction)類別可用來以動畫顯示 warp 效果之間的轉換。
- 自訂著色器可以提供屬性 (`SKAttribute`)，藉由提供屬性值中使用著色器的每一個節點可以分別設定 (`SKAttributeValue`)。
- [SKView](https://developer.apple.com/reference/spritekit/skview)類別提供數種新方法，來提供更細微的控制何時及如何呈現的場景。

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>UIKit 增強功能

下列增強功能進行了 tvOS 10 的 UIKit 架構：

- 焦點 API 已增強以非檢視項目的焦點，還支援`UIViews`。 支援焦點的項目_必須_實作`IUIFocusItem`介面。
- 新`UIGraphicsRender`類別會提供物件導向的方法從 UIKit 轉譯或 Core 圖形建立點陣圖或 Pdf，並取代已被取代`UIGraphicsBeginImageContext`方法。
- `UIUserInterfaceStyle`類別加入至判斷目前作用中的使用者介面佈景主題 （暗色或亮色）。
- 已新增新的具全面互動性、 物件為基礎、 可中斷的動畫支援和 van 連結筆勢。 請參閱 Apple 的[UIViewAnimating 通訊協定參考](https://developer.apple.com/reference/uikit/uiviewanimating)， [UIViewPropertyAnimator 類別參考](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)， [UITimingCurveProvider 通訊協定參考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)， [UICubicTimingParameters 類別參考](https://developer.apple.com/reference/uikit/uicubictimingparameters)並[UISpringTimingParameter 類別參考](https://developer.apple.com/reference/uikit/uispringtimingparameters)如需詳細資訊。
- 新`UIPreviewInteraction`和`UIPreviewInteractionDelegate`允許應用程式提供一種自訂介面，查看和 pop 的作業。
- 新`UIAccessibilityCustomRotor`類別可讓應用程式提供自訂的特定內容的功能，例如 Voiceover 的輔助技術。
- 使用`UIAccessibilityIsAssistiveTouchRunning`和`UIAccessibilityAssistiveTouchStatusDidChangeNotification`符號來判斷是否已啟用 AssistiveTouch。
- 使用`UIAccessibilityHearingDevicePairedEar`和`UIAccessibilityHearingDevicePairedEarDidChangeNotification`符號，可取得的任何狀態配對 MFi 期待輔助工具。
- 新[UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API 提供新的選項 （例如存留期限制），並會自動宣告類別上的常見的相容內容類型。
- 若要支援動態型別在標籤中，文字欄位和文字方塊使用新`PreferredFontForTextStyle`方法的`UIFont`類別。
- 若要決定如果項目應該更新其字型時裝置`UIContentSizeCategory`變更，使用`AdjustsFontForContentSizeCategory`屬性`UIContentSizeCategoryAdjusting`委派。
- 應用程式現在可以控制索引標籤列項目，例如文字和背景色彩徽章的外觀。
- 重新整理中的控制項現在支援在所有的捲動檢視和捲動檢視子類別 (例如`UICollectionView`)。
- `OpenURL`方法的`UIApplication`類別呼叫以非同步方式現在支援開啟完成後，會呼叫完成處理常式。
- 起始 CloudKit 共用，並修改其使用新的屬性`UICloudSharingController`和`UICloudSharingControllerDelegate`類別。
- 利用預先擷取的資料格，以改善的捲動體驗`UICollectionViews`與新`UICollectionViewDataSourcePrefetching`委派。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [TvOS 10 中最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
