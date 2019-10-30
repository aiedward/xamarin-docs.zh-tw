---
title: iOS 9 簡介
description: 本文介紹 iOS 9 中適用于 Xamarin iOS 開發人員的所有新的和修改過的 Api 和功能。
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: db398947b5f13963febae7d25a7898af21600813
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031788"
---
# <a name="introduction-to-ios-9"></a>iOS 9 簡介

_本文介紹 iOS 9 中適用于 Xamarin iOS 開發人員的所有新的和修改過的 Api 和功能。_

![](images/ios9-sml.png "The iOS 9 logo")

Apple 在 iOS 9 中新增了數個新的 Api 和服務，以及現有功能的許多增強功能。

## <a name="3d-touch"></a>3D Touch

IOS 9 和 iPhone 6s 和 iPhone 6s Plus 的新手，3D Touch 將壓力機密手勢新增至您的 iOS 應用程式。 有了 3D Touch，iPhone 應用程式現在可以得知使用者是否觸及裝置的螢幕，也能瞭解使用者造成和回應不同壓力等級的壓力。

3D Touch 為您的應用程式提供下列功能：

- **壓力敏感度**-應用程式現在可以測量使用者觸及螢幕的困難或光線，並利用該資訊。 例如，繪製應用程式可以根據使用者觸及螢幕的程度，使線條變粗或變小。
- **查看和**快顯-您的應用程式現在可以讓使用者與資料互動，而不需要流覽其目前的內容。 只要按下螢幕上的 [hard]，他們就可以*查看*他們感興趣的專案（例如預覽訊息）。 只要按下 [困難]，他們就可以*彈出*到專案中。
- **快速動作**-將快速動作視為可在使用者以滑鼠右鍵按一下桌面應用程式中的專案時彈出的內容功能表。 使用快速動作，您可以從 iOS 裝置上的主畫面圖示，為應用程式中的函式新增一般、快速且容易存取的快捷方式。

若要深入瞭解，請參閱我們的[3D Touch 指南簡介](~/ios/platform/3d-touch.md)。

## <a name="app-transport-security"></a>應用程式傳輸安全性

應用程式傳輸安全性（ATS）是 iOS 9 的新手，會在網際網路資源（例如應用程式的後端伺服器）和您的應用程式之間強制執行安全的連接。 ATS 可確保所有網際網路通訊都符合安全連線最佳作法，藉此防止意外洩漏敏感性資訊，無論是直接透過您的應用程式或它所耗用的程式庫。

由於預設會在針對 iOS 9 和 OS X 10.11 （El Capitan）建立的應用程式中啟用 ATS，因此使用[NSUrlConnection](xref:Foundation.NSUrlConnection)、 [CFUrl](xref:CoreFoundation.CFUrl)或[NSUrlSession](xref:Foundation.NSUrlSession)的所有連線都將受限於 ATS 安全性需求。 如果您的連線不符合這些需求，則會失敗並產生例外狀況。

若要深入瞭解 ATS，請參閱我們的[應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)指南。

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>適用於 iPad 的多工

在 iOS 9 中，Apple 已新增多工支援，可在特定 iPad 硬體上同時執行兩個應用程式。 因此，您的 Xamarin iOS 應用程式不會再假設它們是在任何指定時間執行的唯一應用程式，或可以存取裝置的全螢幕或資源。

透過下列功能可支援 iPad 的多工作業：

- **滑過**-允許使用者在 [滑出面板] 中暫時執行第二個 iOS 應用程式（根據語言方向，在畫面的右側或左側），其中涵蓋約為目前正在執行之主要應用程式的25%。 滑過僅適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。
- **分割視圖**-在支援的 ipad 硬體上（僅限 ipad Air 2、ipad 迷你4和 ipad Pro），使用者可以挑選第二個應用程式，並在分割螢幕模式中與目前執行中的應用程式並存執行。 使用者可以控制每個應用程式所佔用主畫面的百分比。
- **圖片**-針對播放影片內容的應用程式，現在可以在可移動且可調整大小的視窗中播放影片，並將其浮動到目前正在 iOS 裝置上執行的其他應用程式。 使用者可以完全掌控這個視窗的大小和位置。 圖片中的圖片僅適用于 iPad Pro、iPad Air、iPad Air 2、iPad 迷你2、iPad 迷你3或 iPad 迷你4。

若要深入瞭解 iOS 9 的新多工作業能力，請參閱我們[的多工（iPad](~/ios/platform/multitasking.md) ）指南。

## <a name="new-contacts-and-contacts-ui-frameworks"></a>新連絡人和連絡人 UI 架構

隨著 iOS 9 的推出，Apple 發行了兩個新的架構：[連絡人](xref:Contacts)和[ContactsUI](xref:ContactsUI)，以取代 iOS 8 和之前版本所使用的現有通訊錄和通訊錄 UI 架構。

這些新的物件導向架構提供下列各項：

- **連絡人**–提供 Xamarin iOS 對使用者連絡人資訊的存取權。 因為大部分的應用程式只需要唯讀存取，所以此架構已針對安全線程的唯讀存取進行優化。
- **ContactsUI** –提供 XAMARIN ios UI 元素，以顯示、編輯、選取及建立 iOS 裝置上的連絡人。

如需詳細資訊，請參閱我們的[連絡人和連絡人 UI](~/ios/platform/contacts.md)檔。

## <a name="new-search-apis"></a>新的搜尋 Api

IOS 9 中的搜尋功能已擴充，可提供絕佳的新方式來存取您的 Xamarin iOS 應用程式內的資訊。 使用新的搜尋 Api，您可以透過焦點和 Safari 搜尋結果、遞交和 Siri 提醒和建議，讓應用程式的內容可供搜尋。 這可讓使用者快速存取應用程式內的活動和資訊。

此外，新的搜尋 Api 可讓您更輕鬆地在應用程式中整合搜尋，而不需要預先搜尋的執行體驗。 因此，Apple 宣告通常需要幾個小時的時間，才能使用應用程式搜尋，讓 iOS 9 應用程式的內容可供通用搜尋。

如需詳細資訊，請參閱我們的[搜尋增強功能](~/ios/platform/search/index.md)檔。

## <a name="new-stack-view"></a>新增堆疊視圖

Stack 視圖控制項（[UIStackView](xref:UIKit.UIStackView)會利用自動設定和大小類別的功能來管理子檢視堆疊（水準或垂直），以動態方式回應 iOS 裝置的方向和螢幕大小。

藉由使用 Stack 視圖控制項，配置使用者介面所需的工作量會大幅降低。 附加至堆疊視圖的所有子檢視的配置都會根據開發人員定義的屬性（例如軸、分佈、對齊和間距）自動進行管理。

如需詳細資訊，請參閱我們的[Stack 視圖簡介](~/ios/user-interface/controls/uistackview.md)檔。

## <a name="collection-view-changes"></a>集合視圖變更

在 iOS 9 中，集合視圖（[UICollectionView](xref:UIKit.UICollectionView)現在支援新增新的預設手勢辨識器和數個新的支援方法，以從方塊中拖曳專案。

您可以使用這些新方法，輕鬆地在您的集合視圖中執行拖放重新排序，並可讓您選擇在重新排列程式的任何階段自訂專案外觀。

若要深入瞭解 iOS 9 的集合視圖變更，請參閱我們的[集合視圖變更](~/ios/user-interface/controls/uicollectionview.md)指南。

## <a name="game-enhancements"></a>遊戲增強功能

在 iOS 9 中，Apple 對遊戲 Api 進行了幾項技術改良，讓您更輕鬆地在您的 Xamarin iOS 應用程式中執行遊戲圖形和音訊。 這些功能包括透過高階架構的輕鬆開發，以及運用 iOS 裝置的 GPU 功能來改善速度和圖形功能，並提供低層級的增強功能。

這包括 GameplayKit、ReplayKit、Model i/o、MetalKit 和金屬效能著色器，以及金屬、SceneKit 和 SpriteKit 的新增強功能。

如需詳細資訊，請參閱我們的[遊戲增強功能](~/ios/platform/gaming/index.md)檔。

## <a name="homekit-framework-changes"></a>HomeKit 架構變更

IOS 8 中引進的[HomeKit](xref:HomeKit)架構可讓您從 Xamarin iOS 應用程式設定及控制各種已啟用 HomeKit 功能的配件（例如自動化光源、門鎖和車庫門 openers）。 除了容易設定和設定之外，HomeKit 的配件也可以透過語音 Siri 命令來控制。

在 iOS 9 中，Apple 讓設定變得更簡單，擴充支援的配件類型，並提供更多的配件互動（例如透過 iCloud 遠端控制附件）。

如需詳細資訊，請參閱我們的[HomeKit 簡介](~/ios/platform/homekit.md)、 [HomeKitIntro IOS 範例應用程式](https://docs.microsoft.com/samples/xamarin/ios-samples/homekit-homekitintro)和 Apple 的[HomeKit](https://developer.apple.com/homekit/)檔。

## <a name="handoff-framework-changes"></a>提交架構變更

IOS 8 和 OS X Yosemite （10.10）中的 Apple 引進了遞交（也稱為持續性），這種方式可讓使用者在其中一個裝置（iOS 或 Mac）上啟動活動，並在其裝置上繼續該相同的活動（如使用者的 iClou 所識別d 帳戶）。

IOS 9 中的遞交已擴大，也支援新的增強型搜尋功能。 如需詳細資訊，請參閱我們的[搜尋增強功能](~/ios/platform/search/index.md)檔。 如需有關使用遞交的詳細資訊，請參閱我們的[遞交簡介](~/ios/platform/handoff.md)檔。

## <a name="new-extension-points"></a>新的擴充點

在 iOS 8 中，Apple 引進了擴充功能—作業系統在標準內容中呈現的程式庫，例如在通知中心內、使用者要求鍵盤或編輯相片時。

在 iOS 9 中，Apple 提供數個定義使用原則的新_擴充點_，並提供可在指定區域內運作的 api，以擴充擴充功能支援，如下所示：

- **新的音訊單元擴充點**–使用此延伸模組點來提供音訊效果、音樂儀器、音效產生器等等，以便在其他音訊單元主機應用程式中使用（例如 GarageBand）。 這個擴充點也可讓您在 App Store 上銷售_音訊單元_（音訊外掛程式）。
- **新的索引維護擴充點**-使用此延伸模組可支援重新編制應用程式資料的索引，而不需要重新開機應用程式。
- **新的網路擴充點**（需要 Apple 的特殊許可權）：
  - **應用程式 Proxy 提供者延伸**模組—使用此延伸模組點來執行自訂透明用戶端網路 Proxy。
  - **篩選 Data Provider/篩選控制提供者延伸**模組-使用這些擴充點，在裝置上執行動態網路內容篩選。
  - 封**包通道提供者延伸**模組—使用此延伸模組點來執行自訂 VPN 通道通訊協定用戶端。
- **新的 Safari 擴充點**：
  - **內容封鎖延伸模組**—使用此擴充點來定義使用者流覽網站時，不會顯示的已封鎖內容清單。
  - **共用連結延伸**模組—使用此延伸模組點，即可在 Safari 的共用連結中，查看應用程式的內容。

如需詳細資訊，請參閱我們的[擴充功能簡介](~/ios/platform/extensions.md)和 Apple 的[應用程式延伸模組程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)檔。

## <a name="keychain-enhancements"></a>Keychain 增強功能

在 iOS 9 中，Apple 已增強 Keychain，為 Secure 記憶體保護區和更多專案保護選項提供新的加密金鑰類型，如下所示：

- 當修改指紋資料庫時，會使 Keychain 專案失效的新 Touch ID 條件約束。
- 新的條件約束，允許只使用 Touch ID 或密碼建立存取控制清單專案。
- 新的驗證內容，可讓您叫用獨立于 `SecItem` 呼叫的驗證。
- 存取控制列出應用程式提供之 keychain 專案加密的熵（使用 [應用程式密碼] 選項）。
- 支援在安全記憶體保護區（透過 `kSecAttrTokenIDSecureEnclave` 屬性）內產生和使用金鑰。

如需詳細資訊，請參閱我們的[觸控識別碼簡介](~/ios/platform/touchid.md)檔。

## <a name="right-to-left-language-support"></a>由右至左語言支援

在 iOS 9 中，Apple 藉由提供從右至左語言的完整支援，讓您更輕鬆地呈現翻轉的使用者介面。 其中包括下列項目：

- 標準[UIKit](xref:UIKit)控制項會根據 iOS 裝置地區設定和語言設定，從右至左自動翻轉。
- [UIView](xref:UIKit.UIView)類別提供屬性，可讓您定義當從右至左翻轉時，應該如何顯示指定的視圖。
- 使用[UIImage](xref:UIKit.UIImage)類別的[FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection)屬性，以程式設計方式翻轉影像的能力。

如需詳細資訊，請參閱 Apple 的[支援從右至左的語言](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17)檔。

## <a name="additional-framework-changes"></a>其他架構變更

除了我們先前討論過的重大變更之外，Apple 也已針對 iOS 9 的數個現有架構進行修改和改進，包括下列各項：

- AV 基礎架構
- AVKit 架構
- CloudKit 架構
- 基礎架構
- 遞交架構
- HealthKit 架構
- HomeKit 架構
- 本機驗證架構
- MapKit 架構
- PassKit 架構
- Safari 服務架構
- UIKit 架構

如需詳細資訊，請參閱我們的[其他 iOS 9 架構變更](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)檔。

## <a name="deprecated-apis-and-functions"></a>已淘汰的 Api 和函式

Apple 已淘汰 iOS 9 中的下列 Api 和功能：

- **通訊錄 & 通訊錄 ui** -這些 api 已由 Contact 和 contact ui 架構取代。 如需詳細資訊，請參閱我們的[連絡人和連絡人 UI](~/ios/platform/contacts.md)檔。
- **CBCentralManager** -iOS 9 中已移除 `CBCentralManager` 類別的 `RetrievePeripherals` 和 `RetrieveConnectedPeripherals` 方法。 呼叫這些方法會導致應用程式在配對附件或應用程式啟動時損毀。
- **FetchAllChanges** -`CKFetchRecordChangesOperation` 類別的 `FetchAllChanges` 已進行過折舊，將在 iOS 9 中移除。
- **媒體播放機**-iOS 9 中的媒體播放機架構已被取代。 請改用 AVKit 或 AV Foundation Api。

如需特定 API 棄用功能的完整清單，請參閱 Apple 的[iOS 9.0 API 差異](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222)檔。

## <a name="ios-9-sample-apps"></a>iOS 9 範例應用程式

我們有一些[iOS 9 特定的範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)可供您開始使用：

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-metalperformanceshadershelloworld)
- [MusicMotion](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-musicmotion)
- [PhotoProgress](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-photoprogress)
- [SegueCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-seguecatalog)
- [System.windows.forms.toolstrip.stackview](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

另請參閱這些範例的 iOS 部分（隨附 Mac OS X 版本！）：

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)

## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [3D Touch 簡介](~/ios/platform/3d-touch.md)
- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
- [適用於 iPad 的多工](~/ios/platform/multitasking.md)
- [連絡人和連絡人 UI](~/ios/platform/contacts.md)
- [新的搜尋 Api](~/ios/platform/search/index.md)
- [堆疊視圖簡介](~/ios/user-interface/controls/uistackview.md)
- [集合視圖變更](~/ios/user-interface/controls/uicollectionview.md)
- [遊戲增強功能](~/ios/platform/gaming/index.md)
- [HomeKit 簡介](~/ios/platform/homekit.md)
- [遞交簡介](~/ios/platform/handoff.md)
- [其他 iOS 9 架構變更](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [疑難排解](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [iOS 9 開發人員](https://developer.apple.com/ios/pre-release/)
- [IOS 9.0 中的新功能](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [將您的 Xamarin iOS 應用程式更新為 iOS9 （影片）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
