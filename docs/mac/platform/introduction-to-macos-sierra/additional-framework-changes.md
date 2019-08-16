---
title: 其他 macOS Sierra 架構變更
description: 本檔說明 macOS Sierra 中引進的現有架構的微小變更和增強功能。 它會檢查加速架構、AppKit、AVFoundation、核心資料、核心影像、基礎等等的變更。
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 5c8b2b20d037edd35fd3c50d1418df2110cb8e4c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528886"
---
# <a name="additional-macos-sierra-framework-changes"></a>其他 macOS Sierra 架構變更

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>加速架構增強功能

已針對 macOS Sierra 的加速架構進行下列增強功能:

- 已新增 Quadrature (整數微積分)。
- 已新增用來建立類神經網路的基本函數。
- 已新增幾何述詞函式, 以測試兩個幾何物件的交集。

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>AppKit Framework 增強功能

MacOS Sierra 的 AppKit 架構已進行下列增強功能:

- 下列幾項`NSCollectionView`增強功能:
    - 可折迭的**區段**-允許使用者將集合視圖區段折迭成單一水準資料列。
    - **浮動標頭**-頁首和頁尾現在可以使用與 IOS 中[UICOLLECTIONVIEW](https://developer.apple.com/reference/uikit/uicollectionview)相同的 API 來浮動 (在流程版面配置中)。
    - 可**滾動的背景視圖**-集合 Views 背景現在可以設定為與內容一起滾動。
- 延遲的視圖配置傳遞已經過優化和擴充。
- 拖放 API 現在包含新`NSFilePromiseProvider`的和`NSFilePromiseReceiver`類別, 以支援拖曳 flocking。
- 現有的控制項中已加入數個便利的函式:
    - `NSButton`包含用來建立推播按鈕、核取方塊和選項按鈕的新程式。
    - `NSTextField`包含新的函式, 用於建立換行和非包裝標籤、屬性化標籤和可編輯的文字欄位。
    - `NSSegmentedControl`包含新的函式, 可從一組標籤或影像建立分段的控制項。
    - `NSSlider`包含用來建立水平線性滑杆的新函式。
    - `NSImageView`包含新的函式, 可從指定`NSImage`的建立不可編輯的影像視圖。
- 已將`NSGridView`新的新增至將子視圖集合自動設定到具有可變大小之資料列和資料行的方格中, 可以動態地隱藏或顯示。

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>AVFoundation Framework 增強功能

MacOS Sierra 的 AVFoundation 架構已進行下列增強功能:

- 在 macOS 中, 應用程式不再需要根據內容類型來執行不同的[AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem)行為。 只要設定`Rate`屬性, AVFoundation 就會決定是否有足夠的內容可供播放, 而不需要停止。
- 新`AVPlayerLooper`的類別可讓您更輕鬆地在播放期間迴圈指定的媒體片段。
- `AVAssetDownloadURLSession`類別可讓您下載及稍後播放 FairPlay 加密的 HLS 串流。

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>核心資料架構增強功能

MacOS Sierra 的核心資料架構已進行下列增強功能:

- 根[NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援並行錯誤和未序列化的提取。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別會維護 SQLite 資料存放區的集區。
- WAL 記錄模式中具有 SQLite 資料存放區的[NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext)物件支援新的查詢產生功能, 其中 Managed 物件內容 (MOC) 可以釘選到特定的資料庫版本, 以供未來提取和錯誤交易使用。
- 使用高階`NSPersistenceContainer`來`NSPersistentStoreCoordinator`參考、 [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel)和其他核心資料設定資源。
- 已新增`NSManagedObject`數個新的便利方法, 讓您更輕鬆地執行提取和建立子類別。

如需詳細資訊, 請參閱 Apple 的[核心資料架構參考](https://developer.apple.com/reference/coredata)。

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>核心映射架構增強功能

MacOS Sierra 的核心映射架構已進行下列增強功能:

- [CIFilter 類別](https://developer.apple.com/reference/coreimage/cifilter)的方法可以用來將自訂處理插入篩選作業`ImageWithExtent`中。 核心映射會在處理輸出或顯示的影像時, 叫用篩選之間的指定回呼。
- 應用程式現在可以在處理之前和之後來回轉換色彩空間, 以在核心影像內容的工作色彩空間之外的色彩空間中處理影像。
- 核心映射核心現在可以要求特定的圖元輸出格式。
- 已新增下列新的影像篩選: `CINinePartTitled`、 `CINinePartStretched`、 `CIHueSaturationValueGradient` `CIEdgePreserveUpsampleFilter`和`CIClamp`。

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Foundation Framework 增強功能

MacOS Sierra 的基礎架構已進行下列增強功能:

- 使用[NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) API 來代表、轉換和顯示許多最常見的實體單位, 例如大量、長度、速度、持續時間和溫度。
- 使用[NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter)類別來剖析和產生 ISO 8601 格式化的日期。
- 使用新的[NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval)類別來進行日期和時間間隔計算 (例如持續期間), 以比較間隔和測試間隔交集。
- 使用[NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter)類別, 從字串剖析人員名稱的元素。
- 使用新的[NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics)類別來取得 URL 網路會話的計量。

如需詳細資訊, 請參閱[適用于 OS X v 10.12 和 iOS 10 的 Apple Foundation 版本](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html)資訊。

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>GameKit Framework 增強功能

MacOS Sierra 的 GameKit 架構已進行下列增強功能:

- **Game Center 應用程式**已被取代, 並已從 macOS 中移除。 如果應用程式使用 GameKit, 它_必須_提供自己的介面來顯示 GameKit 功能, 例如排行榜等。 
- [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別已實作為僅限 iCloud 的新帳戶類型。
- 新的[GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession)類別提供通用的解決方案來管理 Game Center 上的持續性資料存放區。 `GKGameSession`會維護一份玩家清單, 而應用程式會負責執行如何以及何時儲存、抓取或交換玩家之間的參與者日期。 在許多情況下, 遊戲會話可以取代現有的回合型比對、即時符合或持續的遊戲儲存方法。

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>GamePlayKit Framework 增強功能

MacOS Sierra 的 GamePlayKit 架構已進行下列增強功能:

- 已新增程式產生雜訊, 並可用來增強自然外觀材質的真實性、增加攝影機移動的真實感, 並協助產生豐富的遊戲世界。
- 使用空間分割來分割遊戲世界的資料, 以進行有效率的搜尋。
- 已加入新的 Monte Carlo 策略家 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)), 以取得徹底可能的移動計算。
- 新的決策樹 API 已新增 ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree)和[GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)), 以增強遊戲建立 AI。
- 已使用新的[GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d)和[GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d)類別, 將3d 支援新增至現有的代理程式和路徑尋找行為。
- 使用新的[GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph)類別來提供高效能、自然外觀的路徑。
- 新的[GKScene](https://developer.apple.com/reference/gameplaykit/gkscene)和[GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent)類別結合了 GameplayKit 和 SpriteKit, 使其比以往更容易。

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>金屬架構增強功能

MacOS Sierra 的金屬架構已進行下列增強功能:

- 3D 應用程式和遊戲現在可以使用_鑲嵌_式, 透過 GPU 有效率地呈現複雜的場景和幾何。
- 使用函數特製化, 為場景建立高度優化的材質和光源組合功能集合。
- 使用資源堆積和 Memoryless 轉譯目標, 提供更精細的資源配置控制, 以將金屬型應用程式的效能優化。

若要深入瞭解, 請參閱 Apple 的[金屬程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>模型 i/o 架構增強功能

MacOS Sierra 的模型 i/o 架構已進行下列增強功能:

- 現在支援 USD 檔案格式。
- 使用新`MDLMaterialPropertyGraph`的類別, 輕鬆地支援模型的執行時間變更。
- 已將已簽署的距離欄位支援新增至[MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray)類別。
- 使用新`MDLLightProbeIrradianceDataSource`的類別來協助進行較輕的探查位置。

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>相片架構增強功能

MacOS Sierra 的相片架構已進行下列增強功能:

- 即時相片編輯現在適用于支援相片架構和相片編輯延伸模組 (在相片和相機應用程式內使用) 的應用程式。
- 使用新的[PHLivePhotoEditingCoNtext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext)類別, 將編輯同時套用至影片, 同時套用至即時相片的內容。
- 使用[CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput)和[CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput)類別, 利用新的核心映射處理器功能來執行編輯。
- 為了支援即時相片, [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto)和[PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview)類別已從 iOS 移植到 macOS。

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>SceneKit Framework 增強功能

MacOS Sierra 的 SceneKit 架構已進行下列增強功能:

- 現在包含新的實際轉譯 (.PBR) 系統, 可提供更簡單的資產製作, 以獲得更實際的結果。
- 使用新的[SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased)陰影模型來組成各式各樣的實際陰影效果, 同時只需要三個基本屬性 (`Diffuse`、 `Metalness`和`Roughness`)。
- 由於 .pbr 陰影最適合使用以環境為基礎的光源, `LightingEnvironment`因此, 請使用屬性將影像型光源指派給 tan 整個場景。
- `IESProfileURL`使用屬性, 即可匯入定義光源的真實世界光源, 例如濃度 (流明) 和色溫度 (以度為單位)。
- [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera)類別可使用 HDR 特性和效果來提供更高的真實性。 使用調適型曝光來建立自動效果, 或使用 vignetting、色彩 fringing 和色彩評分, 將 filmatic 效果新增到遊戲中。
- 與傳統轉譯技術相比, .PBR 和 HDR 攝影機功能提供更好的結果, 因此, SceneKit 現在會線上性色彩空間中執行所有色彩計算 (在寬色彩裝置顯示器上使用 P3 色彩色域)。
- [立即 SceneKit] 色彩會藉由閱讀色彩設定檔資訊來符合所有色彩。
- SceneKit 會針對所有著色器類型, 以線性 RGB 色彩空間來解讀色彩元件值。
- 由於 SceneKit 會讀取和調整材質影像中的色彩設定檔資訊, 因此請針對所有影像使用資產目錄, 以確保提供這項資訊。
- 藉由在應用程式的`SCNDisableLinearSpaceRendering` `Info.plist`中指定和`SCNDisableWideGamut`鍵, 可以停用線性色彩空間呈現和寬色彩。
- 建立任意多邊形 primates (從檔案載入或以程式設計方式產生), 以使用新的[SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon)類別來指定幾何。

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>安全性架構增強功能

MacOS Sierra 的安全性架構已進行下列增強功能:

- `SecKey`介面已在所有平臺 (iOS、tvOS、watchOS 和 macOS) 中現代化並整合。

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>SpriteKit Framework 增強功能

MacOS Sierra 的 SpriteKit 架構已進行下列增強功能:

- `SKTileMapMode`Tilemaps 現在支援使用`SKTileGroup` `SKTileGroupRule` 、和`SKTileSet`類別之2d、2.5 d 和側邊滾動遊戲的正方形、六邊形和等式磚圖形。
- 使用新`SKWarpGeometry`的類別來延展或扭曲[SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode)或[SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode)轉譯。 新的[SKAction](https://developer.apple.com/reference/spritekit/skaction)類別可以用來建立變形效果之間的轉換動畫。
- 自訂著色器可以提供`SKAttribute`屬性 (), 您可以藉由提供屬性值 (`SKAttributeValue`), 分別由使用著色器的每個節點加以設定。
- [SKView](https://developer.apple.com/reference/spritekit/skview)類別提供數個新的方法, 可讓您更精細地控制場景的呈現時機和方式。

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>新架構

下列架構已新增至 macOS Sierra:

- **意圖架構**-此架構可讓應用程式檢查互動 (例如位置或使用者動作), 並根據該資訊採取動作。
- **SafariServices Framework** -此架構可讓應用程式針對 MacOS 和 IOS 開發 Safari 的應用程式擴充功能 (例如內容封鎖程式)。

## <a name="related-links"></a>相關連結

- [Mac 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [OS X 10.12 中的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
