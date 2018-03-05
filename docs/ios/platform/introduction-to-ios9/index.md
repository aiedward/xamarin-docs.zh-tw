---
title: "IOS 9 的簡介"
description: "本文介紹的所有新的及修改應用程式開發介面和適用於 iOS 9 功能 Xamarin.iOS 開發人員。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 5b26989603695cfb309fba5a5318f7ef4d2460e2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-ios-9"></a>IOS 9 的簡介

_本文介紹的所有新的及修改應用程式開發介面和適用於 iOS 9 功能 Xamarin.iOS 開發人員。_

![](images/ios9-sml.png "IOS 9 標誌")

Apple 已新增數個新的 Api 和服務，在 iOS 9 及許多現有功能的增強功能。

## <a name="3d-touch"></a>3D Touch

IOS 9 iPhone 6s 和 iPhone 6s 的新手再加上，3D Touch 將不足的壓力機密筆勢加入至您的 iOS 應用程式。 使用 3D Touch iPhone 應用程式現在就可以不只在通知使用者觸控裝置的螢幕，同時，也有意義的使用者 exerting 多少壓力並回應不同的壓力層級。

3D Touch 提供您的應用程式的下列功能：

- **壓力敏感度**-應用程式現在可以手動測量或 light 使用者接觸該資訊的螢幕，並採取優點。 例如，繪製應用程式可以進行線條較粗或 thinner 根據使用者已手動觸控螢幕。
- **查看和 Pop** -您的應用程式現在可讓使用者與資料互動，而不必巡覽超出其目前的內容。 可以按在螢幕上的硬碟，*查看*他們感興趣 （例如預覽訊息） 的項目。 按下更困難，他們可以*Pop*到項目。
- **快速動作**-認為的快速之類的動作，不可以是快顯總當使用者以滑鼠右鍵按一下桌面應用程式中的項目內容功能表。 使用快速控制項目，您可以加入常見快速且輕鬆函式的存取捷徑在您的應用程式從 iOS 裝置上的 [首頁] 畫面圖示。

若要進一步了解，請參閱我們[簡介 3D Touch](~/ios/platform/3d-touch.md)指南。

## <a name="app-transport-security"></a>應用程式的傳輸安全性

新增 ios 9，應用程式傳輸安全性 (ATS) 會強制執行 （例如應用程式的後端伺服器） 的網際網路資源與您的應用程式之間的安全連線。 AT 可確保所有的網際網路通訊符合要保障連線安全的最佳作法，因此可以防止意外洩漏機密資訊直接透過您的應用程式或它正在使用的程式庫。

因為在建置適用於 iOS 9 和 OS X 10.11 (El Capitan) 使用的所有連接的應用程式預設會啟用 AT [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/)， [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/)或[NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/)會受限於AT 安全性需求。 如果您的連線不符合這些需求，它們將會失敗並發生例外狀況。

若要了解有關 AT 的詳細資訊，請參閱我們[應用程式的傳輸安全性](~/ios/app-fundamentals/ats.md)指南。

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>適用於 iPad 的多工

IOS 9，與 Apple 新增了多工作業的支援在特定的 iPad 硬體上同時執行兩個應用程式。 如此一來，Xamarin.iOS 應用程式不會再假設它們是只有在任何給定時間執行的應用程式，或他們擁有存取權的全螢幕或裝置的資源。

適用於 iPad 的多工可支援透過下列功能：

- **投影片上**-可讓使用者暫時將投影片出面板 （無論是根據語言方向螢幕的右側或左側一邊），包括大約 25%的主要應用程式目前正在執行中執行的第二個 iOS 應用程式。 投影片上只部。 iPad Pro、 iPad 空中、 iPad 空中 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4
- **分割檢視**-支援的 iPad 的硬體上 (iPad 空中 2，iPad 迷你 4 和 iPad Pro 只)，使用者可以選取第二個應用程式，並與目前執行的應用程式分割螢幕模式中執行的並行。 使用者可以控制主畫面的每個應用程式所佔的百分比。
- **圖片中的圖片**-播放視訊內容，現在視訊可以播放的可移動且可調整大小視窗中，漂浮在目前在 iOS 裝置上執行的應用程式的應用程式。 使用者擁有的大小和位置，此視窗的完整控制權。 IPad Pro、 iPad 空中、 iPad 空中 2、 iPad 迷你 2、 iPad 迷你 3 或 iPad 迷你 4 上的只有時圖片中的圖片。

若要了解有關新的多工能力的 iOS 9 的詳細資訊，請參閱我們[適用於 iPad 的多工](~/ios/platform/multitasking.md)指南。

## <a name="new-contacts-and-contacts-ui-frameworks"></a>新的連絡人和連絡人的 UI 架構

IOS 9 的簡介，與 Apple 已釋放這兩個新的架構，[連絡人](https://developer.xamarin.com/api/namespace/Contacts/)和[ContactsUI](https://developer.xamarin.com/api/namespace/ContactsUI/)，取代現有的通訊錄，地址通訊錄 UI 架構使用 iOS 8 以及之前。

這些新的物件導向架構提供下列內容：

- **連絡人**– 提供 Xamarin.iOS 存取使用者的連絡資訊。 因為大部分的應用程式只需要唯讀存取權，此架構已經過最佳化的執行緒安全的唯讀存取。
- **ContactsUI** – 提供 Xamarin.iOS UI 項目，若要顯示，編輯、 選取和 iOS 裝置上建立的連絡人。

如需詳細資訊，請參閱我們[連絡人以及連絡人 UI](~/ios/platform/contacts.md)文件。


## <a name="new-search-apis"></a>新的搜尋應用程式開發介面

搜尋已展開於 iOS 9 提供絕佳的新方法，以存取 Xamarin.iOS 應用程式內的資訊。 使用新的搜尋應用程式開發介面，您可以讓您的應用程式搜尋內容透過精選和 Safari 搜尋結果中，遞交 Siri 提醒和建議。 這可讓使用者快速存取活動和深層應用程式中的資訊。

此外，新的搜尋應用程式開發介面讓您更輕鬆地整合應用程式不在前一個搜尋實作體驗中的搜尋。 因為這個緣故，Apple 的宣告，通常需要花費幾小時的時間讓通用搜尋使用應用程式搜尋 iOS 9 應用程式的內容。

如需詳細資訊，請參閱我們[搜尋的增強功能](~/ios/platform/search/index.md)文件。

## <a name="new-stack-view"></a>新的堆疊檢視

堆疊檢視控制項 ([UIStackView](https://developer.xamarin.com/api/type/UIKit.UIStackView/)) 會利用動態回應 iOS 裝置的方向和螢幕大小自動配置和大小類別來管理一堆 subviews （水平或垂直） 的電源。

使用堆疊檢視控制項的工作量所需的配置可大幅降低使用者介面。 所有 subviews 附加至堆疊檢視的配置會自動依據開發人員定義的屬性，例如軸、 發佈、 對齊和間距所管理。

如需詳細資訊，請參閱我們[堆疊檢視簡介](~/ios/user-interface/controls/uistackview.md)文件。


## <a name="collection-view-changes"></a>集合檢視變更

在 iOS 9、 集合檢視中 ([UICollectionView](https://developer.xamarin.com/api/type/UIKit.UICollectionView/)) 現在支援拖放重新排列現成的項目加入新的預設筆勢辨識器和數個新的支援方法。

使用這些新方法，您可以輕鬆地實作拖放到要重新排列集合檢視中，而且可以選擇的自訂項目外觀，期間重新排列的程序的任何階段。

若要了解有關 iOS 9 的集合檢視變更的詳細資訊，請參閱我們[集合檢視變更](~/ios/user-interface/controls/uicollectionview.md)指南。

## <a name="game-enhancements"></a>遊戲的增強功能

IOS 9，Apple 已進行幾項技術的改良遊戲 api 可讓您更輕鬆地實作 Xamarin.iOS 應用程式中的遊戲圖形與音訊。 這些包括這兩個輕鬆開發透過高層級架構和另外的 iOS 裝置的 GPU，改善的速度和圖形的能力，具有低層級的增強功能。

這包括 GameplayKit、 ReplayKit、 模型 I/O、 MetalKit 和金屬效能著色器連同金屬、 SceneKit 和 SpriteKit 新的增強功能。

如需詳細資訊，請參閱我們[遊戲增強功能](~/ios/platform/gaming/index.md)文件。

## <a name="homekit-framework-changes"></a>HomeKit 架構變更

[HomeKit](https://developer.xamarin.com/api/namespace/HomeKit/) iOS 8 中引入的 framework 可讓您設定和控制各種 HomeKit 啟用附屬應用程式 （例如自動化的燈、 門鎖和機庫門 openers） 從 Xamarin.iOS 應用程式。 除了您輕鬆地安裝和設定，可以透過 Siri 說的命令控制 HomeKit 附屬應用程式。

在 iOS 9，Apple 可以更方便進行安裝，展開 附屬應用程式支援，並提供更多的配件互動 （例如控制從遠端透過 iCloud 附屬應用程式） 的類型。

如需詳細資訊，請參閱我們[簡介 HomeKit](~/ios/platform/homekit.md)， [HomeKitIntro iOS 範例應用程式](https://developer.xamarin.com/samples/monotouch/HomeKit/HomeKitIntro/)與 Apple [HomeKit](https://developer.apple.com/homekit/)文件。

## <a name="handoff-framework-changes"></a>遞交架構變更

遞交 （也稱為持續性） 是由引入，Apple 在 iOS 8 和 OS X Yosemite (10.10)，讓使用者針對他們的裝置 （iOS 或 Mac） 的其中一個開始活動，並繼續在其裝置 （如使用者 iClou 所識別的另一台相同的活動d 帳戶)。

遞交已展開在 iOS 9，也支援新的、 增強的搜尋功能。 如需詳細資訊，請參閱我們[搜尋的增強功能](~/ios/platform/search/index.md)文件。 如需有關使用遞移式的詳細資訊，請參閱我們[簡介遞交](~/ios/platform/handoff.md)文件。

## <a name="new-extension-points"></a>新的擴充點

在 iOS 8，Apple 導入了擴充功能，會由作業系統在標準的內容中，例如在通知中心內，當使用者要求是鍵盤，或當他們在編輯相片的程式庫。

IOS 9，與 Apple 藉由提供數個新擴充延伸模組支援_擴充點_，定義使用量原則，並提供 Api 來處理給定區域內，如下所示：

- **新的音訊單元擴充點**– 使用此擴充點提供音訊效果、 樂器、 音效的產生器 」 等其他音訊單元主機應用程式 （例如 GarageBand) 內使用。 這個擴充點也可讓您銷售_音訊單位_（音訊外掛程式） App Store 上的。
- **新的索引維護擴充點**— 使用此擴充點來支援 重新索引的應用程式資料，而不需要應用程式重新啟動。
- **新的網路擴充點**（這些會需要從 Apple 的特殊權限）：
    - **應用程式 Proxy 提供者擴充**— 使用此擴充點來實作自訂透明的用戶端網路 proxy。
    - **篩選資料提供者 / 篩選器控制項的提供者延伸模組**-若要實作動態網路內容篩選裝置上使用這些擴充點。
    - **封包通道提供者延伸模組**— 若要實作自訂的 VPN 通道通訊協定的用戶端使用此擴充點。
- **新的 Safari 擴充點**:
    - **內容封鎖的副檔名**— 使用此擴充點來定義封鎖的內容將不會顯示當使用者瀏覽網站的清單。
    - **共用連結延伸**— 使用此擴充點，以檢視您的應用程式的內容會 Safari 的共用連結。

如需詳細資訊，請參閱我們[延伸模組簡介](~/ios/platform/extensions.md)與 Apple[應用程式擴充功能的程式設計指南](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214)文件。

## <a name="keychain-enhancements"></a>Keychain 的增強功能

在 iOS 9，Apple 已增強 Keychain 提供新的加密金鑰的類型安全的 Enclave 和更多的項目保護選項，如下所示：

- 新的 Touch ID 條件約束，修改指紋資料庫時，使金鑰鏈的項目。
- 新的條件約束，允許只建立存取控制清單項目使用 Touch ID 或密碼。
- 新的驗證內容，可讓您叫用驗證分開`SecItem`呼叫。
- 存取控制清單 （使用應用程式密碼選項） 的熵進行應用程式提供的金鑰鏈項目加密。
- 支援產生及使用安全 enclave 內部索引鍵 (透過`kSecAttrTokenIDSecureEnclave`屬性)。

如需詳細資訊，請參閱我們[簡介 Touch ID](~/ios/platform/touchid.md)文件。


## <a name="right-to-left-language-support"></a>由右至左語言支援

在 iOS 9，Apple 已呈現翻轉的使用者介面比簡單曾由提供由右至左語言的完整支援。 其中包括下列項目：

- 標準[UIKit](https://developer.xamarin.com/api/namespace/UIKit/)控制項將會自動翻轉從右至左取決於 iOS 裝置的地區設定和語言設定。
- [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/)類別會提供可讓您定義指定的檢視應該會出現時屬性翻轉由右至左。
- 能夠以程式設計方式使用翻轉影像[FlipsForRightToLeftLayoutDirection](https://developer.xamarin.com/api/property/UIKit.UIImage.FlipsForRightToLeftLayoutDirection/)屬性[UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/)類別。

如需詳細資訊，請參閱 Apple[支援從右至左語言](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17)文件。



## <a name="additional-framework-changes"></a>其他架構變更

我們已經討論過上方的主要變更，除了 Apple 已進行修改，以及數個現有的架構，適用於 iOS 9 包括下列增強功能：

- AV Foundation 架構
- AVKit Framework
- CloudKit 架構
- Mfc 架構
- Handoff Framework
- HealthKit 架構
- HomeKit 架構
- 本機驗證架構
- MapKit 架構
- PassKit 架構
- Safari Services Framework
- UIKit 架構

如需詳細資訊，請參閱我們[其他 iOS 9 架構變更](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)文件。

## <a name="deprecated-apis-and-functions"></a>已被取代的 Api 和函式

Apple 已取代下列應用程式開發介面和 iOS 9 中的函式：

- **解決活頁簿 （& s） 地址通訊錄 UI** -連絡人以及連絡人的 UI 架構已取代這些應用程式開發介面。 如需詳細資訊，請參閱我們[連絡人以及連絡人 UI](~/ios/platform/contacts.md)文件。
- **CBCentralManager** -`RetrievePeripherals`和`RetrieveConnectedPeripherals`方法`CBCentralManager`iOS 9 中已移除類別。 呼叫這些方法會導致應用程式當機配對附屬應用程式時，或在應用程式啟動。
- **FetchAllChanges** -`FetchAllChanges`的`CKFetchRecordChangesOperation`類別已停用，並將 iOS 9 中移除。
- **Media Player** -iOS 9 中，Media Player framework 已被取代。 改用 AVKit 或 AV Foundation 應用程式開發介面。

特定應用程式開發介面 deprecations 需完整清單，請參閱 Apple [iOS 9.0 以上版本的 API 差異](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222)文件。

## <a name="ios-9-sample-apps"></a>iOS 9 範例應用程式

我們有一些[iOS 9 特定範例](https://developer.xamarin.com/samples/ios/iOS9/)若要開始使用：

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
- [3D Touch 簡介](~/ios/platform/3d-touch.md)
- [應用程式傳輸安全性](~/ios/app-fundamentals/ats.md)
- [適用於 iPad 的多工](~/ios/platform/multitasking.md)
- [連絡人及連絡人 UI](~/ios/platform/contacts.md)
- [新的搜尋應用程式開發介面](~/ios/platform/search/index.md)
- [Introduction to 堆疊檢視](~/ios/user-interface/controls/uistackview.md)
- [集合檢視變更](~/ios/user-interface/controls/uicollectionview.md)
- [遊戲的增強功能](~/ios/platform/gaming/index.md)
- [HomeKit 簡介](~/ios/platform/homekit.md)
- [遞交簡介](~/ios/platform/handoff.md)
- [其他 iOS 9 架構變更](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [疑難排解](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [iOS 9 的開發人員](https://developer.apple.com/ios/pre-release/)
- [新功能 iOS 9.0 以上版本](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Xamarin.iOS 應用程式更新至 iOS9 （影片）](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
