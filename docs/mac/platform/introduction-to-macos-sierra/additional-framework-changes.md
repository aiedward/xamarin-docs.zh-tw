---
title: 其他 macOS Sierra 架構變更
description: 本文件說明微幅的變更和增強功能，在 macOS Sierra 中導入的現有架構。 它會檢查變更的 「 增進洞悉 framework、 AppKit、 AVFoundation、 核心資料、 Core 映像、 Foundation，以及更多。
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6ed827c018931e5b79887dc355f136e2a84623d6
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031623"
---
# <a name="additional-macos-sierra-framework-changes"></a>其他 macOS Sierra 架構變更

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>加速 Framework 增強功能

加速適用於 macOS Sierra 架構已做了下列增強功能：

- 已新增的 Quadrature （整數微積分）。
- 已新增建構類神經網路的基本功能。
- 新增幾何述詞函式來測試兩個幾何物件的交集等項目。

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>AppKit Framework 增強功能

適用於 macOS Sierra AppKit Framework 已做了下列增強功能：

- 數個增強功能`NSCollectionView`例如：
    - **可摺疊區段**-可讓使用者摺疊成單一的水平資料列的集合檢視區段。
    - **浮動標頭**-頁首和頁尾可以現在浮動 （在 流程版面配置） 使用的 API 相同[UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview)在 iOS 中。
    - **可捲動的背景檢視**-現在可以設定集合檢視背景隨著內容一起捲動。
- 延後的檢視版面配置階段已最佳化並擴充。
- 拖放 API 現在包含新`NSFilePromiseProvider`和`NSFilePromiseReceiver`群群聚的類別，以支援拖曳。
- 為了方便起見建構函式已新增至現有的控制項：
    -  `NSButton` 包含新的建構函式建立按鈕、 核取方塊和選項按鈕。
    -  `NSTextField` 包含新的建構函式來建立文繞圖功能和非換行標籤中，屬性化的標籤和可編輯的文字欄位。
    -  `NSSegmentedControl` 包含新的建構函式來建立從群組的標籤或映像的分段的控制項。
    -  `NSSlider` 包含新的建構函式來建立水平的線性滑桿。
    -  `NSImageView` 包含新的建構函式來建立從非可編輯的映像檢視給定`NSImage`。
-  新`NSGridView`已新增至子檢視的集合，使用 變數方格大小的資料列和資料行可以動態地隱藏或顯示的自動版面配置。

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>AVFoundation Framework 增強功能

適用於 macOS Sierra AVFoundation Framework 已做了下列增強功能：

- 在 macOS 應用程式不會再有實作不同[AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem)行為取決於內容類型。 只要設定`Rate`屬性和 AVFoundation 會決定當足夠的內容是適用於播放不含懸置。
- 新`AVPlayerLooper`類別可讓您更輕鬆地循環播放在播放期間，指定的媒體項目。
- `AVAssetDownloadURLSession`類別是用來下載和更新版本播放 FairPlay 加密的 HLS 串流。

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>核心資料 Framework 增強功能

核心資料架構，適用於 macOS Sierra 已做了下列增強功能：

- 根[NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援並行的判定為失敗，並擷取沒有序列化。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別維護的 SQLite 資料存放區集區。
- [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件與 SQLite WAL 日誌模式支援新的查詢產生的資料存放區功能，受管理物件內容 （模式） 可以釘選到未來擷取特定的資料庫版本和失敗的交易。
- 使用高階`NSPersistenceContainer`參考`NSPersistentStoreCoordinator`， [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心資料設定資源。
- 已新增數個新的便利方法`NSManagedObject`更容易執行提取，並建立子類別。

如需詳細資訊，請參閱 Apple [Core Data Framework 參考](https://developer.apple.com/reference/coredata)。

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>核心映像 Framework 增強功能

適用於 macOS Sierra 核心映像架構已做了下列增強功能：

- `ImageWithExtent`方法[CIFilter](https://developer.apple.com/reference/coreimage/cifilter)類別可以用來篩選作業中插入自訂處理。 Core 映像將會叫用指定的回撥處理輸出的映像時的篩選器之間，或顯示。
- 應用程式現在可以將核心映像內容的處理色彩空間之外的色彩空間中的映像處理之前和之後處理流入和流出的色彩空間轉換。
- Core 映像核心現在可以要求特定的像素輸出格式。
- 已新增下列新的映像篩選器： `CINinePartTitled`， `CINinePartStretched`， `CIHueSaturationValueGradient`，`CIEdgePreserveUpsampleFilter`和`CIClamp`。

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Foundation Framework 增強功能

適用於 macOS Sierra Foundation 架構已做了下列增強功能：

- 使用[NSDimentions](https://developer.apple.com/reference/foundation/nsdimension)表示，轉換和顯示的最常見的實體單位許多這類 API 質量、 長度、 速度、 持續時間和溫度。
- 使用[NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter)類別進行剖析和產生 ISO 8601 格式的日期。
- 使用新[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別，例如持續時間，來比較的間隔，並測試間隔交集的日期和時間間隔計算。
- 使用[NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter)類別來剖析從字串的人員名稱的項目。
- 使用新[NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics)類別，以取得度量資訊的網路工作階段的 URL。

如需詳細資訊，請參閱 Apple [Foundation 版本資訊適用於 OS X v10.12 和 iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html)。

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>GameKit Framework 增強功能

適用於 macOS Sierra GameKit Framework 已做了下列增強功能：

- **Game Center 應用程式**已被取代，並從 macOS 中移除。 如果應用程式使用 GameKit，它_必須_呈現它自己的介面，以顯示 GameKit 功能，例如排行榜等等。 
- 已實作新的僅限 iCloud 帳戶類型所[GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別。
- 新[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)類別管理在 Game Center 的持續性資料儲存體提供的通用的解決方案。 `GKGameSession` 維護一份清單的播放程式和應用程式是負責的形式實作如何及何時會儲存參與者的日期，擷取或播放程式之間交換。 在許多情況下遊戲工作階段可以取代現有的回合式相符項目、 即時的相符項目或持續性方法儲存的遊戲。

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>GamePlayKit Framework 增強功能

適用於 macOS Sierra GamePlayKit Framework 已做了下列增強功能：

- 程序的雜訊產生已新增，而且可用來增強自然尋找紋理中的真實性，加入真實性觀景窗移動並協助產生豐富的遊戲世界。
- 使用空間資料分割來分割的遊戲世界資料進行有效率的搜尋。
- 新的 Monte Carlo 策略家 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) 已加入的詳盡的可能移動計算。
- 已新增新的決策樹 API ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)並[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) 來增強遊戲建置 AI。
- 3D 支援已新增至現有的代理程式和使用新的路徑尋找行為[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)並[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)類別。
- 使用新[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)類別，以提供高效能、 自然尋找路徑。
- 新[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)並[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)類別進行結合 GameplayKit 和 SpriteKit 比以往更容易。

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>金屬 Framework 增強功能

Metal Framework，適用於 macOS Sierra 已做了下列增強功能：

- 現在可以使用 3D 應用程式和遊戲_鑲嵌式_來有效率地呈現複雜的場景和透過 GPU 的幾何。
- 若要建立的 material 和場景光線的組合函式高度最佳化的集合中使用函式特製化。
- 提供更精細地控制資源配置，以最佳化效能的金屬為基礎的堆積使用資源的應用程式和 Memoryless 轉譯目標。

若要進一步了解，請參閱 Apple [Metal 程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>模型 I/O Framework 增強功能

模型的 I/O 架構，適用於 macOS Sierra 已做了下列增強功能：

- 現在支援美元檔案格式。
- 使用新`MDLMaterialPropertyGraph`類別，以輕鬆地支援執行階段變更模型。
- 帶正負號距離欄位已加入支援[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)類別。
- 使用新`MDLLightProbeIrradianceDataSource`類別，以協助 Light 探查的放置。

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>相片 Framework 增強功能

適用於 macOS Sierra 相片 Framework 已做了下列增強功能：

- 即時的相片編輯現已提供支援相片架構的應用程式和相片編輯擴充功能 (用於內部的相片和相機應用程式)。
- 使用新[PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext)類別，以同時適用於編輯影片和仍然 Live 相片的內容。
- 使用[CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput)並[CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput)類別，以利用新的核心映像處理器功能，可執行編輯。
- 若要支援 Live 相片[PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto)並[PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview)類別已從 iOS 移植到 macOS。

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>SceneKit Framework 增強功能

適用於 macOS Sierra SceneKit Framework 已做了下列增強功能：

- 現在包含新的實際基礎呈現 (PBR) 系統，更簡單的資產編寫更實際的結果。
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

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>安全性架構的增強功能

安全性架構，適用於 macOS Sierra 已做了下列增強功能：

- `SecKey`介面已經現代化，並統一跨所有平台 （iOS、 tvOS、 watchOS 和 macOS）。

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>SpriteKit Framework 增強功能

適用於 macOS Sierra 的 SpriteKit 架構已做了下列增強功能：

- 2D、 2.5 D 和側邊捲動的遊戲使用 Tilemaps 現在支援的方形，六角等的並排顯示圖案`SKTileMapMode`， `SKTileGroup`，`SKTileGroupRule`和`SKTileSet`類別。
- 使用新`SKWarpGeometry`類別來自動縮放或扭曲[SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode)或是[SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode)轉譯。 新[SKAction](https://developer.apple.com/reference/spritekit/skaction)類別可用來以動畫顯示 warp 效果之間的轉換。
- 自訂著色器可以提供屬性 (`SKAttribute`)，藉由提供屬性值中使用著色器的每一個節點可以分別設定 (`SKAttributeValue`)。
- [SKView](https://developer.apple.com/reference/spritekit/skview)類別提供數種新方法，來提供更細微的控制何時及如何呈現的場景。

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>新的架構

MacOS Sierra 中已新增了下列架構：

- **Intents Framework** -此架構可讓應用程式，以檢查互動 （例如位置或使用者動作），並根據該資訊採取動作。
- **SafariServices Framework** -此架構讓應用程式開發 （例如內容封鎖程式） 的 Safari macOS 及 iOS 的應用程式延伸模組。

## <a name="related-links"></a>相關連結

- [Mac 範例](https://developer.xamarin.com/samples/mac/)
- [在 OS X 10.12 最新消息](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
