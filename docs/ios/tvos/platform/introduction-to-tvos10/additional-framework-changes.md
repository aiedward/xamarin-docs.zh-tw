---
title: 其他 tvOS 10 架構變更
description: 本文件說明微幅變更以及對現有的架構，在 iOS 10 中的增強功能。 其中也會討論 AVFoundation、 AVKit、 核心資料、 核心圖形、 Foundation、 GameKit、 GameplayKit，及更新多個。
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 326fb6a23048ba3d3e1d33f42c8da2fff8544c25
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788871"
---
# <a name="additional-tvos-10-frameworks-changes"></a>其他 tvOS 10 架構變更

除了 tvOS 重大變更，Apple 已進行修改，以及數個現有架構的增強功能中 tvOS 10。

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>AVFoundation 架構新增項目

AVFoundation 架構包含下列增強功能：

 - 在 tvOS 10，應用程式不再實作不同[AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem)行為會根據內容類型。 只要設定`Rate`屬性而且 AVFoundation 會決定當足夠的內容是可供播放不一。
 - 新`AVPlayerLooper`類別可讓您更輕鬆地在播放期間迴圈指定的媒體項目。

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>AVKit 架構增強功能

AVKit 架構包含下列增強功能：

 - 應用程式現在已略過行為的控制[AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller)所以正在略過手勢，可能會移至下一個項目，在播放清單或在目前的項目中的進階。

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>核心資料增強功能

tvOS 10 包含核心資料架構的下列增強功能：

 - 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援並行判定為失敗，和擷取沒有序列化。
 - [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別會維護 SQLite 資料存放區集區。
 - [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件與 SQLite WAL 日誌模式支援新的查詢產生的資料存放區功能在受管理物件內容 （木） 可以固定到未來擷取特定的資料庫版本和判定為失敗的交易。
 - 使用高階`NSPersistenceContainer`參考`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他資源的核心資料設定。
 - 已加入數個新的便利方法`NSManagedObject`以便更容易執行提取及建立子類別。

如需詳細資訊，請參閱 Apple[核心資料架構參考](https://developer.apple.com/reference/coredata)。

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>核心圖形增強功能

tvOS 10 包含核心圖形架構的下列增強功能：

 - 新[CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref)類別可以用來執行一系列的色彩轉換。

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>Core 映像的增強功能

tvOS 10 會進行 Core 映像架構的下列增強功能：

 - `ImageWithExtent`方法[CIFilter](https://developer.apple.com/reference/coreimage/cifilter)類別可以用來篩選作業中插入自訂處理。 Core 映像將會叫用指定的回呼之間篩選處理輸出的映像時，或顯示。
 - 應用程式現在可以藉由之前和之後處理內外的色彩空間轉換處理 Core 映像內容的工作色彩空間以外的色彩空間中的映像。
 - 數個轉譯效能增強功能已對`UIImage`中呈現 （當 Core 映像的映像存放區所支援）`UIImageView`物件。 
 - `UIImage` 物件已加上標籤的整個範圍將會呈現為寬色域圖中的色彩`UIImageView`支援寬色彩的 iOS 裝置上的物件。
 - Core 映像核心程式碼現在可以要求特定像素輸出格式。

此外，已新增下列新 Core 映像篩選器：

 - `CINinePartTiled`
 - `CINinePartStretched`
 - `CIHueSaturationValueGradient`
 - `CIEdgePreserveUpsampleFilter`
 - `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Foundation 增強功能

TvOS 10 的基礎架構已做了下列增強功能：

 - 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別，例如持續時間，來比較間隔及測試間隔交集的日期和時間間隔計算。
 - 已加入數個新屬性[NSLocal](https://developer.apple.com/reference/foundation/nslocale)類別取得本機資訊以及可用的顯示格式。
 - 使用新[NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement)轉換之間不同單位的量值 (UOM)，或在不同的 UOMs 值上執行計算的類別。
 - 使用新[NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter)類別來格式化終端使用者顯示當地語系化的度量單位。
 - 使用新[NSUnit](https://developer.apple.com/reference/foundation/nsunit)和[NSDimension](https://developer.apple.com/reference/foundation/nsdimension)類別，表示特定 UOMs。

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>GameKit 增強功能

GameKit 架構 tvOS 10 中已做了下列增強功能：

 - 新的僅限 iCloud 帳戶類型由[GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別。
 - 新[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)類別提供的管理永續性資料儲存在遊戲中心的一般化的解決方案。 `GKGameSession` 會維護的播放清單和應用程式是負責的形式實作如何及何時會儲存參與者的日期，擷取或播放程式之間交換。 在許多情況下遊戲工作階段可以取代現有的開啟相符項，即時的相符項目或持續性方法儲存的遊戲。

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>GameplayKit 增強功能

GameplayKit 架構 tvOS 10 中已做了下列增強功能：

 - 程序的雜訊產生已經加入，而且可用來增強自然尋找紋理中的真實性，加入真實度相機移動並說明產生豐富的遊戲世界。
 - 使用空間資料分割的有效搜尋的遊戲世界資料分割。
 - 新的 Monte Carlo 策劃師 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 已加入的獨占可能移動計算。
 - 已加入新的決策樹 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)和[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 來增強遊戲建置 AI。
 - 3D 支援已新增至現有的代理程式和使用新的路徑尋找行為[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)和[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)類別。
 - 使用新[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)提供高效能、 自然尋找路徑的類別。
 - 新[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)和[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)類別產生結合 GameplayKit 和 SpriteKit 比以往更為容易。

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>裸機增強功能

TvOS 10 的金屬架構已做了下列增強功能：

 - 現在可以使用 3D 應用程式和遊戲_鑲嵌式_來有效率地呈現複雜的場景和透過 GPU 的幾何。
 - 若要建立獲得高度最佳化的場景的淺組合函式和資料，集合中使用函式特製化。
 - 請提供更精細地控制資源配置，來最佳化效能的金屬堆積使用資源的應用程式和 Memoryless 呈現目標。

若要進一步了解，請參閱 Apple[金屬程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>裸機效能著色器增強功能

TvOS 10 的金屬效能著色器架構已做了下列增強功能：

 - 已加入許多新的核心金屬效能著色器架構以允許應用程式，以充分利用高最佳化、 資料平行計算，例如色彩空間轉換和類神經網路作業。

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>ModelIO 增強功能

ModelIO 架構 tvOS 10 中已做了下列增強功能：

 - 現在支援 USD 檔案格式。
 - 使用新`MDLMaterialPropertyGraph`類別，以輕鬆地支援執行階段變更模型。
 - 帶正負號距離的欄位已加入支援[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)類別。
 - 使用新`MDLLightProbeIrradianceDataSource`類別，以協助 Light 探查位置中。

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>SceneKit 增強功能

SceneKit 架構 tvOS 10 中已做了下列增強功能：

 - SceneKit 現在包含新的實際基礎呈現 (PBR) 系統與撰寫簡單的資產比較實際的結果。
 - 使用新[SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased)網底模型產品各種不同的具真實感的陰影效果同時需要只有三個基本屬性 (`Diffuse`，`Metalness`和`Roughness`)。
 - PBR 與環境為基礎的光源最佳陰影運作，因為使用`LightingEnvironment`指派映像為基礎的光源 tan 整個場景的屬性。
 - 使用`IESProfileURL`要匯入的真實世界的值，例如強度 （以 lumens) 上，定義光源的基底和色彩的溫度 （以度為單位開氏） 的真實世界照明設備屬性。
 - [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera)類別可以使用 HDR 功能和效果提供更大的真實性。 使用自動調整曝光建立自動效果或使用 vignetting、 fringing 色彩及色彩閱卷 filmatic 效果加入遊戲。
 - PBR 和 HDR 相機功能提供更好的結果，與傳統的呈現技術，如此一來，SceneKit 現在便會執行所有色彩計算線性色彩空間 （wide 色彩裝置顯示畫面上使用 P3 色域圖） 中。
 - 依讀取的色彩設定檔資訊 SceneKit 現在色彩比對的所有色彩。
 - SceneKit 解譯所有著色器類型線性 RGB 色彩空間中的色彩元件值。
 - 由於 SceneKit 讀取，並且調整紋理映像中的色彩設定檔資訊，使用所有影像資產目錄以確保在提供此資訊。
 - 線性兩者色彩空間轉譯，可以透過指定停用整個色彩`SCNDisableLinearSpaceRendering`和`SCNDisableWideGamut`的應用程式中的索引鍵`Info.plist`。
 - 建立任意多邊形靈長類動物 （無論是從檔案載入或透過程式設計方式產生） 來指定與新的 geometry [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon)類別。

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>SpriteKit 增強功能

SpriteKit 架構 tvOS 10 中已做了下列增強功能：

 - 2D、 2.5 D 和使用的側邊捲動遊戲 Tilemaps 現在支援的正方形、 六角和等並排顯示圖形`SKTileMapMode`， `SKTileGroup`，`SKTileGroupRule`和`SKTileSet`類別。
 - 使用新`SKWarpGeometry`類別來自動縮放或扭曲[SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode)或[SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode)轉譯。 新[SKAction](https://developer.apple.com/reference/spritekit/skaction)類別可以用來建立動畫 warp 效果之間的轉換。
 - 自訂的著色器可以提供屬性 (`SKAttribute`)，藉由提供屬性值使用著色器的每一個節點可以分別設定 (`SKAttributeValue`)。
 - [SKView](https://developer.apple.com/reference/spritekit/skview)類別提供數種新方法，來提供更細微的控制時機和方式呈現的場景。

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>UIKit 增強功能

UIKit 架構 tvOS 10 中已做了下列增強功能：

 - 焦點應用程式開發介面已增強為支援非檢視項目的焦點此外至`UIViews`。 支援焦點的項目_必須_實作`IUIFocusItem`介面。
 - 新`UIGraphicsRender`類別提供的物件導向方法從 UIKit 轉譯或核心圖形建立點陣圖或 Pdf，並會取代已被取代`UIGraphicsBeginImageContext`方法。
 - `UIUserInterfaceStyle`類別加入至判斷哪一個使用者介面主題 （暗色或亮色） 是目前作用中。
 - 已加入新的完全互動式、 以物件為基礎、 可中斷動畫支援 van 連結到筆勢。 請參閱 Apple 的[UIViewAnimating 通訊協定參考](https://developer.apple.com/reference/uikit/uiviewanimating)， [UIViewPropertyAnimator 類別參考](https://developer.apple.com/reference/uikit/uiviewpropertyanimator)， [UITimingCurveProvider 通訊協定參考](https://developer.apple.com/reference/uikit/uitimingcurveprovider)， [UICubicTimingParameters 類別參考](https://developer.apple.com/reference/uikit/uicubictimingparameters)和[UISpringTimingParameter 類別參考](https://developer.apple.com/reference/uikit/uispringtimingparameters)如需詳細資訊。
 - 新`UIPreviewInteraction`和`UIPreviewInteractionDelegate`允許應用程式應用程式，以查看和 pop 作業提供自訂的介面。
 - 新`UIAccessibilityCustomRotor`類別可讓應用程式，以提供自訂的特定內容的輔助技術，例如透過語音功能。
 - 使用`UIAccessibilityIsAssistiveTouchRunning`和`UIAccessibilityAssistiveTouchStatusDidChangeNotification`符號來判斷是否已啟用 AssistiveTouch。
 - 使用`UIAccessibilityHearingDevicePairedEar`和`UIAccessibilityHearingDevicePairedEarDidChangeNotification`符號來取得狀態的任何成對 MFi 聽覺輔助工具。
 - 新[UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API 會提供新選項 （例如存留時間限制），並會自動宣告相容的一般類別類型的內容類型。
 - 若要支援動態類型標籤中，文字欄位與文字方塊使用新`PreferredFontForTextStyle`方法`UIFont`類別。
 - 若要決定如果項目應該更新其字型時裝置`UIContentSizeCategory`變更，使用`AdjustsFontForContentSizeCategory`屬性`UIContentSizeCategoryAdjusting`委派。
 - 應用程式現在可以控制索引標籤列項目，例如文字和背景色彩的徽章的外觀。
 - 重新整理控制項現在支援在所有的捲動檢視和捲動檢視子類別 (例如`UICollectionView`)。
 - `OpenURL`方法`UIApplication`類別呼叫以非同步方式現在支援稱為開啟已完成之後完成處理常式。
 - 起始 CloudKit 共用，並修改其屬性，使用新`UICloudSharingController`和`UICloudSharingControllerDelegate`類別。
 - 利用預先擷取的資料格，以改善捲動經驗的`UICollectionViews`與新`UICollectionViewDataSourcePrefetching`委派。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [在 tvOS 10 最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
