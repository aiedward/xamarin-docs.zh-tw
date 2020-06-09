---
title: tvOS 9 簡介
description: 本文介紹 tvOS 9 for Xamarin. tvOS 開發人員所提供的所有新的和修改過的 Api 和功能。
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 99565d7e61e8e8c1d28b67a6a6d25979c79fa387
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84566158"
---
# <a name="introduction-to-tvos-9"></a>tvOS 9 簡介

_本文介紹 tvOS 9 for Xamarin. tvOS 開發人員所提供的所有新的和修改過的 Api 和功能。_

Apple 已發行第4代的 Apple TV 硬體，其中包含重新設計的觸控式遠端，並執行新的 tvOS 作業系統（以 iOS 9 為基礎）。

第一次，tvOS 會為開發人員開啟 Apple TV 平臺，讓您可以建立豐富的沉浸式應用程式，並透過 Apple TV 內建的 App Store 來發行它們，這類似于使用 iTunes App Store 撰寫和發行 iOS 應用程式的體驗。

如果您熟悉 Xamarin iOS 開發，您應該會發現轉換到 tvOS 相當簡單。 大部分的 Api 和功能都相同，不過，許多常見的 Api 無法使用（例如 WebKit）。 此外，使用 Siri 遠端時，會造成在以觸控式 iOS 裝置中不存在的一些設計挑戰。

本指南將介紹 tvOS 9 for Xamarin. tvOS 開發人員所提供的所有新的和已修改的 Api 和功能。 如需 tvOS 的詳細資訊，請參閱 Apple[針對新的 APPLE TV 開發](https://developer.apple.com/tvos/)檔。

<a name="Supported-and-Unsupported-Capabilities"></a>

## <a name="supported-and-unsupported-capabilities"></a>支援和不支援的功能

在 Apple 電視上執行的 tvOS 應用程式具有下列支援的功能和功能：

- 應用程式群組
- 背景模式
- 資料保護
- Game Center
- 遊戲控制器
- iCloud
- 應用程式內購買
- Keychain 共用

不支援下列特性和功能：

- Apple Pay
- 應用程式沙箱
- 相關聯的網域
- HealthKit
- HomeKit
- Inter-App 音訊
- 地圖服務
- 個人 VPN
- 推播通知
- 電子錢包
- 無線配件組態

如需詳細資訊，請參閱[支援的元件](~/ios/tvos/internals/assemblies.md)和[支援](~/ios/tvos/internals/frameworks.md)的架構檔。

<a name="Apple-TV-Hardware"></a>

## <a name="apple-tv-hardware"></a>Apple TV 硬體

新的 Apple TV 具有下列硬體規格：

- 64位 A8 處理器
- 存放裝置的32GB 或64GB
- 2GB 的 RAM
- 10/100Mbps 乙太網路
- WiFi 802.11 a/b/g/n/ac
- 1080p 解析
- HDMI
- USB C 埠（僅適用于開發人員和診斷用途）
- 新的 Siri 遠端或 Apple TV 遠端（根據地區）

### <a name="siri-remote"></a>Siri 遠端

根據區域，提供的 Apple TV 遠端會有下列其中一項設定： Siri 遠端或 Apple TV 遠端。

Siri 遠端目前適用于下列國家/地區：

- 澳洲
- 加拿大
- 法國
- 德國
- 日本
- 西班牙
- 英國
- 美國

所有其他國家/地區都會收到 Apple TV 遙控器，其會將 [Siri] 按鈕取代為 [搜尋] 按鈕，以顯示包含搜尋文字輸入的預設搜尋畫面：

[![](tvos9-images/remote02.png "Siri Remote")](tvos9-images/remote02.png#lightbox)

如需詳細資訊，請參閱我們的[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)檔。

<a name="Apple-TV-Provisioning"></a>

## <a name="apple-tv-provisioning"></a>Apple 電視布建

就像針對 iOS 進行開發一樣，新的 tvOS 將需要適當的布建設定檔以進行開發和散發，這是以您已經與 Apple 建立的小組成員資格和簽署身分識別為基礎。

若要存取 tvOS 功能（例如 iCloud KVS 或 CloudKit 資料存放區），則也需要適當的布建。 如需在您的 tvOS apps 中支援 iCloud 的相關資訊，請參閱我們的[資源和資料存放區](~/ios/tvos/app-fundamentals/resources-data-storage.md)。

建立和安裝布建設定檔的方式與使用 Xamarin iOS 應用程式相同。 如需詳細資訊，請參閱 iOS[裝置](~/ios/get-started/installation/device-provisioning/index.md)布建檔。

<a name="Apple-TV-Apps"></a>

## <a name="apple-tv-apps"></a>Apple 電視應用程式

新的 Apple TV 硬體和 tvOS 9 支援兩種類型的應用程式：傳統和用戶端伺服器應用程式。

<a name="Traditional-Apps"></a>

### <a name="traditional-apps"></a>傳統應用程式

傳統應用程式會從 Apple 電視 App Store 購買，並直接安裝在裝置上。 這些應用程式可以是使用與 Xamarin iOS 應用程式相同的架構和技術所開發的遊戲、公用程式或媒體應用程式。

Apple TV 應用程式的大小上限為 200 MB，且可以使用隨選資源下載額外的2GB 內容。 如需詳細資訊，請參閱我們的[資源和資料存放區](~/ios/tvos/app-fundamentals/resources-data-storage.md)。

請參閱我們[的 Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md)，讓您熟悉使用 tvOS 開發 tvOS 應用程式所需的工具和概念。

<a name="Summary"></a>

### <a name="client-server-apps"></a>用戶端-伺服器應用程式

除了已安裝的傳統應用程式之外，Apple TV 還可讓您輕鬆地使用 web 技術（HTTPS、XML 和 JavaScript）建立 web 型的用戶端伺服器媒體串流應用程式。 您將使用 Apple 的 TVML 標記語言設計使用者介面，並使用 JavaScript 來定義使用 TVMLKit 的應用程式行為。

如需詳細資訊，請參閱 Apple 的[APPLE Tv 標記語言參考](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064)、 [TVJS framework 參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076)、 [TVMLKit 架構參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429)，關於 Apple TV 檔的[HTTP 即時串流](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978)和[HLS 撰寫規格](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf)。

<a name="User-Interface-Challenges"></a>

## <a name="user-interface-challenges"></a>使用者介面挑戰

與 iOS 或 OS X 不同的是，Apple TV 沒有觸控式螢幕或滑鼠，可讓使用者直接選取應用程式或其內容並與其互動。 相反地，他們會使用者新的 Siri 遠端或 Bluetooth 遊戲控制器來流覽應用程式的使用者介面。 如需詳細資訊，請參閱我們的[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)檔。

此外，整體使用者經驗與 iOS 或 Mac 應用程式的差異，通常是單一使用者體驗。 使用 Apple 電視，使用者體驗傾向于本質上的社交，而許多人可能會坐在沙發上，並與單一應用程式互動。 若要設計成功的 Apple TV 應用程式體驗（新的應用程式或移植現有的 app），必須將這些變更納入考慮。 

<a name="Summary"></a>

### <a name="working-with-focus-and-parallax-images"></a>使用焦點和視差影像

如上所述，tvOS 應用程式的使用者不會與它直接與 iOS 互動，因為它會在裝置的螢幕上使用影像，而不是在房間內使用 Siri 遠端進行。 為了呈現及處理這種使用者互動，Apple TV 使用以焦點為基礎的模型。 

隨著焦點的變更，會使用細微的動畫和效果（例如影像上的視差效果）來清楚識別目前具有焦點的使用者介面專案。

如果使用者在 Siri 遙控器上進行緩慢的迴圈手勢，則焦點專案會即時回應這項移動。 當 sway 發生時，會將發光的 sheen 套用至其影像，讓表面看起來像是發光。 在指定的非使用狀態之後，任何不專注的內容都會變暗，而焦點的專案也會更大。

如需詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)和[使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)檔。

<a name="The-Home-Screen"></a>

### <a name="the-home-screen"></a>主畫面

[Apple TV 首頁] 畫面會顯示已安裝的所有應用程式，並提供存取使用者喜好設定的方式：

[![](tvos9-images/home01.png "The Home Screen")](tvos9-images/home01.png#lightbox)

使用者使用焦點在 Siri 遠端流覽應用程式圖示的方格，以選取並啟動應用程式。 應用程式圖示是您對潛在使用者有絕佳印象的第一次機會，而且應該一目了然地傳達應用程式的用途。

每個應用程式都必須同時提供其應用程式圖示的小型和大型版本。 在安裝應用程式時，Apple TV 首頁畫面上會使用小型圖示。 應用程式存放區使用的是大型版本。 大型應用程式圖示應該模擬小圖示版本的外觀與風格。

如需詳細資訊，請參閱我們[的使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)檔。

<a name="The-Top-Shelf"></a>

### <a name="the-top-shelf"></a>頂尖貨位

如果使用者已將您的 tvOS 應用程式放在 Apple 電視首頁螢幕上的頂端列，當使用者選取您的應用程式時，將會顯示較大的頂尖影像。 此影像應會反白顯示您應用程式的功能，或提供其內容的直接連結。

[![](tvos9-images/topshelf01.png "The Top Shelf")](tvos9-images/topshelf01.png#lightbox)

最上層的貨位映射可以當做單一靜態或檔案提供 `.png` ， `.lsr` 也可以在執行時間動態建立，做為可設定焦點專案的單一資料列。

它可以包含動態資料列或可設定焦點的專案，或動態的一組滾動橫幅，而不是顯示靜態的最大貨位影像。 這兩種動態樣式都可讓您醒目提示應用程式所提供的內容，或跳到其最常使用的功能。

如需詳細資訊，請參閱我們[的使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)檔和 Apple 的[TVServices 架構參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)，以取得將最上層延伸模組新增至您的應用程式以提供動態主要貨位內容的詳細資訊。

## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [使用 Xamarin 建立適用于 tvOS 的應用程式（影片）](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
