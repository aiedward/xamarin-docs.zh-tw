---
title: TvOS 9 簡介
description: 本文介紹的所有新的及修改應用程式開發介面和功能中 tvOS 9 Xamarin.tvOS 開發人員。
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: e2e3843506061cc79ad911404468477bf49dfe56
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-tvos-9"></a>TvOS 9 簡介

_本文介紹的所有新的及修改應用程式開發介面和功能中 tvOS 9 Xamarin.tvOS 開發人員。_


Apple 已發行的第 4 個產生的 Apple TV 硬體，並且包含重新設計，觸控功能的遠端，執行新 tvOS 的作業系統 （根據 iOS 9）。

第一次，tvOS 開啟開發人員，可讓您建立豐富、 沈浸式應用程式，並釋放它們透過 Apple 電視遙控器內建 App Store 中的程序撰寫和發行使用 iTunes 應用程式的 iOS 應用程式的體驗類似 Apple TV 平台存放區。

如果您熟悉 Xamarin.iOS 開發，您應該會發現 tvOS 轉為相當簡單。 大部分的應用程式開發介面和功能都相同，不過，許多常見的 Api （例如，WebKit) 無法使用。 此外，使用與 Siri 遠端會帶來一些設計挑戰不存在於基礎的觸控螢幕的 iOS 裝置。

本指南將 Xamarin.tvOS 開發人員提供的所有新的及修改應用程式開發介面和 tvOS 9 中可用功能的簡介。 如需有關 tvOS 的詳細資訊，請參閱 Apple[開發新的 Apple tv](https://developer.apple.com/tvos/)文件。

<a name="Supported-and-Unsupported-Capabilities" />

## <a name="supported-and-unsupported-capabilities"></a>支援和不支援的功能

tvOS Apple TV 上執行的應用程式有支援下列功能：

 - 應用程式群組
 - 背景模式
 - 資料保護
 - Game Center
 - 遊戲控制器
 - iCloud
 - 在應用程式內購買
 - Keychain 共用

不支援下列功能和功能：

 - Apple Pay
 - 沙箱應用程式
 - 相關聯的網域
 - HealthKit
 - HomeKit
 - Inter-App 音訊
 - 地圖
 - 個人 VPN
 - 推播通知
 - 電子錢包
 - 無線配件組態

請參閱我們[支援組件](~/ios/tvos/internals/assemblies.md)和[支援架構](~/ios/tvos/internals/frameworks.md)文件的詳細資訊。

<a name="Apple-TV-Hardware" />

## <a name="apple-tv-hardware"></a>Apple TV 硬體

新的 Apple TV 有下列硬體規格：

 - 64 位元 A8 處理器
 - 32 gb 的記憶體或 64 GB 的儲存體
 - 2 GB 的 RAM
 - 10/100Mbps 乙太網路
 - WiFi 802.11a/b/g/n/ac
 - 1080p 解析度
 - HDMI
 - USB C 連接埠 （適用於開發人員和僅限診斷使用）
 - 新 Siri 遠端或 Apple TV 遠端 （根據區域）

### <a name="siri-remote"></a>Siri 遠端

區域為基礎，提供 Apple TV 遠端將有可能是一個組態： Siri 遠端或 Apple TV 遠端。

Siri 遠端下列國家 （地區） 是目前可用的：

 - 澳洲
 - Canada
 - 台北市
 - 德國
 - 日本
 - 西班牙
 - 英國
 - 美國

所有其他國家/地區會收到 Apple TV 遠端，就會出現預設搜尋畫面，以用於搜尋的文字輸入 [搜尋] 按鈕以取代 Siri 按鈕：

[![](tvos9-images/remote02.png "Siri 遠端")](tvos9-images/remote02.png#lightbox)

如需詳細資訊，請參閱我們[Siri 遠端和藍芽控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。

<a name="Apple-TV-Provisioning" />

## <a name="apple-tv-provisioning"></a>Apple TV 佈建

就像適用於 iOS 開發，新 tvOS 將進行開發和發佈根據小組的成員資格和簽署識別您已經建立透過 Apple 需要適當的佈建設定檔。

適當的佈建也會需要存取 tvOS 功能，例如 iCloud KVS 或 CloudKit 資料存放區。 請參閱我們[資源和資料存放區](~/ios/tvos/app-fundamentals/resources-data-storage.md)支援 iCloud Xamarin.tvOS 應用程式中的資訊。

佈建設定檔，建立並安裝與使用 Xamarin.iOS 應用程式相同的方式。 在這種情況，請參閱我們的 iOS[裝置佈建](~/ios/get-started/installation/device-provisioning/index.md)文件以取得詳細資料。

<a name="Apple-TV-Apps" />

## <a name="apple-tv-apps"></a>Apple TV 應用程式

新的 Apple TV 硬體和 tvOS 9 支援的應用程式的兩種類型： 傳統和用戶端-伺服器應用程式。

<a name="Traditional-Apps" />

### <a name="traditional-apps"></a>傳統應用程式

傳統應用程式從 Apple TV App Store 購買並安裝在裝置上直接中。 遊戲、 公用程式或以 Xamarin.iOS 應用程式使用相同的架構和技術所開發的媒體應用程式，可使用這些應用程式。

Apple TV 應用程式有 200 MB 的大小上限，並且可以下載內容使用視資源的額外的 2 GB。 請參閱我們[資源和資料存放區](~/ios/tvos/app-fundamentals/resources-data-storage.md)如需詳細資訊。

請參閱我們[Hello，tvOS 快速入門指南](~/ios/tvos/get-started/hello-tvos.md)自己熟悉的工具和開發使用 Xamarin.tvOS tvOS 應用程式所需的概念。

<a name="Summary" />

### <a name="client-server-apps"></a>用戶端-伺服器應用程式

已安裝的傳統應用程式，除了 Apple TV 可輕鬆建立網頁用戶端-伺服器媒體串流處理應用程式使用 web 技術 （HTTPS、 XML 和 JavaScript）。 您將設計使用 Apple 的 TVML 標記語言的使用者介面，並使用 JavaScript 來定義使用 TVMLKit 應用程式的行為。

如需詳細資訊，請參閱 Apple [Apple TV 標記語言參考](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064)， [TVJS Framework 參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076)， [TVMLKit Framework 參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429)，[有關HTTP 即時資料流](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978)和[HLS 撰寫規格 Apple tv](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf)文件。

<a name="User-Interface-Challenges" />

## <a name="user-interface-challenges"></a>使用者介面的挑戰

不同於 iOS 或 OS X，Apple TV 沒有觸控螢幕或滑鼠，讓使用者直接選取並與其互動的應用程式或其內容。 改為在使用者瀏覽應用程式的使用者介面的新 Siri 遠端或藍芽遊戲控制器。 如需詳細資訊，請參閱我們[Siri 遠端和藍芽控制器](~/ios/tvos/platform/remote-bluetooth.md)文件。

此外，整體的使用者經驗也大幅不同於 iOS 或 Mac 應用程式通常是單一使用者的體驗。 Apple TV，與使用者經驗通常更社交的本質，其中許多人可能坐在沙發上與單一的應用程式，而且彼此互動。 若要設計成功 Apple TV 的應用程式體驗 （新的應用程式或將移植現有），這些變更必須納入考量。 

<a name="Summary" />

### <a name="working-with-focus-and-parallax-images"></a>使用焦點和視差影像

如上所述，Xamarin.tvOS 應用程式的使用者將不會互動的介面直接做為使用 iOS 其中他們點選裝置螢幕上的映像但間接從使用 Siri 遙控器的遙控器輕鬆。 若要顯示和處理這種使用者互動，Apple TV 會使用焦點基礎模型。 

當焦點變更時，難以察覺的動畫和作用 （例如，在影像上視差效果） 可用來清楚識別目前具有焦點的使用者介面項目。

如果使用者會變慢，循環的筆勢 Siri 遙控器上，已取得焦點的項目將 sway 即時回應此移動。 Sway 過程圖案的光澤會套用到它的映像進行平台會出現在介面中。 指定一段閒置時間之後, 任何失焦內容變暗，集中項目會成長甚至更大。

如需詳細資訊，請參閱我們[使用導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md)和[使用圖示和映像](~/ios/tvos/app-fundamentals/icons-images.md)文件。

<a name="The-Home-Screen" />

### <a name="the-home-screen"></a>主畫面

Apple TV 主畫面會顯示所有的應用程式會安裝並提供存取使用者喜好設定的方法：

[![](tvos9-images/home01.png "主畫面")](tvos9-images/home01.png#lightbox)

使用者瀏覽應用程式圖示上以選取應用程式，然後啟動它使用焦點 Siri 遠端使用觸控筆勢的方格。 應用程式圖示，則您的第一個可能發生的潛在使用者進行絕佳的印象就和應該要能傳達一眼您的應用程式用途。

每個應用程式必須提供一個小型和大型應用程式圖示的版本。 安裝應用程式時，將首頁 Apple TV 螢幕上使用小圖示。 大型版本會使用應用程式市集。 大型應用程式圖示應該模擬小型圖示版本的外觀與風格。

如需詳細資訊，請參閱我們[使用圖示和映像](~/ios/tvos/app-fundamentals/icons-images.md)文件。

<a name="The-Top-Shelf" />

### <a name="the-top-shelf"></a>高層貨架

如果使用者具有 Xamarin.tvOS 應用程式置於頂端列，在 Apple TV 主畫面上，當使用者選取您的應用程式時將顯示大型的上架映像。 此映像應該反白顯示您的應用程式的功能，或提供其內容的直接連結。

[![](tvos9-images/topshelf01.png "高層貨架")](tvos9-images/topshelf01.png#lightbox)

可以為單一靜態提供上架映像`.png`或`.lsr`檔案，或者它可以動態建立在執行階段為單一資料列的可設定焦點的項目。

而不是顯示靜態的上架映像，它可以包含動態資料列或可設定焦點的項目或一組動態的捲動橫幅。 這兩個這些動態樣式可讓您反白顯示您的應用程式或跳躍到其最常使用的功能所提供的內容。

如需詳細資訊，請參閱我們[使用圖示和映像](~/ios/tvos/app-fundamentals/icons-images.md)文件集和 Apple [TVServices Framework 參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412)如需有關加入您的應用程式提供上架延伸模組動態的上架內容。






## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [建置 tvOS （影片） 的 xamarin 應用程式](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)
