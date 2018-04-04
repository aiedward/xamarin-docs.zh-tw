---
title: 應用程式散發概觀
description: 這份文件可供 Xamarin.tvOS 應用程式的通訊技術的概觀，並做為更詳細的文件在主題上的指標。
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: e5be0bef158c87fe06516d9a58e34c741e6e14b1
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="app-distribution-overview"></a>應用程式散發概觀

_這份文件可供 Xamarin.tvOS 應用程式的通訊技術的概觀，並做為更詳細的文件在主題上的指標。_


一旦開發 Xamarin.tvOS 應用程式後，軟體開發生命週期的下一個步驟是散發給使用者，應用程式中的下圖反白顯示的區段所示：


[![軟體開發生命週期概觀](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)


Apple 提供下列方式來發佈 tvOS 應用程式支援的 Xamarin.tvOS:

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**內部 (Enterprise)**](#In-House-Distribution) 
2. [**臨機操作**](#Ad_Hoc_Distribution) 

上述每樣方式都要求使用適當的*佈建設定檔*來佈建應用程式。 佈建設定檔是一種檔案，其包含程式碼簽署資訊、應用程式身分識別及預期的散發機制。 對於非 App Store 的散發，佈建設定檔也包含可用來部署應用程式之裝置的相關資訊。

<a name="Apple-TV-App-Store-Distribution" />

## <a name="apple-tv-app-store-distribution"></a>Apple TV App Store 發佈

這是主要 tvOS 應用程式為發佈至 Apple TV 裝置上的取用者的方式。 所有提交至 Apple TV App Store 的應用程式需要由 Apple 的核准。

應用程式是透過名為 *iTunes Connect* 的入口網站提交到 App Store。 請參閱我們[在 iTunes Connect 中設定您的應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)指南以取得有關下列主題的資訊：

- 管理協議、 稅金和銀行。
- 建立 iTunes Connect 記錄。
- 管理應用程式的視訊和螢幕擷取畫面。
- 管理應用程式名稱、 描述、 什麼是新的、 關鍵字和 Url。
- 維護一般應用程式資訊。
- 維護 Game Center 的資訊。
- 維護應用程式檢閱資訊。
- 維護定價資訊。
- 維護應用程式內購買資訊。
- 檢視應用程式的檢閱。

雖然不是特別針對 tvOS 撰寫，這份文件會提供有關如何設定和準備 Apple TV App Store 中發行的應用程式使用此入口網站的資訊。 許多相同的步驟都需要您所設定的 iOS 或 tvOS 應用程式。

當您完成所有上面所列的步驟時，請參閱我們[在 iTunes Connect 中設定您的應用程式的 tvOS](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md)加入 tvOS 應用程式特定資訊所需。

請務必注意：只有隸屬於 **Apple Developer Program** 的開發人員有權存取 iTunes Connect。 **Apple Developer Enterprise Program** 成員沒有存取權限。

如果您有提交至 Apple TV 應用程式市集應用程式 Xamarin.tvOS 的問題，請參閱我們[疑難排解](~/ios/tvos/troubleshooting.md)指南。 它包含許多可能會遇到的已知的問題及如何解決 Xamarin.tvOS。

如需詳細資訊，請瀏覽[發佈到 Apple TV App Store](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md)指南。

<a name="In-House-Distribution" />

## <a name="in-house-distribution"></a>內部作業散發

內部散發 (有時稱為「企業散發」) 允許 **Apple Developer Enterprise Program** 成員在組織內部散發應用程式給同組織的其他成員。 內部散發的優勢在於不需要應用程式市集審查，且不限制可安裝應用程式的裝置數量。 但是請注意，**Apple Developer Enterprise Program** 成員**沒有權限**存取 iTunes Connect，因此被授權者需負責散發應用程式。

如需有關如何取得設定及內部發佈您的應用程式的詳細資訊，請參閱[內部發佈指南](~/ios/deploy-test/app-distribution/in-house-distribution.md)。 這份文件為 iOS 專屬，但是相同的技術會使用 tvOS 應用程式。

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>臨機操作散發

Xamarin.tvOS 應用程式會透過臨機操作散發，這是用於同時測試使用者**Apple 開發人員計劃**，而**Apple Developer Enterprise Program**，並可允許最多 100 個 Apple TV 裝置若要進行測試。 臨機操作發佈的最佳的使用案例時，發佈在公司 iTunes Connect 中不是可行。

如需有關如何取得設定及內部發佈您的應用程式的詳細資訊，請參閱[臨機操作發佈指南](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)。 同樣地，這份文件為 iOS 專屬，但是 tvOS 應用程式使用相同的技術。

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章會提供發佈機制，可供 Xamarin.tvOS 應用程式的簡短概觀。 它引進 Apple TV App Store 臨機操作和內部部署，並提供更詳細的資訊連結。



## <a name="related-links"></a>相關連結

- [tvOS 範例](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人性化介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Tvos 應用程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
