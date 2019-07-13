---
title: tvOS 9 簡介
description: 這篇文章會介紹的所有新的及修改 Api 和 tvOS 9 中可用的功能適用於 Xamarin.tvOS 開發人員。
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: c4aea5a35fd5db46c9e7ca245b852c988e82f53e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830499"
---
# <a name="introduction-to-tvos-9"></a>tvOS 9 簡介

_這篇文章會介紹的所有新的及修改 Api 和 tvOS 9 中可用的功能適用於 Xamarin.tvOS 開發人員。_

Apple 已發行的 Apple TV 硬體具備重新設計，觸控功能的遠端，執行新 tvOS 的作業系統 （根據 iOS 9） 第 4 個層代。

第一次，tvOS 開啟開發人員，可讓您建立豐富的沉浸式應用程式，並透過 Apple TV 內建 App Store 中撰寫和發行使用 iTunes 應用程式的 iOS 應用程式的體驗類似的程序中將它們發行至 Apple TV 平台存放區。

如果您已熟悉使用 Xamarin.iOS 開發，您應該會發現 tvOS 轉為相當簡單。 大部分的 Api 和功能都相同，不過，許多常見的 Api （例如，WebKit) 無法使用。 此外，使用與 Siri 遠端會造成不存在於基礎的觸控式螢幕的 iOS 裝置的一些設計挑戰。

本指南將 Xamarin.tvOS 開發人員提供的所有新的及修改 Api 和 tvOS 9 中可用功能的簡介。 如需有關 tvOS 的詳細資訊，請參閱 Apple[開發的新 Apple TV](https://developer.apple.com/tvos/)文件。

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>支援和不支援的功能

Apple TV 上執行的 tvOS 應用程式有支援下列功能和特性：

- 應用程式群組
- 背景模式
- 資料保護
- Game Center
- 遊戲控制器
- iCloud
- 在應用程式內購買
- Keychain 共用

不支援下列特性與功能：

- Apple Pay
- 應用程式沙箱
- 相關聯的網域
- HealthKit
- HomeKit
- Inter-App 音訊
- 地圖
- 個人 VPN
- 推播通知
- 電子錢包
- 無線配件組態

請參閱我們[支援的組件](~/ios/tvos/internals/assemblies.md)並[支援的架構](~/ios/tvos/internals/frameworks.md)文件的詳細資訊。

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Apple TV 硬體

新增 Apple TV 有下列的硬體規格：

- 64 位元 A8 處理器
- 32 GB 或 64 GB 的儲存體
- 2 GB 的 RAM
- 10/100Mbps Ethernet
- WiFi 802.11a/b/g/n/ac
- 1080p 解析度
- HDMI
- USB C 的連接埠 （適用於開發人員和診斷使用）
- 新 Siri 遠端或 Apple TV 遠端 （根據區域）

### <a name="siri-remote"></a>Siri 遠端

區域為基礎，提供 Apple TV 遠端將有可能是一個組態：Siri 遠端或 Apple TV 遠端。

Siri 遠端會在下列國家/地區目前上市：

- 澳洲
- Canada
- 法國
- 德國
- 日本
- 西班牙
- United Kingdom
- 美國

所有其他國家/地區將會收到 Apple TV 遠端，就會出現預設搜尋畫面，以供搜尋的文字輸入 [搜尋] 按鈕以取代 Siri 按鈕：

[![](tvos9-images/remote02.png "Siri 遠端")](tvos9-images/remote02.png#lightbox)

如需詳細資訊，請參閱我們[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Apple TV 佈建

如同開發適用於 iOS、 tvOS 需要適當的佈建設定檔進行開發和散發的小組成員資格 」 和 「 簽署身分識別，您已建立與 Apple。

正確佈建的是，也需要存取 tvOS 功能，例如 iCloud KVS 或 CloudKit 資料存放區。 請參閱我們[資源和資料存放區](~/ios/tvos/app-fundamentals/resources-data-storage.md)支援 iCloud Xamarin.tvOS 應用程式中的資訊。

建立佈建設定檔，並安裝與使用 Xamarin.iOS 應用程式相同的方式。 因此，請參閱我們的 iOS[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)文件，如需詳細資訊。

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Apple TV 應用程式

新增 Apple TV 硬體和 tvOS 9 支援的應用程式的兩種類型： 傳統和用戶端-伺服器應用程式。

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>傳統應用程式

傳統應用程式從 Apple TV App Store 購買，並在裝置上直接安裝。 這些應用程式可以是遊戲、 公用程式或媒體作為 Xamarin.iOS 應用程式使用相同的架構和技術所開發的應用程式。

Apple TV 應用程式有 200 MB 的大小上限，而且可以下載額外的 2 GB 的使用上隨選資源的內容。 請參閱我們[資源和資料存放區](~/ios/tvos/app-fundamentals/resources-data-storage.md)如需詳細資訊。

請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)熟悉的工具和開發使用 Xamarin.tvOS 的 tvOS 應用程式所需的概念。

<a name="Summary" />

### <a name="client-server-apps"></a>用戶端-伺服器應用程式

除了已安裝的傳統應用程式，Apple TV 可讓您輕鬆地建立網頁用戶端-伺服器媒體串流處理應用程式使用 web 技術 （HTTPS、 XML 和 JavaScript）。 您將設計使用 Apple 的 TVML 標記語言的使用者介面，並使用 JavaScript 來定義使用 TVMLKit 的應用程式的行為。

如需詳細資訊，請參閱 Apple [Apple TV 標記語言參考](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064)， [TVJS Framework 參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076)， [TVMLKit Framework 參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429)，[相關HTTP 即時資料流](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978)並[HLS 撰寫規格 Apple TV](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf)文件。

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>使用者介面的挑戰

不同於 iOS 或 OS X、 Apple TV 沒有觸控式螢幕或滑鼠，讓使用者直接選取並與其互動的應用程式或其內容。 改為在使用者瀏覽應用程式的使用者介面的 新的 Siri 遠端或藍牙遊戲控制器。 如需詳細資訊，請參閱我們[Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。

此外，整體使用者體驗也大幅不同於 iOS 或 Mac 應用程式通常是單一使用者體驗。 使用 Apple TV 中，使用者經驗通常在本質上，其他社交其中許多人可能坐在單一應用程式和彼此互動的沙發上。 若要設計成功的 Apple TV 應用程式體驗 （新的應用程式或移植現有），這些變更必須納入考量。 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>使用焦點和視差影像

如上所述，Xamarin.tvOS 應用程式的使用者將不進行互動的介面直接做為 ios，他們點選裝置的畫面上的映像但間接從使用 Siri 遠端房間對面。 呈現，並處理這種使用者互動，Apple TV 使用焦點基礎模型。 

當焦點變更時，難以察覺的動畫和作用 （例如映像上視差效果） 來清楚識別目前具有焦點的使用者介面項目。

如果使用者在 Siri 遠端進行緩慢、 循環的筆勢，已取得焦點的項目將 sway 即時回應此移動。 Sway 發生時，圖案的整體呈現耀眼會套用至映像進行平台會出現在介面中。 在一段指定的閒置時間之後, 失焦的任何內容變暗，Focused 項目會成長甚至會更長。

如需詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)並[使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)文件。

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>主畫面

Apple TV 主畫面會顯示已安裝，並提供方法來存取使用者喜好設定的所有應用程式：

[![](tvos9-images/home01.png "主畫面")](tvos9-images/home01.png#lightbox)

使用者瀏覽應用程式圖示上使用選取的應用程式，並啟動它的焦點 Siri 遠端使用觸控筆勢的方格。 應用程式圖示，為您的第一個可能發生的潛在的使用者進行的絕佳的印象，並應該傳達您的應用程式用途，且一目了然。

每個應用程式必須提供一個小型和大型版本的應用程式圖示。 小圖示將使用 Apple TV 主畫面上，在安裝應用程式。 應用程式存放區使用大型的版本。 大型的應用程式圖示應該模擬小型圖示版本的外觀與風格。

如需詳細資訊，請參閱我們[使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)文件。

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>高層貨架

如果使用者具有 Xamarin.tvOS 程式置於頂端資料列，在 Apple TV 主畫面上，使用者會選取您的應用程式時，將會顯示大型的最上層影像。 此映像應該反白顯示您的應用程式的功能，或提供其內容的直接連結。

[![](tvos9-images/topshelf01.png "高層貨架")](tvos9-images/topshelf01.png#lightbox)

最上層影像可以提供為單一的靜態`.png`或`.lsr`檔案，或者它可以動態建立在執行階段為單一資料列的可設定焦點的項目。

而不是顯示靜態的最上層影像，它可以包含動態資料列或可設定焦點的項目或一組動態的捲動橫幅。 這兩個這些動態樣式可讓您反白顯示您的應用程式或跳躍到其最常使用的功能所提供的內容。

如需詳細資訊，請參閱我們[使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md)文件和 Apple [TVServices Framework 參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)如需有關將 Top Shelf 延伸模組新增至您的應用程式提供動態 Top Shelf 內容。






## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [建置使用 Xamarin （影片） 的 tvOS 應用程式](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
