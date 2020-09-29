---
title: 其他 macOS Sierra 架構變更
description: 本檔說明 macOS Sierra 所引進之現有架構的微小變更和增強功能。 它會檢查加速架構、AppKit、AVFoundation、核心資料、核心影像、基礎等的變更。
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 9bee1cb5108053dac74fcde4f4f5171c24c7ce1a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437214"
---
# <a name="additional-macos-sierra-framework-changes"></a>其他 macOS Sierra 架構變更

<a name="Accelerate-Framework-Enhancements"></a>

## <a name="accelerate-framework-enhancements"></a>加速架構的增強功能

MacOS Sierra 的加速架構已進行下列增強功能：

- 已新增 Quadrature (整數微積分) 。
- 已新增用來建立類神經網路的基本函數。
- 新增幾何述詞函式來測試兩個幾何物件的交集。

<a name="AppKit-Framework-Enhancements"></a>

## <a name="appkit-framework-enhancements"></a>AppKit 架構的增強功能

MacOS Sierra 的 AppKit 架構已進行下列增強功能：

- 的許多增強功能如下 `NSCollectionView` ：
  - 可折迭**區段**-可讓使用者將集合視圖區段折迭成單一水準資料列。
  - **浮動標題** ：標題和頁尾現在可以浮動 (在流程配置中，) 使用與 IOS 中 [UICOLLECTIONVIEW](https://developer.apple.com/reference/uikit/uicollectionview) 相同的 API。
  - 可**滾動的背景視圖**-您現在可以將集合視圖背景設定為與內容一起滾動。
- 延遲的視圖配置傳遞已經過優化並擴充。
- 拖放 API 現在包含新的 `NSFilePromiseProvider` 和 `NSFilePromiseReceiver` 支援拖曳群群聚的類別。
- 現有的控制項已加入數個便利的函式：
  - `NSButton` 包含新的函式，可用於建立推播按鈕、核取方塊和選項按鈕。
  - `NSTextField` 包含新的函式，可用於建立包裝和非換行標籤、屬性化標籤和可編輯的文字欄位。
  - `NSSegmentedControl` 包含新的函式，可從一組標籤或影像建立分段的控制項。
  - `NSSlider` 包含用來建立水平線性滑杆的新函式。
  - `NSImageView` 包含新的函式，可從指定的建立不可編輯的影像視圖 `NSImage` 。
- 已新增新的 `NSGridView` ，以自動將子視圖的集合配置至方格，其中包含可動態隱藏或顯示的可變大小資料列和資料行。

<a name="AVFoundation-Framework-Enhancements"></a>

## <a name="avfoundation-framework-enhancements"></a>AVFoundation 架構的增強功能

MacOS Sierra 的 AVFoundation 架構已進行下列增強功能：

- 在 macOS 中，應用程式不再需要根據內容類型來執行不同的 [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) 行為。 只要設定 `Rate` 屬性，AVFoundation 就會判斷是否有足夠的內容可在沒有拖延的情況下播放。
- 新 `AVPlayerLooper` 類別可讓您更輕鬆地在播放期間迴圈指定的媒體。
- `AVAssetDownloadURLSession`類別可讓您下載及稍後播放 FairPlay 加密的 HLS 資料流程。

<a name="Core-Data-Framework-Enhancements"></a>

## <a name="core-data-framework-enhancements"></a>核心資料架構的增強功能

以下是針對 macOS Sierra 的核心資料架構進行的增強：

- 根 [NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) 物件支援並行錯誤和未序列化的提取。
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator)類別會維護 SQLite 資料存放區的集區。
- 在 WAL 記錄模式中具有 SQLite 資料存放區的 [NSManagedObjectCoNtext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) 物件，可支援新的查詢產生功能，其中的 Managed 物件內容 (MOC) 可以釘選到特定的資料庫版本，以供未來提取和錯誤交易之用。
- 使用高階 `NSPersistenceContainer` 參考 `NSPersistentStoreCoordinator` 、 [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) 和其他核心資料設定資源。
- 已加入數個新的便利方法，可 `NSManagedObject` 讓您更輕鬆地執行提取和建立子類別。

如需詳細資訊，請參閱 Apple 的 [核心資料架構參考](https://developer.apple.com/reference/coredata)。

<a name="Core-Image-Framework-Enhancements"></a>

## <a name="core-image-framework-enhancements"></a>核心映射架構的增強功能

MacOS Sierra 的核心映射架構已進行下列增強功能：

- `ImageWithExtent` [CIFilter](https://developer.apple.com/reference/coreimage/cifilter)類別的方法可以用來將自訂處理插入篩選作業中。 核心影像會在處理影像以供輸出或顯示時，在篩選之間叫用指定的回呼。
- 應用程式現在可以在處理之前和之後來回轉換色彩空間，在核心影像內容的工作色彩空間之外的色彩空間中處理影像。
- 核心影像核心現在可以要求特定的圖元輸出格式。
- 已新增下列新的影像篩選： `CINinePartTitled` 、 `CINinePartStretched` 、 `CIHueSaturationValueGradient` `CIEdgePreserveUpsampleFilter` 和 `CIClamp` 。

<a name="Foundation-Framework-Enhancements"></a>

## <a name="foundation-framework-enhancements"></a>Foundation Framework 增強功能

MacOS Sierra 的基礎架構已進行下列增強功能：

- 使用 [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) API 來代表、轉換和顯示許多最常見的實體單位，例如品質、長度、速度、持續時間和溫度。
- 使用 [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) 類別來剖析和產生 ISO 8601 格式化日期。
- 使用新的 [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) 類別來進行日期和時間間隔計算（例如持續時間），以比較間隔和測試間隔交集。
- 您可以使用 [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) 類別，從字串剖析人員名稱的元素。
- 使用新的 [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) 類別來取得 URL 網路會話的度量。

如需詳細資訊，請參閱 [適用于 OS X v 10.12 和 iOS 10 的 Apple Foundation 版本](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html)資訊。

<a name="GameKit-Framework-Enhancements"></a>

## <a name="gamekit-framework-enhancements"></a>GameKit 架構的增強功能

MacOS Sierra 的 GameKit 架構已進行下列增強功能：

- **Game Center 的應用程式**已被取代，並已從 macOS 中移除。 如果應用程式使用 GameKit，它 _必須_ 有自己的介面，以顯示 GameKit 功能，例如排行榜等等。
- [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer)類別已實作為新的 iCloud 帳戶類型。
- 新的 [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) 類別提供通用的解決方案，可用來管理 Game Center 上的持續性資料存放區。 `GKGameSession` 會維護播放程式的清單，而應用程式會負責實作為參與者日期的儲存、抓取或在播放程式之間交換的方式和時間。 在許多情況下，遊戲會話可以取代現有的回合型相符專案、即時比對或持續性的遊戲儲存方法。

<a name="GamePlayKit-Framework-Enhancements"></a>

## <a name="gameplaykit-framework-enhancements"></a>GamePlayKit 架構的增強功能

MacOS Sierra 的 GamePlayKit 架構已進行下列增強功能：

- 已新增程式性雜訊產生，可用來增強自然的材質中的真實感、增加相機移動的真實感，以及協助產生豐富的遊戲世界。
- 使用空間分割來分割遊戲世界資料，以進行有效率的搜尋。
- 已新增新的 Monte Carlo 策略家 ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) ，以進行徹底的移動計算。
- 新的決策樹 API 已新增 ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) 和 [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) ，以增強遊戲建立 AI。
- 已使用新的 [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) 和 [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) 類別，將3d 支援新增至現有的代理程式和路徑尋找行為。
- 使用新的 [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) 類別，以提供高效能且自然的路徑。
- 新的 [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) 和 [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) 類別讓 GameplayKit 和 SpriteKit 的結合比以往更容易。

<a name="Metal-Framework-Enhancements"></a>

## <a name="metal-framework-enhancements"></a>金屬架構的增強功能

MacOS Sierra 的裸機架構已進行下列增強功能：

- 3D 應用程式和遊戲現在可以使用 _鑲嵌_ 來有效率地透過 GPU 呈現複雜的場景和幾何。
- 使用函數特製化，為場景建立高度優化的材質和輕量組合函式集合。
- 提供更精細的資源配置控制，以使用資源堆積和 Memoryless 轉譯目標，將裸機型應用程式的效能優化。

若要深入瞭解，請參閱 Apple 的 [裸機程式設計指南](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221)。

<a name="ModelIO-Framework-Enhancements"></a>

## <a name="model-io-framework-enhancements"></a>模型 i/o 架構的增強功能

MacOS Sierra 的模型 i/o 架構已進行下列增強：

- 現在支援 USD 檔案格式。
- 使用新 `MDLMaterialPropertyGraph` 類別可輕鬆支援模型的執行時間變更。
- 已將已簽署的距離欄位支援新增至 [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) 類別。
- 使用新的 `MDLLightProbeIrradianceDataSource` 類別來協助進行輕量探查放置。

<a name="Photos-Framework-Enhancements"></a>

## <a name="photos-framework-enhancements"></a>相片架構的增強功能

MacOS Sierra 的相片架構已進行下列增強功能：

- 即時照片編輯現在適用于支援相片架構的應用程式，以及相片編輯延伸模組 (可在相片和相機應用程式) 內使用。
- 使用新的 [PHLivePhotoEditingCoNtext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) 類別，將編輯套用至影片，並同時套用至即時相片的內容。
- 使用 [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) 和 [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) 類別，以利用新的核心映射處理器功能來執行編輯。
- 為了支援即時相片， [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) 和 [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) 類別已從 iOS 移植到 macOS。

<a name="SceneKit-Framework-Enhancements"></a>

## <a name="scenekit-framework-enhancements"></a>SceneKit 架構的增強功能

MacOS Sierra 的 SceneKit 架構已進行下列增強功能：

- 現在包含新的實際轉譯 (.PBR) 系統，以提供更簡單的資產撰寫，以獲得更實際的結果。
- 使用新的 [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) 陰影模型來產生各式各樣的真實陰影效果，同時只需要三個基本屬性 (`Diffuse` `Metalness` 和 `Roughness`) 。
- 由於 .PBR 陰影與以環境為基礎的光源效果最佳，所以請使用 `LightingEnvironment` 屬性將以影像為基礎的光源指派給 tan 的整個場景。
- 您 `IESProfileURL` 可以使用屬性來匯入真實世界值（例如) 流明中的強度 (）和色溫 (（以角度為單位的) ）來定義光源的真實裝置。
- [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera)類別可以使用 HDR 功能和效果來提供更高的真實性。 使用調適型曝光來建立自動效果，或使用 vignetting、色彩 fringing 和色彩來將 filmatic 效果新增到遊戲中。
- .PBR 和 HDR 攝影機的功能提供比傳統轉譯技術更好的結果，因此，SceneKit 現在會線上性色彩空間中執行所有色彩計算 (使用單色裝置上的 P3 色彩範圍顯示) 。
- SceneKit 現在的色彩會藉由讀取色彩設定檔資訊來符合所有色彩。
- SceneKit 會以線性 RGB 色彩空間來解讀所有著色器類型的色彩元件值。
- 由於 SceneKit 會讀取及調整材質影像中的色彩設定檔資訊，請使用所有影像的資產目錄，以確保提供這項資訊。
- 您可以 `SCNDisableLinearSpaceRendering` 在應用程式中指定和索引鍵，以停用線性色彩空間轉譯和寬色彩 `SCNDisableWideGamut` `Info.plist` 。
- 建立任意多邊形 primates (從檔案載入或以程式設計方式產生) ，以使用新的 [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/documentation/scenekit/scngeometryprimitivetype/scngeometryprimitivetypepolygon?language=objc) 類別來指定幾何。

<a name="Security-Framework-Enhancements"></a>

## <a name="security-framework-enhancements"></a>安全性架構的增強功能

MacOS Sierra 的安全性架構已進行下列增強功能：

- `SecKey`介面已跨所有平臺現代化並整合， (iOS、tvOS、watchOS 和 macOS) 。

<a name="SpriteKit-Framework-Enhancements"></a>

## <a name="spritekit-framework-enhancements"></a>SpriteKit 架構的增強功能

MacOS Sierra 的 SpriteKit 架構已進行下列增強功能：

- Tilemaps 現在支援使用 `SKTileMapMode` 、 `SKTileGroup` `SKTileGroupRule` 和類別之2d、2.5 d 和側邊滾動遊戲的正方形、六邊形和等位磚圖形 `SKTileSet` 。
- 使用新 `SKWarpGeometry` 類別來延展或扭曲 [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) 或 [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) 轉譯。 新的 [SKAction](https://developer.apple.com/reference/spritekit/skaction) 類別可用來將變形效果之間的轉換動畫。
- 自訂著色器可以提供屬性 (`SKAttribute`) ，而這些屬性可由使用著色器的每個節點個別設定，方法是提供 () 的屬性值 `SKAttributeValue` 。
- [SKView](https://developer.apple.com/reference/spritekit/skview)類別提供數個新方法，可讓您更精細地控制場景的呈現時間和方式。

<a name="New-Frameworks"></a>

## <a name="new-frameworks"></a>新架構

下列架構已新增至 macOS Sierra：

- **意圖架構** -此架構可讓應用程式檢查互動 (例如) 的位置或使用者動作，並根據該資訊採取動作。
- **SafariServices framework** -此架構可讓應用程式開發 Safari (的應用程式延伸模組，例如 MacOS 和 iOS 的內容封鎖程式) 。

## <a name="related-links"></a>相關連結

- [Mac 範例](/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [OS X 10.12 的新功能](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)