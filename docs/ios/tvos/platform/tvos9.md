---
title: tvOS 9 簡介
description: 本文介紹 tvOS 9 for Xamarin. tvOS 開發人員提供的所有新的和修改過的 Api 和功能。
ms.prod: xamarin
ms.assetid: A7E738E1-9F94-489B-918F-7DF8F0810987
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 552a163636c85b13be64a0613b1523263deb5e92
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437031"
---
# <a name="introduction-to-tvos-9"></a>tvOS 9 簡介

_本文介紹 tvOS 9 for Xamarin. tvOS 開發人員提供的所有新的和修改過的 Api 和功能。_

Apple 已發行第4代的 Apple TV 硬體，該硬體採用重新設計、觸控式遠端、執行新的 tvOS 作業系統 (根據 iOS 9) 。

TvOS 首次為開發人員開啟 Apple TV 平臺，可讓您建立豐富、沉浸式的應用程式，並透過 Apple TV 內建的 App Store 在程式中發行，類似于使用 iTunes App Store 撰寫和發行 iOS 應用程式的體驗。

如果您熟悉 Xamarin 的開發，您應該會發現轉換成 tvOS 相當簡單。 大部分的 Api 和功能都是相同的，但許多常見的 Api 都無法使用 (例如 WebKit) 。 此外，使用 Siri 遠端時，會帶來一些在以觸控式螢幕為基礎的 iOS 裝置上不存在的設計挑戰。

本指南將介紹適用于 Xamarin. tvOS 開發人員的 tvOS 9 中所有新的和修改過的 Api 和功能。 如需有關 tvOS 的詳細資訊，請參閱 Apple [針對新的 APPLE TV](https://developer.apple.com/tvos/) 檔的開發。

<a name="Supported-and-Unsupported-Capabilities"></a>

## <a name="supported-and-unsupported-capabilities"></a>支援和不支援的功能

在 Apple TV 上執行的 tvOS 應用程式具有下列支援的功能與功能：

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
- 地圖
- 個人 VPN
- 推播通知
- 電子錢包
- 無線配件組態

如需詳細資訊，請參閱 [支援的元件](~/ios/tvos/internals/assemblies.md) 和 [支援的 framework](~/ios/tvos/internals/frameworks.md) 檔。

<a name="Apple-TV-Hardware"></a>

## <a name="apple-tv-hardware"></a>Apple TV 硬體

新的 Apple TV 有下列硬體規格：

- 64位 A8 處理器
- 儲存體的32GB 或64GB
- 2GB 的 RAM
- 10/100Mbps 乙太網路
- WiFi 802.11 a/b/g/n/ac
- 1080p 解析
- HDMI
- USB C 埠 (，僅供開發人員和診斷使用) 
- 根據區域) 的新 Siri 遠端或 Apple TV 遠端 (

### <a name="siri-remote"></a>Siri 遠端

根據區域，提供的 Apple TV 遠端會有其中一種設定： Siri 遠端或 Apple TV Remote。

Siri 遠端目前可在下列國家/地區使用：

- 澳洲
- 加拿大
- 法國
- 德國
- 日本
- 西班牙
- 英國
- 美國

所有其他國家（地區）都會收到 Apple TV 遠端，並以搜尋按鈕取代 [Siri] 按鈕，此按鈕會顯示具有文字輸入的預設搜尋畫面來搜尋：

[![Siri 遠端](tvos9-images/remote02.png)](tvos9-images/remote02.png#lightbox)

如需詳細資訊，請參閱我們的 [Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md) 檔。

<a name="Apple-TV-Provisioning"></a>

## <a name="apple-tv-provisioning"></a>Apple TV 布建

就像針對 iOS 進行開發一樣，新的 tvOS 將需要適當的布建設定檔，以根據您已經使用 Apple 建立的團隊成員資格和簽署身分識別，進行開發和散發。

若要存取 tvOS 功能（例如 iCloud KVS 或 CloudKit 資料存放區），也需要適當的布建。 如需在 tvOS 應用程式中支援 iCloud 的詳細資訊，請參閱我們的 [資源和資料儲存體](~/ios/tvos/app-fundamentals/resources-data-storage.md) 。

建立和安裝布建設定檔的方式與使用 Xamarin iOS 應用程式的方式相同。 因此，請參閱我們的 iOS [裝置](~/ios/get-started/installation/device-provisioning/index.md) 布建檔以取得詳細資料。

<a name="Apple-TV-Apps"></a>

## <a name="apple-tv-apps"></a>Apple TV 應用程式

新的 Apple TV 硬體和 tvOS 9 支援兩種類型的應用程式：傳統和用戶端-伺服器應用程式。

<a name="Traditional-Apps"></a>

### <a name="traditional-apps"></a>傳統應用程式

傳統的應用程式是從 Apple TV App Store 購買，並直接安裝在裝置上。 這些應用程式可以是使用與 Xamarin iOS 應用程式相同的架構和技術所開發的遊戲、公用程式或媒體應用程式。

Apple TV 應用程式的大小上限為200MB，而且可以使用隨選資源下載其他2GB 的內容。 如需詳細資訊，請參閱我們的 [資源和資料儲存體](~/ios/tvos/app-fundamentals/resources-data-storage.md) 。

請參閱我們的 [Hello，tvOS 快速入門手冊](~/ios/tvos/get-started/hello-tvos.md) ，以熟悉使用 tvOS 開發 tvOS 應用程式所需的工具和概念。

<a name="Summary"></a>

### <a name="client-server-apps"></a>用戶端-伺服器應用程式

除了已安裝的傳統應用程式，Apple TV 還可讓您輕鬆地使用 web 技術（ (HTTPS、XML 和 JavaScript) ）來建立 web 型用戶端伺服器媒體串流應用程式。 您將使用 Apple 的 TVML 標記語言來設計使用者介面，並使用 JavaScript 來定義使用 TVMLKit 的應用程式行為。

如需詳細資訊，請參閱 apple 的 [APPLE Tv 標記語言參考](https://developer.apple.com/library/prerelease/tvos/documentation/LanguagesUtilities/Conceptual/ATV_Template_Guide/index.html#//apple_ref/doc/uid/TP40015064)、 [TVJS framework 參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLJS/Reference/TVJSFrameworkReference/index.html#//apple_ref/doc/uid/TP40016076)、 [TVMLKit 架構參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVMLKit/Reference/TVMLKit_Collection/index.html#//apple_ref/doc/uid/TP40016429)、 [關於 HTTP 即時串流](https://developer.apple.com/library/prerelease/tvos/referencelibrary/GettingStarted/AboutHTTPLiveStreaming/about/about.html#//apple_ref/doc/uid/TP40013978) 和 [HLS 撰寫規格的 apple tv](https://developer.apple.com/services-account/download?path=/Documentation/HLS_Authoring_Specification_for_Apple_TV/HLS_Authoring_Specification_for_Apple_TV.pdf) 檔。

<a name="User-Interface-Challenges"></a>

## <a name="user-interface-challenges"></a>消費者介面挑戰

與 iOS 或 OS X 不同的是，Apple TV 沒有可讓使用者直接選取應用程式或其內容並與其互動的觸控式螢幕或滑鼠。 相反地，他們會使用新的 Siri 遠端或 Bluetooth 遊戲控制器來流覽應用程式的消費者介面。 如需詳細資訊，請參閱我們的 [Siri 遠端和藍牙控制器](~/ios/tvos/platform/remote-bluetooth.md) 檔。

此外，整體使用者體驗與通常會是單一使用者體驗的 iOS 或 Mac 應用程式截然不同。 使用 Apple TV 時，使用者體驗的本質通常會更廣大，其中有許多人可能坐在沙發與單一應用程式互動。 若要設計成功的 Apple TV 應用程式體驗 (新的應用程式或移植現有的應用程式) ，必須將這些變更納入考慮。 

<a name="Summary"></a>

### <a name="working-with-focus-and-parallax-images"></a>使用焦點和視差影像

如上所述，您的 tvOS 應用程式的使用者將不會像 iOS 一樣直接與其介面互動，因為它們會在裝置的螢幕上點出影像，但會使用 Siri 遠端在房間間間接地進行。 為了呈現和處理這種使用者互動，Apple TV 使用以焦點為基礎的模型。 

當焦點變更時，會使用微妙的動畫和效果 (例如影像) 的視差效果，用來清楚識別目前擁有焦點的消費者介面專案。

如果使用者在 Siri 遠端進行緩慢的迴圈手勢，則焦點的專案會即時回應此移動。 當 sway 發生時，會將有向的的 sheen 套用至其影像，讓表面看起來更加亮。 在指定的非使用狀態之後，任何專注的內容都會變暗，而且焦點的專案會變得更大。

如需詳細資訊，請參閱我們使用 [導覽和焦點](~/ios/tvos/app-fundamentals/navigation-focus.md) 以及使用 [圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md) 檔。

<a name="The-Home-Screen"></a>

### <a name="the-home-screen"></a>主畫面

Apple TV 首頁畫面會顯示已安裝的所有應用程式，並提供存取使用者喜好設定的方法：

[![主畫面](tvos9-images/home01.png)](tvos9-images/home01.png#lightbox)

使用者會使用焦點來選取應用程式並啟動應用程式，以在 Siri 遠端使用觸控手勢來流覽應用程式圖示的方格。 應用程式圖示是您對潛在使用者很棒的第一個機會，而且應該很快就能傳達您的應用程式用途。

每個應用程式都必須提供小型和大型版本的應用程式圖示。 安裝應用程式時，會在 Apple TV 首頁畫面上使用小型圖示。 App Store 使用大型版本。 大型應用程式圖示應模仿小型圖示版本的外觀和風格。

如需詳細資訊，請參閱我們使用 [圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md) 檔。

<a name="The-Top-Shelf"></a>

### <a name="the-top-shelf"></a>頂尖貨架

如果使用者已將您的 tvOS 應用程式放在 Apple TV 首頁畫面上的頂端列，當使用者選取您的應用程式時，將會顯示大型的最高貨架影像。 此影像應醒目提示您應用程式的功能，或提供其內容的直接連結。

[![頂尖貨架](tvos9-images/topshelf01.png)](tvos9-images/topshelf01.png#lightbox)

您可以將最上層的影像提供為單一靜態或檔案， `.png` `.lsr` 也可以在執行時間以動態方式建立為可設定焦點的專案的單一資料列。

它不會顯示靜態的最上層貨架影像，而是可以包含動態資料列或可設定焦點的專案，或動態的一組滾動橫幅。 這兩種動態樣式可讓您反白顯示應用程式所提供的內容，或跳到最常使用的功能。

如需詳細資訊，請參閱我們的 [使用圖示和影像](~/ios/tvos/app-fundamentals/icons-images.md) 檔和 Apple 的 [TVServices 架構參考](https://developer.apple.com/library/prerelease/tvos/documentation/TVServices/Reference/TVServices_Ref/index.html#//apple_ref/doc/uid/TP40016412) ，以取得將最大貨架延伸模組新增至您的應用程式以提供動態頂端內容的詳細資訊。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
- [使用 Xamarin 建立適用于 tvOS 的應用程式 (影片) ](https://university.xamarin.com/lightninglectures/tvos-with-xamarin)