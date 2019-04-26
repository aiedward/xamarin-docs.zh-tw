---
title: iOS 9 簡介
description: 這篇文章會介紹的所有新的及修改 Api 和功能可在 iOS 9 中適用於 Xamarin.iOS 開發人員。
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: d0ad25a1ecff7262b9b4b41a5f9d73a5931bbd1c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946222"
---
# <a name="introduction-to-ios-9"></a>iOS 9 簡介

_這篇文章會介紹的所有新的及修改 Api 和功能可在 iOS 9 中適用於 Xamarin.iOS 開發人員。_

![](images/ios9-sml.png "IOS 9 標誌")

Apple 已新增數個新的 Api 和服務，在 iOS 9 及現有功能的許多增強功能。

## <a name="3d-touch"></a>3D Touch

IOS 9 和 iPhone 6s 和 iPhone 6s 新手此外，3D 觸控，請將您的 iOS 應用程式的壓力機密的筆勢。 使用 3D 觸控，iPhone 應用程式現在就可以向不只在使用者觸碰裝置的畫面上，它可以也感測使用者 exerting 多少壓力並回應不同的壓力層級。

3D Touch 提供您的應用程式的下列功能：

- **壓力敏感度**-應用程式現在可以測量嚴格程度或淺使用者會觸碰該項資訊的畫面並利用。 比方說，繪製應用程式可以進行線條較粗或 thinner 根據如何強制使用者會觸碰螢幕。
- **查看和快顯**-您的應用程式現在可以讓使用者與資料互動，而不必巡覽到其目前的內容。 藉由按下 固定在螢幕上，他們可以*查看*在他們感興趣 （例如預覽訊息） 的項目。 藉由按下 困難，他們可以*快顯*到項目。
- **快速動作**-認為的快速動作，例如，不可以是快顯總當使用者以滑鼠右鍵按一下桌面應用程式中的項目內容功能表。 使用快速動作，您可以新增常見、 可快速及輕鬆存取捷徑函式應用程式中從 iOS 裝置上的 [首頁] 畫面圖示。

若要深入了解，請參閱我們[3D 觸控簡介](~/ios/platform/3d-touch.md)指南。

## <a name="app-transport-security"></a>應用程式傳輸安全性

新增 iOS 9，App Transport Security (ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。 ATS 可確保所有的網際網路通訊符合保護連線安全的最佳作法，藉此防止意外洩露機密的資訊，直接透過您的應用程式或它正在使用的程式庫。

因為在建置適用於 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有連線的應用程式預設會啟用 ATS [NSUrlConnection](xref:Foundation.NSUrlConnection)， [CFUrl](xref:CoreFoundation.CFUrl)或是[NSUrlSession](xref:Foundation.NSUrlSession)會受限於ATS 安全性需求。 如果您的連線不符合這些需求，它們將會失敗並發生例外狀況。

若要深入了解 ATS，請參閱我們[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)指南。

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>適用於 iPad 的多工

含 iOS 9，Apple 已新增對特定的 iPad 硬體上，同時執行兩個應用程式的多工支援。 如此一來，您的 Xamarin.iOS 應用程式不會再假設它們是在任何給定時間執行的唯一應用程式，或他們擁有存取權的全螢幕或裝置的資源。

適用於 iPad 的多工支援透過下列功能：

- **投影片透過**-可讓使用者暫時面板 （無論是在 [依語言方向] 畫面的左右端），它涵蓋了大約 25%的目前執行的主要應用程式出一張投影片中執行的第二個的 iOS 應用程式。 投影片上是僅適用於 iPad Pro，iPad Air、 iPad 空氣 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4。
- **分割檢視**-支援的 iPad 的硬體上 (iPad 空氣 2，iPad Mini 4 和 iPad 僅限 Pro)，使用者可以挑選的第二個應用程式，並執行它並排顯示目前正在執行的應用程式，在分割畫面模式。 使用者可以控制主畫面中，每個應用程式所佔的百分比。
- **在圖中的圖片**-播放視訊內容，現在，影片會播放可移動且可調整大小的視窗會漂浮在目前在 iOS 裝置上執行的其他應用程式中的應用程式。 使用者可以完全控制的大小和位置，此視窗。 圖片中的圖片是僅適用於 iPad Pro，iPad Air、 iPad 空氣 2、 iPad Mini 2、 iPad Mini 3 或 iPad Mini 4。

若要深入了解新的多工能力的 iOS 9，請參閱我們[適用於 iPad 的多工](~/ios/platform/multitasking.md)指南。

## <a name="new-contacts-and-contacts-ui-frameworks"></a>新的連絡人和連絡人 UI 架構

使用 iOS 9 的引進，Apple 已發行兩個新的架構[連絡人](xref:Contacts)並[ContactsUI](xref:ContactsUI)、 取代現有的通訊錄。 和地址通訊錄 UI 架構供 iOS 8 和較早版本。

這些新的物件導向的架構提供下列項目：

- **連絡人**– 提供 Xamarin.iOS 存取使用者的連絡資訊。 因為大部分的應用程式只需要唯讀存取權，這個架構已經過最佳化的執行緒安全的唯讀存取。
- **ContactsUI** – 提供 Xamarin.iOS UI 項目，以顯示、 編輯、 選取和 iOS 裝置上建立的連絡人。

如需詳細資訊，請參閱我們[連絡人以及連絡人 UI](~/ios/platform/contacts.md)文件。


## <a name="new-search-apis"></a>新的搜尋 Api

搜尋已展開於 iOS 9，提供絕佳的新方法，來存取您的 Xamarin.iOS 應用程式內的資訊。 使用新的搜尋服務 Api，您可以讓您的應用程式內容可搜尋的焦點以及 Safari 搜尋結果，遞移式及 Siri 提醒及建議。 這可讓使用者快速存取活動和您的應用程式內的資訊。

此外，新的搜尋服務 Api 讓您更輕鬆地將搜尋-在您的應用程式，而不需要實作的先前的搜尋體驗整合。 因為這個緣故，Apple 會宣告它通常只需要幾小時的時間讓 iOS 9 應用程式的內容都可供搜尋使用應用程式內搜尋。

如需詳細資訊，請參閱我們[搜尋增強功能](~/ios/platform/search/index.md)文件。

## <a name="new-stack-view"></a>新的堆疊檢視

堆疊檢視控制項 ([UIStackView](xref:UIKit.UIStackView)運用動態回應 iOS 裝置的方向和螢幕大小自動版面配置和大小類別，來管理子檢視的堆疊 （水平或垂直） 的力量。

使用堆疊檢視控制項的工作量所需的使用者介面就會大幅減少版面配置。 附加至堆疊檢視的所有子檢視的配置是根據開發人員定義的屬性，例如軸、 發佈、 對齊及間距自動進行管理。

如需詳細資訊，請參閱我們[堆疊檢視簡介](~/ios/user-interface/controls/uistackview.md)文件。


## <a name="collection-view-changes"></a>集合檢視變更

在 iOS 9、 集合檢視中 ([UICollectionView](xref:UIKit.UICollectionView)現在支援拖放藉由新增新的預設筆勢辨識器和數個新的支援方法重新排列現成的項目。

使用這些新方法，可以輕鬆地實作拖放以重新排列集合檢視中和已自訂重新排序的程序任何階段中的項目外觀的選項。

若要深入了解 iOS 9 的集合檢視變更，請參閱我們[集合檢視變更](~/ios/user-interface/controls/uicollectionview.md)指南。

## <a name="game-enhancements"></a>遊戲的增強功能

含 iOS 9，Apple 已對幾項技術改良遊戲 Api，讓您更輕鬆地在您的 Xamarin.iOS 應用程式中實作的遊戲圖形和音訊。 這些包括這兩種方便開發的高階架構和 iOS 裝置的 GPU 經改善的速度和低層級的增強功能的圖形功能的力量。

這包括 GameplayKit、 ReplayKit、 模型 I/O、 MetalKit 和金屬效能著色器以及金屬、 SceneKit 和 SpriteKit 的新的增強功能。

如需詳細資訊，請參閱我們[遊戲的增強功能](~/ios/platform/gaming/index.md)文件。

## <a name="homekit-framework-changes"></a>HomeKit 架構變更

[HomeKit](xref:HomeKit) iOS 8 中引入架構讓您能夠設定和控制各種啟用 HomeKit 附屬應用程式 （例如自動化的號誌、 門鎖和車庫門 openers） 從 Xamarin.iOS 應用程式。 除了輕鬆安裝和設定，可以透過語音 Siri 命令控制 HomeKit 配件。

在 iOS 9 中，Apple 已變得非常容易設定、 展開 附屬應用程式支援，並提供更多的配件互動 （例如控制從遠端透過 iCloud 附屬應用程式） 的類型。

如需詳細資訊，請參閱我們[HomeKit 簡介](~/ios/platform/homekit.md)， [HomeKitIntro iOS 範例應用程式](https://developer.xamarin.com/samples/monotouch/HomeKit/HomeKitIntro/)與 Apple [HomeKit](https://developer.apple.com/homekit/)文件。

## <a name="handoff-framework-changes"></a>遞移式架構變更

遞交 （也就是持續性） 是由 Apple 在 iOS 8 和 OS X Yosemite (10.10) 引入做為針對其裝置 （iOS 或 Mac） 的其中一個開始活動，並繼續相同的活動，在他們的裝置 （如使用者的 iClou 所識別的另一個使用者的方式d 帳戶)。

增強的搜尋功能時，已在 iOS 9，也支援新的、 擴充遞交。 如需詳細資訊，請參閱我們[搜尋增強功能](~/ios/platform/search/index.md)文件。 如需有關使用遞移式的詳細資訊，請參閱我們[遞移式簡介](~/ios/platform/handoff.md)文件。

## <a name="new-extension-points"></a>新的擴充點

在 iOS 8 中，Apple 已引進延伸模組，會由作業系統在標準的內容中，例如在通知中心內，當使用者要求是鍵盤，或當他們在編輯相片的程式庫。

含 iOS 9，Apple 會藉由提供數個新擴充延伸模組支援_擴充點_，定義使用原則，並提供 Api，用於指定的區域內，如下所示：

- **新的音訊單元延伸模組點**– 使用這個擴充點，以便使用於其他音訊單元主機應用程式 （例如 GarageBand) 提供音訊效果、 樂器、 音效的產生器 」 等。 這個擴充點也可讓您銷售_音訊單位_（音訊外掛程式） 上的 App Store。
- **新的索引維護擴充點**— 若要支援重新編製索引的應用程式資料而不需要應用程式重新啟動之後使用這個擴充點。
- **新的網路擴充點**（這些都需要從 Apple 的特殊權限）：
    - **應用程式 Proxy 提供者擴充功能**— 若要實作自訂透明的用戶端網路 proxy 中使用這個擴充點。
    - **篩選資料提供者 / 篩選控制項提供者擴充功能**-使用這些擴充點來實作動態網路內容篩選的裝置。
    - **封包通道提供者延伸模組**— 若要實作自訂的 VPN 通道通訊協定的用戶端使用這個擴充點。
- **新的 Safari 擴充點**:
    - **內容封鎖副檔名**— 若要定義一份封鎖的內容，將不會顯示當使用者瀏覽網站時使用這個擴充點。
    - **共用連結擴充功能**— 為了能夠檢視您的應用程式中的 Safari 共用連結的內容中使用這個擴充點。

如需詳細資訊，請參閱我們[延伸模組簡介](~/ios/platform/extensions.md)與 Apple[應用程式擴充功能程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)文件。

## <a name="keychain-enhancements"></a>金鑰鏈的增強功能

在 iOS 9 中，Apple 已增強的金鑰鏈，以提供新的加密金鑰的類型安全飛地和更多的項目保護選項，如下所示：

- 新的 Touch ID 條件約束指紋資料庫遭到修改時使金鑰鏈的項目。
- 新的條件約束，允許只能建立使用 Touch ID 或密碼的存取控制清單項目。
- 新的驗證內容，可讓您叫用驗證分開`SecItem`呼叫。
- 應用程式提供的金鑰鏈項目加密的存取控制清單 （使用 [應用程式密碼] 選項） 的 entropy。
- 支援產生及使用內部安全飛地索引鍵 (透過`kSecAttrTokenIDSecureEnclave`屬性)。

如需詳細資訊，請參閱我們[Touch ID 的簡介](~/ios/platform/touchid.md)文件。


## <a name="right-to-left-language-support"></a>從右至左語言支援

在 iOS 9 中，Apple 已呈現翻轉的使用者介面容易曾經為從右至左的語言提供完整支援。 其中包括下列項目：

- 標準[UIKit](xref:UIKit)控制項將會自動翻轉從右到左取決於 iOS 裝置的地區設定和語言設定。
- [UIView](xref:UIKit.UIView)類別提供可讓您定義指定的檢視應該會出現時的屬性翻轉由右至左。
- 若要使用以程式設計方式翻轉影像的能力[FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection)屬性[UIImage](xref:UIKit.UIImage)類別。

如需詳細資訊，請參閱 Apple[支援從右至左語言](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17)文件。



## <a name="additional-framework-changes"></a>其他的架構變更

除了我們以上所述的重大變更，Apple 已修改及數個現有的架構，適用於 iOS 9 包括下列增強功能：

- AV Foundation 架構
- AVKit Framework
- CloudKit Framework
- Foundation 架構
- 遞移式架構
- HealthKit Framework
- HomeKit 架構
- 本機驗證架構
- MapKit Framework
- PassKit 架構
- Safari 服務架構
- UIKit 架構

如需詳細資訊，請參閱我們[其他 iOS 9 架構變更](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)文件。

## <a name="deprecated-apis-and-functions"></a>已被取代的 Api 和函式

Apple 已取代的下列 Api 和 iOS 9 中的函式：

- **處理活頁簿和地址通訊錄 UI** -連絡人以及連絡人的 UI 架構已取代這些 Api。 如需詳細資訊，請參閱我們[連絡人以及連絡人 UI](~/ios/platform/contacts.md)文件。
- **CBCentralManager** -`RetrievePeripherals`並`RetrieveConnectedPeripherals`方法`CBCentralManager`類別中已移除 iOS 9 中。 呼叫這些方法會導致應用程式損毀配對附屬應用程式時，或在應用程式啟動。
- **FetchAllChanges** -`FetchAllChanges`的`CKFetchRecordChangesOperation`類別已停用，並將 iOS 9 中移除。
- **Media Player** -iOS 9 中，Media Player framework 已被取代。 改用 AVKit 或 AV Foundation Api。

如需的特定 API 棄用功能的完整清單，請參閱 Apple [iOS 9.0 以上版本 API 差異](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222)文件。

## <a name="ios-9-sample-apps"></a>iOS 9 的範例應用程式

我們有一些[iOS 9 的特定範例](https://developer.xamarin.com/samples/ios/iOS9/)開始著手：

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://developer.xamarin.com/samples/monotouch/ios9/MetalPerformanceShadersHelloWorld/)
- [MusicMotion](https://developer.xamarin.com/samples/monotouch/ios9/MusicMotion/)
- [PhotoProgress](https://developer.xamarin.com/samples/monotouch/ios9/PhotoProgress/)
- [SegueCatalog](https://developer.xamarin.com/samples/monotouch/ios9/SegueCatalog/)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

也請參閱這些範例 （附屬 Mac OS X 版本推出 ！） 的 iOS 部分：

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)



## <a name="related-links"></a>相關連結

- [iOS 9 範例](https://developer.xamarin.com/samples/ios/iOS9/)
- [3D 觸控簡介](~/ios/platform/3d-touch.md)
- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
- [適用於 iPad 的多工](~/ios/platform/multitasking.md)
- [連絡人和連絡人 UI](~/ios/platform/contacts.md)
- [新的搜尋 Api](~/ios/platform/search/index.md)
- [堆疊檢視簡介](~/ios/user-interface/controls/uistackview.md)
- [集合檢視變更](~/ios/user-interface/controls/uicollectionview.md)
- [遊戲的增強功能](~/ios/platform/gaming/index.md)
- [HomeKit 簡介](~/ios/platform/homekit.md)
- [遞移式簡介](~/ios/platform/handoff.md)
- [其他 iOS 9 架構變更](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [疑難排解](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [更新您的 Xamarin.iOS 應用程式到 iOS9 （影片）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
