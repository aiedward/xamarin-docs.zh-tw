---
title: 發行應用程式
ms.prod: xamarin
ms.assetid: 51E19000-040A-2B74-C462-EC57C617085C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: b5f09413379de13aaf12e8113e8deb0f3c2bf0c3
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525454"
---
# <a name="publishing-an-application"></a>發行應用程式

建立絕佳的應用程式之後，使用者將會想要使用它。 本節涵蓋透過電子郵件、私人 Web 伺服器、Google Play 或 Amazon App Store for Android 等管道公開散發使用 Xamarin.Android 所建立應用程式的步驟。


## <a name="overview"></a>總覽

Xamarin.Android 應用程式開發的最後一個步驟就是發行應用程式。 發行是編譯 Xamarin.Android 應用程式以將其備妥供使用者安裝在其裝置上的過程，其中涉及兩項必要工作：

- **做好發行準備** &ndash; 這會建立一個可部署至 Android 架構裝置的應用程式發行版本 (如需有關發行準備的詳細資訊，請參閱[準備可供發行的應用程式](~/android/deploy-test/release-prep/index.md))。

- **散發** &ndash; 應用程式的發行版本會透過一或多個各種散發通道提供給使用者。

下表說明發行 Xamarin.Android 應用程式所涉及的步驟：

[![建置和部署流程圖](images/build-and-deploy-steps.png)](images/build-and-deploy-steps.png#lightbox)

如上圖所示，不論使用的散發方式是哪一種，準備工作都相同。 向使用者發行 Android 應用程式的方式有數種：

- **透過網站** &ndash; 可以在網站上提供 Xamarin.Android 應用程式下載，讓使用者從網站上按一下連結來安裝應用程式。
- **透過電子郵件** &ndash; 使用者可以從其電子郵件安裝 Xamarin.Android 應用程式。 使用 Android 架構裝置來開啟附件時，將會安裝應用程式。
- **透過市集** &ndash; 有數個可供散發的市集存在，例如 [Google Play](http://play.google.com/) 或 [Amazon App Store for Android](http://www.amazon.com/mobile-apps/b?ie=UTF8&node=2350149011)。


使用已建立的市集是發行應用程式的最常見方式，因為它既提供最大的市場範圍，也提供最好的散發控制。 不過，透過市集發行應用程式需要額外的工作。

多個通道可同時散發 Xamarin.Android 應用程式。 例如，既可以在 Google Play 上、Amazon App Store for Android 上發行應用程式，也可以從 Web 伺服器下載該應用程式。

另外兩個散發方式 (下載或電子郵件) 最適用於一部分受管制的使用者 (例如企業環境)，或最適用於對象僅限一小組或一組明確指定之使用者的應用程式。
伺服器和電子郵件散發也是較簡單的發行模型，所需的應用程式發行準備工作也較少。

Amazon Mobile App Distribution Program (Amazon 行動應用程式散發計劃) 可讓行動應用程式開發人員在 Amazon 上散發及銷售其應用程式。 使用者可以在其 Android 架構裝置上，使用 Amazon App Store 應用程式來探索及購買應用程式。 以下顯示在 Android 裝置上執行之 Amazon App Store 的螢幕擷取畫面：

Google Play 可說是最全方位且最受歡迎的 Android 應用程式市集。 Google Play 讓使用者在其裝置或電腦上按一下單一圖示，即可進行應用程式探索、下載、評分及付費。 Google Play 也提供工具來協助分析銷售和市場趨勢，以及管制哪些裝置和使用者可以下載應用程式。 以下顯示在 Android 裝置上執行之 Google Play 的螢幕擷取畫面：

[![Google Play 螢幕擷取畫面](images/google-play-app.png)](images/google-play-app.png#lightbox)

本節說明如何將應用程式連同適當的宣傳資料上傳到商店 (例如 Google Play)。 其中說明 APK 擴充檔，提供有關擴充檔是什麼及其運作方式的概念性概觀。 此外，也說明「Google 授權」服務。 最後則是介紹替代的散發方式，包括使用 HTTP Web 伺服器、簡單電子郵件散發，以及 Amazon App Store for Android。


## <a name="related-links"></a>相關連結

- [HelloWorldPublishing (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/helloworldpublishing)
- [建置流程](~/android/deploy-test/building-apps/build-process.md)
- [連結](~/android/deploy-test/linker.md)
- [取得 Google Maps API 金鑰](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [應用程式簽署](https://source.android.com/security/apksigning/)
- [在 Google Play 上發行](https://developer.android.com/distribute/googleplay/publish/index.html)
- [Google 應用程式授權](https://developer.android.com/guide/google/play/licensing/index.html)
- [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary)
- [行動應用程式散發入口網站](https://developer.amazon.com/welcome.html)
- [Amazon 行動應用程式散發常見問題集](https://developer.amazon.com/help/faq.html)
