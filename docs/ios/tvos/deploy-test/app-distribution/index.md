---
title: tvOS 應用程式散發概觀
description: 本文件提供散發技術的概觀，可供 Xamarin.tvOS 應用程式，並作為本主題的更詳細文件的指標。
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 0a40d50d02008439e81d5db19bcda0647203e2da
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61414402"
---
# <a name="tvos-app-distribution-overview"></a>tvOS 應用程式散發概觀

_本文件提供散發技術的概觀，可供 Xamarin.tvOS 應用程式，並作為本主題的更詳細文件的指標。_


完成 Xamarin.tvOS 應用程式開發之後，軟體開發生命週期的下一個步驟是應用程式散發給使用者下, 圖中反白顯示一節中所示：


[![軟體開發生命週期概觀](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)


Apple 提供下列方式來散發 tvOS 應用程式，支援的 Xamarin.tvOS:

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**內部 (Enterprise)**](#In-House-Distribution) 
2. [**臨機操作**](#Ad_Hoc_Distribution) 

上述每樣方式都要求使用適當的*佈建設定檔*來佈建應用程式。 佈建設定檔是一種檔案，其包含程式碼簽署資訊、應用程式身分識別及預期的散發機制。 對於非 App Store 的散發，佈建設定檔也包含可用來部署應用程式之裝置的相關資訊。

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Apple TV App Store 散發

這是在 Apple TV 裝置上的取用者發佈的 tvOS 應用程式的主要方式。 所有提交至 Apple TV App Store 的應用程式需要經過 Apple 核准。

應用程式是透過名為 *iTunes Connect* 的入口網站提交到 App Store。 請參閱我們[在 iTunes Connect 中設定您的應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南以取得資訊的下列主題：

- 管理合約、 稅務和銀行業務。
- 建立 iTunes Connect Record。
- 管理應用程式的影片和螢幕擷取畫面。
- 管理應用程式名稱、 描述、 新功能、 關鍵字和 Url。
- 維護應用程式一般資訊。
- 維護 Game Center 資訊。
- 維護應用程式檢閱資訊。
- 維護定價資訊。
- 維護應用程式內購買資訊。
- 檢視應用程式評論。

雖然不是特別針對 tvOS 撰寫，這份文件會提供有關如何設定和準備在 Apple TV App Store 中發佈您的應用程式中使用此入口網站的資訊。 許多相同的步驟都需要您所設定的 iOS 或 tvOS 應用程式。

一旦您完成所有上述步驟，請參閱我們[在 iTunes Connect 中設定您的 tvOS 應用程式](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md)新增 tvOS 應用程式特定資訊所需。

請務必注意：只有隸屬於 **Apple Developer Program** 的開發人員有權存取 iTunes Connect。 **Apple Developer Enterprise Program** 成員沒有存取權限。

如果您有應用程式提交 Xamarin.tvOS 至 Apple TV App Store 的問題，請參閱我們[疑難排解](~/ios/tvos/troubleshooting.md)指南。 它包含數個您可能會遇到的已知的問題及如何解決這些問題中 Xamarin.tvOS。

如需詳細資訊，請瀏覽[發行至 Apple TV App Store](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md)指南。

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>內部作業散發

內部散發 (有時稱為「企業散發」) 允許 **Apple Developer Enterprise Program** 成員在組織內部散發應用程式給同組織的其他成員。 內部散發的優勢在於不需要應用程式市集審查，且不限制可安裝應用程式的裝置數量。 但是請注意，**Apple Developer Enterprise Program** 成員**沒有權限**存取 iTunes Connect，因此被授權者需負責散發應用程式。

如需有關如何著手設定與如何使用內部方式來散發您的應用程式的詳細資訊，請參閱[內部散發指南](~/ios/deploy-test/app-distribution/in-house-distribution.md)。 這份文件為 iOS 專屬，但是相同的技術用於 tvOS 應用程式。

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>臨機操作散發

Xamarin.tvOS 應用程式可透過臨機操作散發，這可在使用者測試**Apple Developer Program**，而**Apple Developer Enterprise Program**，並允許最多 100 個 Apple TV 裝置若要進行測試。 ITunes Connect 不是可行時，臨機操作散發的最佳使用案例會是您公司內的分佈。

如需有關如何著手設定與如何使用內部方式來散發您的應用程式的詳細資訊，請參閱[臨機操作散發指南](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)。 同樣地，這份文件為 iOS 專屬，但是相同的技術用於 tvOS 應用程式。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章提供了可供 Xamarin.tvOS 應用程式的發佈機制的簡要概觀。 它導入 Apple TV App Store、 臨機操作、 內部部署，並提供更多詳細資訊的連結。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
