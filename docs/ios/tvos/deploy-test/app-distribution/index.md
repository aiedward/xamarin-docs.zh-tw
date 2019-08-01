---
title: tvOS 應用程式散發總覽
description: 本檔概述 tvOS 應用程式可用的散發技術, 並作為主題中更詳細檔的指標。
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: df3a3462923ab396037f9267194588ffcb03f9e5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655231"
---
# <a name="tvos-app-distribution-overview"></a>tvOS 應用程式散發總覽

_本檔概述 tvOS 應用程式可用的散發技術, 並作為主題中更詳細檔的指標。_


開發好 tvOS 應用程式之後, 軟體發展生命週期的下一步就是將您的應用程式散發給使用者, 如下圖中反白顯示的部分所示:


[![軟體發展生命週期總覽](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)


Apple 提供下列方法來散發 tvOS 應用程式, 其支援 tvOS:

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**內部 (Enterprise)** ](#In-House-Distribution) 
3. [**臨機操作**](#Ad_Hoc_Distribution) 

上述每樣方式都要求使用適當的*佈建設定檔*來佈建應用程式。 佈建設定檔是一種檔案，其包含程式碼簽署資訊、應用程式身分識別及預期的散發機制。 對於非 App Store 的散發，佈建設定檔也包含可用來部署應用程式之裝置的相關資訊。

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Apple TV App Store 散發

這是將 tvOS 應用程式散發給 Apple TV 裝置上的取用者的主要方式。 提交至 Apple TV App Store 的所有應用程式都需要 Apple 核准。

應用程式是透過名為 *iTunes Connect* 的入口網站提交到 App Store。 如需下列主題的詳細資訊, 請參閱在[ITunes Connect 中設定您的應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南:

- 管理協定、稅務和銀行。
- 建立 iTunes Connect 記錄。
- 管理應用程式的影片和螢幕擷取畫面。
- 管理應用程式名稱、描述、新增功能、關鍵字和 Url。
- 維護一般應用程式資訊。
- 維護 Game Center 資訊。
- 維護應用程式審查資訊。
- 維護定價資訊。
- 維護應用程式內購買資訊。
- 觀看應用程式審查。

本檔不是特別針對 tvOS 撰寫, 而是提供如何設定和使用此入口網站來準備您的應用程式以在 Apple 電視 App Store 中發佈的相關資訊。 無論您是設定 iOS 或 tvOS 應用程式, 都需要許多相同的步驟。

完成上述所有步驟之後, 請參閱[在 ITunes Connect 中設定您的 TvOS 應用程式](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md), 以新增需要的 tvOS 應用程式特定資訊。

請務必注意：只有隸屬於 **Apple Developer Program** 的開發人員有權存取 iTunes Connect。 **Apple Developer Enterprise Program** 成員沒有存取權限。

如果您在將 tvOS 應用程式提交至 Apple 電視 App Store 時遇到問題, 請參閱我們的[疑難排解](~/ios/tvos/troubleshooting.md)指南。 其中包含數個您可能會遇到的已知問題, 以及如何在 tvOS 中解決它們。

如需詳細資訊, 請流覽[發行至 APPLE 電視 App Store](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md)指南。

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>內部作業散發

內部散發 (有時稱為「企業散發」  ) 允許 **Apple Developer Enterprise Program** 成員在組織內部散發應用程式給同組織的其他成員。 內部散發的優勢在於不需要應用程式市集審查，且不限制可安裝應用程式的裝置數量。 但是請注意，**Apple Developer Enterprise Program** 成員**沒有權限**存取 iTunes Connect，因此被授權者需負責散發應用程式。

如需有關如何進行設定, 以及如何在內部散發應用程式的詳細資訊, 請參閱[內部散發指南](~/ios/deploy-test/app-distribution/in-house-distribution.md)。 本檔僅適用于 iOS, 但 tvOS 應用程式使用相同的技術。

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>臨機操作散發

TvOS 應用程式可透過臨機操作散發來進行使用者測試, 這可在**Apple Developer program**和**Apple developer Enterprise program**上取得, 並允許最多100的 apple TV 裝置進行測試。 當 iTunes Connect 不可行時, 臨機操作散發的最佳使用案例是在公司內散發。

如需有關如何進行設定, 以及如何在內部散發應用程式的詳細資訊, 請參閱臨機操作[散發指南](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)。 同樣地, 這份檔僅適用于 iOS, 但 tvOS 應用程式使用相同的技術。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章提供 tvOS apps 的散發機制簡要概述。 它引進了 Apple TV App Store、臨機操作和內部部署, 並提供更多詳細資訊的連結。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人力介面指南](https://developer.apple.com/tvos/human-interface-guidelines/)
- [TvOS 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
