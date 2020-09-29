---
title: iOS 9 簡介
description: 本文介紹適用于 Xamarin 的 iOS 9 中所有新的和修改過的 Api 和功能。 iOS 開發人員。
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 75da06af3444cdb83f00c1089d43ca4d34f2177e
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436812"
---
# <a name="introduction-to-ios-9"></a>iOS 9 簡介

_本文介紹適用于 Xamarin 的 iOS 9 中所有新的和修改過的 Api 和功能。 iOS 開發人員。_

![IOS 9 標誌](images/ios9-sml.png)

Apple 已在 iOS 9 中新增數個新的 Api 和服務，以及現有功能的許多增強功能。

## <a name="3d-touch"></a>3D Touch

IOS 9 和 iPhone 6s 和 iPhone 6s Plus 的新手，3D Touch 為您的 iOS 應用程式新增壓力敏感性手勢。 有了 3D Touch，iPhone 應用程式現在不僅可以分辨使用者觸及裝置的畫面，也可以判斷使用者造成及回應不同壓力等級的壓力。

3D Touch 為您的應用程式提供下列功能：

- **壓力敏感度** -應用程式現在可以測量使用者觸及畫面的困難或光線，並利用該資訊。 例如，繪製應用程式可能會根據使用者觸及螢幕的困難程度，使線條變粗或更細。
- **查看和** 快顯-您的應用程式現在可以讓使用者與其資料互動，而不需要流覽至其目前的內容。 藉由在螢幕上按下 [難]，就可以 *查看* 他們感興趣的專案 (例如) 預覽訊息。 藉由按下較困難的專案，就可以將其 *彈出* 至專案。
- **快速動作** -在使用者以滑鼠右鍵按一下傳統型應用程式中的專案時，請考慮可彈出的快速動作，例如可彈出的內容功能表。 使用快速動作，您可以從 iOS 裝置上的主畫面圖示，為應用程式中的函式新增常用、快速且容易存取的快捷方式。

若要深入瞭解，請參閱我們的 [3D Touch 指南簡介](~/ios/platform/3d-touch.md) 。

## <a name="app-transport-security"></a>應用程式傳輸安全性

IOS 9 (的新 ATS) 在網際網路資源之間強制執行安全連線 (例如，應用程式的後端伺服器) 和您的應用程式。 ATS 可確保所有網際網路通訊都符合安全的連線最佳作法，藉此防止直接透過您的應用程式或其使用的程式庫，來意外洩漏機密資訊。

由於預設會在針對 iOS 9 和 OS X 10.11 所建立的應用程式中啟用 ATS (El Capitan) ，因此使用 [NSUrlConnection](xref:Foundation.NSUrlConnection)、 [CFUrl](xref:CoreFoundation.CFUrl) 或 [NSUrlSession](xref:Foundation.NSUrlSession) 的所有連線都必須遵守 ATS 安全性需求。 如果您的連線不符合這些需求，則會失敗併發生例外狀況。

若要深入瞭解 ATS，請參閱我們的 [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md) 指南。

<a name="multitasking"></a>

## <a name="multitasking-for-ipad"></a>適用於 iPad 的多工

在 iOS 9 中，Apple 已針對在特定 iPad 硬體上同時執行兩個應用程式，增加多工支援。 因此，您的 Xamarin iOS 應用程式不會再假設它們是在任何指定時間執行的唯一應用程式，或是可以存取裝置的全螢幕或資源。

透過下列功能可支援 iPad 的多工：

- **滑** 出-可讓使用者在 [滑出] 面板中暫時執行第二個 iOS 應用程式， (在畫面的右側或左邊，根據語言方向) ，其涵蓋大約25% 的主要應用程式目前正在執行。 投影片只適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。
- **分割視圖** -在支援的 ipad 硬體上 (ipad Air 2、ipad 迷你4和 ipad Pro 僅) ，使用者可以挑選第二個應用程式，並與目前執行中的應用程式並存執行，以進行分割螢幕模式。 使用者可以控制每個應用程式所佔用主畫面的百分比。
- **圖片** -針對播放影片內容的應用程式，現在可以在可移動且可調整大小的視窗中播放影片，以浮動在目前正在 iOS 裝置上執行的其他應用程式。 使用者可以完全掌控此視窗的大小和位置。 圖片中的圖片僅適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。

若要深入瞭解 iOS 9 的新多工功能，請參閱我們 [的多工（適用于 iPad](~/ios/platform/multitasking.md) ）指南。

## <a name="new-contacts-and-contacts-ui-frameworks"></a>新的連絡人和連絡人 UI 架構

隨著 iOS 9 的推出，Apple 已發行兩個新的架構： [連絡人](xref:Contacts) 和 [ContactsUI](xref:ContactsUI)，以取代 iOS 8 和更早版本所使用的現有通訊錄和通訊錄 UI 架構。

這些新的物件導向架構提供下列各項：

- **連絡人** -提供對使用者連絡人資訊的 Xamarin 存取權。 因為大部分的應用程式只需要唯讀存取，所以此架構已針對安全線程、唯讀存取進行優化。
- **ContactsUI** –提供可在 ios 裝置上顯示、編輯、選取及建立連絡人的 XAMARIN ios UI 元素。

如需詳細資訊，請參閱我們的 [連絡人和連絡人 UI](~/ios/platform/contacts.md) 檔。

## <a name="new-search-apis"></a>新的搜尋 Api

IOS 9 中的搜尋已擴充，可提供絕佳的新方式來存取您的 Xamarin iOS 應用程式內的資訊。 使用新的搜尋 Api，您可以透過焦點和 Safari 搜尋結果、交付和 Siri 提醒和建議，讓您的應用程式內容可供搜尋。 這可讓使用者快速存取應用程式內的活動和資訊。

此外，新的搜尋 Api 可讓您更輕鬆地在應用程式中整合搜尋，而不需要事先搜尋執行體驗。 因此，Apple 宣稱通常需要數小時的時間，才能使用應用程式搜尋來全面搜尋 iOS 9 應用程式的內容。

如需詳細資訊，請參閱我們的 [搜尋增強功能](~/ios/platform/search/index.md) 檔。

## <a name="new-stack-view"></a>新增堆疊視圖

Stack View 控制項 ([UIStackView](xref:UIKit.UIStackView) 利用自動設定和大小類別的強大功能，以水準或) 垂直方式管理 (子檢視的堆疊，動態回應 iOS 裝置的方向和螢幕大小。

藉由使用 Stack View 控制項，可大幅減少配置使用者介面所需的工作量。 所有附加至堆疊視圖的子檢視配置都會根據開發人員定義的屬性（例如軸、分佈、對齊和間距）自動進行管理。

如需詳細資訊，請參閱 [堆疊視圖](~/ios/user-interface/controls/uistackview.md) 檔的簡介。

## <a name="collection-view-changes"></a>集合視圖變更

在 iOS 9 中，[收集視圖 ([UICollectionView](xref:UIKit.UICollectionView) ] 現在可新增預設的手勢辨識器和數個新的支援方法，以支援從方塊中拖曳專案的重新排列。

您可以使用這些新的方法，輕鬆地在您的收集視圖中執行拖放作業，並可選擇在重新排列程式的任何階段中自訂專案外觀。

若要深入瞭解 iOS 9 的收集視圖變更，請參閱我們的 [收集視圖變更](~/ios/user-interface/controls/uicollectionview.md) 指南。

## <a name="game-enhancements"></a>遊戲增強功能

在 iOS 9 中，Apple 對遊戲 Api 進行了幾項技術改進，讓您更輕鬆地在您的 Xamarin iOS 應用程式中執行遊戲圖形和音訊。 這些功能包括透過高階架構輕鬆開發，以及利用 iOS 裝置 GPU 的強大功能，以低層級的增強功能提升速度和圖形能力。

這包括 GameplayKit、ReplayKit、模型 i/o、MetalKit 和金屬效能著色器，以及裸機、SceneKit 和 SpriteKit 的新增強功能。

如需詳細資訊，請參閱我們的 [遊戲增強功能](~/ios/platform/gaming/index.md) 檔。

## <a name="homekit-framework-changes"></a>HomeKit Framework 變更

IOS 8 中引進的 [HomeKit](xref:HomeKit) 架構可讓您設定及控制各種啟用 HomeKit 的配件， (例如，從 Xamarin 應用程式) 的自動燈光、門鎖和車庫門車庫遙控器。 除了容易設定和設定之外，HomeKit 的配件還可以透過說 Siri 命令來控制。

在 iOS 9 中，Apple 讓設定變得更容易，並擴充支援的配件類型，並提供更多的配件互動 (例如，透過 iCloud) 從遠端控制配件。

如需詳細資訊，請參閱 HomeKit、 [HomeKitIntro IOS 範例應用程式](/samples/xamarin/ios-samples/homekit-homekitintro)和 Apple [HomeKit](https://developer.apple.com/homekit/)檔的[簡介](~/ios/platform/homekit.md)。

## <a name="handoff-framework-changes"></a>提交架構變更

IOS 8 和 OS X Yosemite 中的 Apple 引進的 (也稱為持續性)  (10.10) 以使用者的身分在其中一個裝置上啟動活動 (iOS 或 Mac) ，並在其裝置上繼續相同的活動 (如使用者的 iCloud 帳戶) 所識別。

IOS 9 中的交付已擴充，也支援新的增強式搜尋功能。 如需詳細資訊，請參閱我們的 [搜尋增強功能](~/ios/platform/search/index.md) 檔。 如需有關使用遞交的詳細資訊，請參閱我們的 [交付檔簡介](~/ios/platform/handoff.md) 。

## <a name="new-extension-points"></a>新的擴充點

在 iOS 8 中，Apple 引進了延伸模組，也就是由作業系統在標準內容中顯示的程式庫，例如在通知中心內、當使用者要求鍵盤時，或當他們編輯相片時。

在 iOS 9 中，Apple 藉由提供數個新的 _延伸_ 模組來擴充延伸模組支援，以定義使用原則，並提供可在給定區域內運作的 api，如下所示：

- **新的音訊單位擴充點** –使用此擴充點來提供音訊效果、音樂儀器、音效產生器等，以用於其他音訊單元主機應用程式 (例如 GarageBand) 。 這個擴充點也可讓您在 App Store 上 (音訊外掛程式) 銷售 _音訊單位_ 。
- **新的索引維護擴充點** -使用此擴充點可支援重新索引應用程式資料，而不需要重新開機應用程式。
- **新的網路擴充點** (這些需要 Apple) 的特殊許可權：
  - **應用程式 Proxy 提供者延伸** 模組-使用此擴充點來執行自訂的透明用戶端網路 Proxy。
  - **篩選 Data Provider/篩選控制項提供者延伸** 模組-使用這些擴充點在裝置上執行動態網路內容篩選。
  - 封**包通道提供者延伸**模組-使用此擴充點來執行自訂 VPN 通道通訊協定用戶端。
- **新的 Safari 擴充點**：
  - **內容封鎖延伸模組** -使用此延伸模組來定義使用者流覽網頁時，不會顯示的封鎖內容清單。
  - **共用連結擴充** 功能-使用此擴充點可讓您在 Safari 的共用連結中，查看您的應用程式內容。

如需詳細資訊，請參閱我們的延伸模組和 Apple[應用程式延伸模組程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)檔的[簡介](~/ios/platform/extensions.md)。

## <a name="keychain-enhancements"></a>Keychain 增強功能

在 iOS 9 中，Apple 強化了 Keychain，為安全記憶體保護區和更多專案保護選項提供新的加密金鑰類型，如下所示：

- 新的 Touch ID 條件約束，可在修改指紋資料庫時使 Keychain 專案失效。
- 允許以觸控識別碼或密碼建立存取控制清單專案的新條件約束。
- 新的驗證內容，可讓您叫用與呼叫分開的驗證 `SecItem` 。
- 存取控制清單熵 (針對應用程式提供的 keychain 專案加密使用應用程式密碼選項) 。
- 支援透過屬性) ，在安全記憶體保護區 (內產生和使用金鑰 `kSecAttrTokenIDSecureEnclave` 。

如需詳細資訊，請參閱 [Xamarin 中的 TOUCH id 和臉部識別碼](~/ios/platform/touch-id-face-id.md)。

## <a name="right-to-left-language-support"></a>從右至左的語言支援

在 iOS 9 中，Apple 藉由提供從右至左語言的完整支援，讓您更輕鬆地呈現翻轉的使用者介面。 其中包括下列項目：

- 標準 [UIKit](xref:UIKit) 控制項會根據 iOS 裝置的地區設定和語言設定，自動從右至左切換。
- [UIView](xref:UIKit.UIView)類別提供屬性，可讓您定義指定的視圖在從右至左翻轉時應如何顯示。
- 使用[UIImage](xref:UIKit.UIImage)類別的[FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection)屬性，以程式設計方式翻轉影像的能力。

如需詳細資訊，請參閱 Apple [支援的由右至左語言](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17) 檔。

## <a name="additional-framework-changes"></a>其他架構變更

除了上述的主要變更之外，Apple 還針對 iOS 9 的數個現有架構進行了修改和改進，包括下列各項：

- AV 基礎架構
- AVKit 架構
- CloudKit 架構
- 基礎架構
- 交付架構
- HealthKit 架構
- HomeKit 架構
- 本機驗證架構
- MapKit 架構
- PassKit 架構
- Safari 服務架構
- UIKit 架構

如需詳細資訊，請參閱我們的 [其他 iOS 9 Framework 變更](~/ios/platform/introduction-to-ios9/additional-framework-changes.md) 檔。

## <a name="deprecated-apis-and-functions"></a>已淘汰的 Api 和函式

Apple 已在 iOS 9 中淘汰下列 Api 和函式：

- **通訊錄 & 通訊錄 UI** -這些 api 已由 Contact 和 contact UI framework 取代。 如需詳細資訊，請參閱我們的 [連絡人和連絡人 UI](~/ios/platform/contacts.md) 檔。
- **CBCentralManager** - `RetrievePeripherals` `RetrieveConnectedPeripherals` `CBCentralManager` 已在 iOS 9 中移除類別的和方法。 呼叫這些方法會導致應用程式在配對附屬應用程式或啟動應用程式時損毀。
- **FetchAllChanges** - `FetchAllChanges` 類別的 `CKFetchRecordChangesOperation` 已被折舊，將在 iOS 9 中移除。
- **Media player** -媒體播放機架構已在 iOS 9 中淘汰。 請改用 AVKit 或 AV 基礎 Api。

如需特定 API 棄用功能的完整清單，請參閱 Apple 的 [iOS 9.0 Api 差異](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222) 檔。

## <a name="ios-9-sample-apps"></a>iOS 9 範例應用程式

我們有一些 [iOS 9 特定範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9) 可供您開始使用：

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](/samples/xamarin/ios-samples/ios9-metalperformanceshadershelloworld)
- [MusicMotion](/samples/xamarin/ios-samples/ios9-musicmotion)
- [PhotoProgress](/samples/xamarin/ios-samples/ios9-photoprogress)
- [SegueCatalog](/samples/xamarin/ios-samples/ios9-seguecatalog)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

也請查看這些範例的 iOS 部分 (隨附 Mac OS X 版本！ ) ：

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)

## <a name="related-links"></a>相關連結

- [iOS 9 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS9)
- [3D Touch 簡介](~/ios/platform/3d-touch.md)
- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
- [適用於 iPad 的多工](~/ios/platform/multitasking.md)
- [連絡人和連絡人 UI](~/ios/platform/contacts.md)
- [新的搜尋 Api](~/ios/platform/search/index.md)
- [Stack 視圖簡介](~/ios/user-interface/controls/uistackview.md)
- [集合視圖變更](~/ios/user-interface/controls/uicollectionview.md)
- [遊戲增強功能](~/ios/platform/gaming/index.md)
- [HomeKit 簡介](~/ios/platform/homekit.md)
- [交付簡介](~/ios/platform/handoff.md)
- [其他 iOS 9 架構變更](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [疑難排解](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [適用于開發人員的 iOS 9](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 的新功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [將您的 Xamarin iOS 應用程式更新為 iOS9 (影片) ](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)