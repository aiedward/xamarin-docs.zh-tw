---
title: 其他 macOS 利也架構變更
description: 本文涵蓋其他次要變更或增強現有 macOS 利也適用架構。
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: a1bc12629a84e9a06cc80882d141bf6a0c2f0c52
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="additional-macos-sierra-framework-changes"></a>其他 macOS 利也架構變更

_本文涵蓋其他次要變更或增強現有 macOS 利也適用架構。_

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>加速架構增強功能

加速架構 macOS 利也已做了下列增強功能：

- 加入 quadrature Amplitude （整數微積分）。
- 加入建構類神經網路所需的基本功能。
- 加入幾何述詞函式來測試兩個幾何物件的交集之類的事情。

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Appkit 架構增強功能

AppKit 架構 macOS 利也已做了下列增強功能：

- 數個增強功能`NSCollectionView`例如：
    - **可摺疊區段**-可讓使用者摺疊成單一的水平資料列的集合檢視 > 一節。
    - **浮動標頭**-頁首和頁尾可以現在浮動 （在 流程版面配置） 使用相同的 API 為[UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview)在 iOS 中。
    - **可捲動的背景檢視**-現在可以設定集合檢視背景內容一起捲動。
- 延後的檢視配置傳遞已最佳化並擴充。
- 拖放 API 現在包含新`NSFilePromiseProvider`和`NSFilePromiseReceiver`flocking 的類別，來支援拖曳。
- 幾個便利的建構函式已加入至現有的控制項：
    -  `NSButton` 包含新的建構函式建立按鈕的核取方塊和選項按鈕。
    -  `NSTextField` 包含新的建構函式來建立包裝和非換行標籤，屬性化的標籤和編輯的文字欄位。
    -  `NSSegmentedControl` 包含新的建構函式，從群組的標籤或映像建立分割的控制項。
    -  `NSSlider` 包含新的建構函式建立水平的線性滑桿。
    -  `NSImageView` 包含新的建構函式建立非可編輯影像檢視，從給定`NSImage`。
-  新`NSGridView`已加入至自動版面配置資料列和資料行可以動態地隱藏或顯示的大小調整成變數方格子檢視的集合。

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>AVFoundation 架構增強功能

AVFoundation 架構 macOS 利也已做了下列增強功能：

- MacOS，在應用程式不會再有實作不同[AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem)行為會根據內容類型。 只要設定`Rate`屬性而且 AVFoundation 會決定當足夠的內容是可供播放不一。
- 新`AVPlayerLooper`類別可讓您更輕鬆地在播放期間迴圈指定的媒體項目。
- `AVAssetDownloadURLSession`類別可讓下載和更新播放 FairPlay 加密 HLS 資料流。

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>核心資料架構增強功能

核心資料架構 macOS 利也已做了下列增強功能：

- 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援並行判定為失敗，和擷取沒有序列化。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別會維護 SQLite 資料存放區集區。
- [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件與 SQLite WAL 日誌模式支援新的查詢產生的資料存放區功能在受管理物件內容 （木） 可以固定到未來擷取特定的資料庫版本和判定為失敗的交易。
- 使用高階`NSPersistenceContainer`參考`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他資源的核心資料設定。
- 已加入數個新的便利方法`NSManagedObject`以便更容易執行提取及建立子類別。

如需詳細資訊，請參閱 Apple[核心資料架構參考](https://developer.apple.com/reference/coredata)。

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Core 映像架構增強功能

Core 映像的架構 macOS 利也已做了下列增強功能：

- `ImageWithExtent`方法[CIFilter](https://developer.apple.com/reference/coreimage/cifilter)類別可以用來篩選作業中插入自訂處理。 Core 映像將會叫用指定的回呼之間篩選處理輸出的映像時，或顯示。
- 應用程式現在可以藉由之前和之後處理內外的色彩空間轉換處理 Core 映像內容的工作色彩空間以外的色彩空間中的映像。
- Core 映像核心現在可以要求特定像素輸出格式。
- 已新增下列新的映像篩選器： `CINinePartTitled`， `CINinePartStretched`， `CIHueSaturationValueGradient`，`CIEdgePreserveUpsampleFilter`和`CIClamp`。

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Mfc 架構增強功能

Mfc 架構 macOS 利也已做了下列增強功能：

- 使用[NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) API，用於代表、 轉換及顯示的最常見的實體單位許多這類大量長度、 速度、 持續時間和溫度。
- 使用[NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter)類別進行剖析和產生 ISO 8601 格式的日期。
- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別，例如持續時間，來比較間隔及測試間隔交集的日期和時間間隔計算。
- 使用[NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter)類別來剖析字串中的某個人的名稱的項目。
- 使用新[NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics)類別，以取得度量資訊的網路工作階段的 URL。

如需詳細資訊，請參閱 Apple [Foundation 版本資訊適用於 OS X v10.12 和 iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html)。

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>GameKit 架構增強功能

GameKit 架構 macOS 利也已做了下列增強功能：

- **Game Center 應用程式**已被取代而且 macOS 來移除。 如果應用程式使用 GameKit，它_必須_呈現自己的介面，以顯示 GameKit 功能，例如排行榜等等。 
- 新的僅限 iCloud 帳戶類型由[GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別。
- 新[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)類別提供的管理永續性資料儲存在遊戲中心的一般化的解決方案。 `GKGameSession` 會維護的播放清單和應用程式是負責的形式實作如何及何時會儲存參與者的日期，擷取或播放程式之間交換。 在許多情況下遊戲工作階段可以取代現有的開啟相符項，即時的相符項目或持續性方法儲存的遊戲。

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>GamePlayKit 架構增強功能

GamePlayKit 架構 macOS 利也已做了下列增強功能：

- 程序的雜訊產生已經加入，而且可用來增強自然尋找紋理中的真實性，加入真實度相機移動並說明產生豐富的遊戲世界。
- 使用空間資料分割的有效搜尋的遊戲世界資料分割。
- 新的 Monte Carlo 策劃師 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 已加入的獨占可能移動計算。
- 已加入新的決策樹 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)和[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 來增強遊戲建置 AI。
- 3D 支援已新增至現有的代理程式和使用新的路徑尋找行為[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)和[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)類別。
- 使用新[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)提供高效能、 自然尋找路徑的類別。
- 新[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)和[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)類別產生結合 GameplayKit 和 SpriteKit 比以往更為容易。

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>裸機架構增強功能

裸機架構 macOS 利也已做了下列增強功能：

- 現在可以使用 3D 應用程式和遊戲_鑲嵌式_來有效率地呈現複雜的場景和透過 GPU 的幾何。
- 若要建立獲得高度最佳化的場景的淺組合函式和資料，集合中使用函式特製化。
- 請提供更精細地控制資源配置，來最佳化效能的金屬堆積使用資源的應用程式和 Memoryless 呈現目標。

若要進一步了解，請參閱 Apple[金屬程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>模型 I/O 架構增強功能

模型 I/O 架構 macOS 利也已做了下列增強功能：

- 現在支援 USD 檔案格式。
- 使用新`MDLMaterialPropertyGraph`類別，以輕鬆地支援執行階段變更模型。
- 帶正負號距離的欄位已加入支援[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)類別。
- 使用新`MDLLightProbeIrradianceDataSource`類別，以協助 Light 探查位置中。

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>相片架構增強功能

相片架構 macOS 利也已做了下列增強功能：

- 即時相片編輯現已提供支援相片架構的應用程式和相片編輯擴充功能 (使用內部的相片和相機應用程式)。
- 使用新[PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext)類別，以編輯套用至視訊和仍然 Live 相片的內容。
- 使用[CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput)和[CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput)類別，以充分利用新 Core 映像處理器功能，來執行編輯。
- 若要支援 Live 相片[PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto)和[PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview)類別已從 iOS 移植到 macOS。

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>SceneKit 架構增強功能

SceneKit 架構 macOS 利也已做了下列增強功能：

- 現在包含新的實際基礎呈現 (PBR) 系統與撰寫簡單的資產比較實際的結果。
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

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>安全性架構增強功能

安全性架構 macOS 利也已做了下列增強功能：

- `SecKey`介面有現代化和跨所有平台 （iOS、 tvOS、 watchOS 及 macOS） 整合。

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>SpriteKit 架構增強功能

SpriteKit 架構 macOS 利也已做了下列增強功能：

- 2D、 2.5 D 和使用的側邊捲動遊戲 Tilemaps 現在支援的正方形、 六角和等並排顯示圖形`SKTileMapMode`， `SKTileGroup`，`SKTileGroupRule`和`SKTileSet`類別。
- 使用新`SKWarpGeometry`類別來自動縮放或扭曲[SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode)或[SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode)轉譯。 新[SKAction](https://developer.apple.com/reference/spritekit/skaction)類別可以用來建立動畫 warp 效果之間的轉換。
- 自訂的著色器可以提供屬性 (`SKAttribute`)，藉由提供屬性值使用著色器的每一個節點可以分別設定 (`SKAttributeValue`)。
- [SKView](https://developer.apple.com/reference/spritekit/skview)類別提供數種新方法，來提供更細微的控制時機和方式呈現的場景。

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>新的架構

MacOS 利也已加入下列架構：

- **對應方式 Framework** -此架構允許應用程式檢查互動 （例如位置或使用者動作），並根據該資訊採取動作。
- **SafariServices Framework** -此架構允許來開發應用程式擴充功能 （例如內容封鎖程式） 的 safari macOS 和 iOS 應用程式。

## <a name="related-links"></a>相關連結

- [Mac 範例](https://developer.xamarin.com/samples/mac/)
- [OS X 10.12 中最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
