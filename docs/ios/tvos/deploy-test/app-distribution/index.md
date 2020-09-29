---
title: tvOS 應用程式散發總覽
description: 本檔概述適用于 Xamarin. tvOS 應用程式的散發技術，並可作為主題中更詳細檔的指標。
ms.prod: xamarin
ms.assetid: D5E0F446-C083-4E21-9788-FC84D32D00C4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a9e555c6e4713c4d34f4e14ea29164a769bc3db9
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435635"
---
# <a name="tvos-app-distribution-overview"></a>tvOS 應用程式散發總覽

_本檔概述適用于 Xamarin. tvOS 應用程式的散發技術，並可作為主題中更詳細檔的指標。_

在開發您的 tvOS 應用程式之後，軟體發展生命週期的下一個步驟是將您的應用程式散發給使用者，如下圖中反白顯示的部分所示：

[![軟體發展生命週期總覽](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

Apple 提供下列方法來散發 tvOS 應用程式，這些應用程式是由 Xamarin tvOS 所支援：

1. [**App Store**](#Apple-TV-App-Store-Distribution)
2. [**內部 (Enterprise)**](#In-House-Distribution) 
3. [**臨機操作**](#Ad_Hoc_Distribution) 

上述每樣方式都要求使用適當的*佈建設定檔*來佈建應用程式。 佈建設定檔是一種檔案，其包含程式碼簽署資訊、應用程式身分識別及預期的散發機制。 對於非 App Store 的散發，佈建設定檔也包含可用來部署應用程式之裝置的相關資訊。

<a name="Apple-TV-App-Store-Distribution"></a>

## <a name="apple-tv-app-store-distribution"></a>Apple TV App Store 散發套件

這是將 tvOS 應用程式散發給 Apple TV 裝置上之取用者的主要方式。 所有應用程式提交至 Apple TV App Store 需要 Apple 核准。

應用程式是透過名為 *iTunes Connect* 的入口網站提交到 App Store。 如需下列主題的詳細資訊，請參閱我們 [在 ITunes Connect 中設定您的應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) 指南：

- 管理合約、稅務和銀行。
- 建立 iTunes Connect 記錄。
- 管理應用程式的影片和螢幕擷取畫面。
- 管理應用程式名稱、描述、新增功能、關鍵字和 Url。
- 維護一般應用程式資訊。
- 維護 Game Center 資訊。
- 維護應用程式評論資訊。
- 維護定價資訊。
- 維護應用程式內購買資訊。
- 查看應用程式評論。

雖然不是專為 tvOS 撰寫，但本檔提供有關如何設定和使用此入口網站，以準備您的應用程式以在 Apple TV App Store 中發佈的資訊。 無論您是設定 iOS 或 tvOS 應用程式，都需要許多相同的步驟。

完成上述所有步驟之後，請參閱 [在 ITunes Connect 中設定您的 TvOS 應用程式](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) ，以新增將需要的 tvOS 應用程式特定資訊。

請務必注意：只有隸屬於 **Apple Developer Program** 的開發人員有權存取 iTunes Connect。 **Apple Developer Enterprise Program** 成員沒有存取權限。

如果您在將 tvOS 應用程式提交至 Apple TV App Store 時發生問題，請參閱我們的 [疑難排解](~/ios/tvos/troubleshooting.md) 指南。 它包含數個您可能會遇到的已知問題，以及如何在 tvOS 中解決這些問題。

如需詳細資訊，請參閱 [發行至 APPLE TV App Store](~/ios/tvos/deploy-test/app-distribution/app-store-publishing.md) 指南。

<a name="In-House-Distribution"></a>

## <a name="in-house-distribution"></a>內部作業散發

內部散發 (有時稱為「企業散發」**) 允許 **Apple Developer Enterprise Program** 成員在組織內部散發應用程式給同組織的其他成員。 內部散發的優勢在於不需要應用程式市集審查，且不限制可安裝應用程式的裝置數量。 但是請注意，**Apple Developer Enterprise Program** 成員**沒有權限**存取 iTunes Connect，因此被授權者需負責散發應用程式。

如需有關如何設定以及如何在內部散發您的應用程式的詳細資訊，請參閱 [內部散發指南](~/ios/deploy-test/app-distribution/in-house-distribution.md)。 這份檔專屬於 iOS，但相同的技術適用于 tvOS apps。

<a name="Ad_Hoc_Distribution"></a>

## <a name="ad-hoc-distribution"></a>臨機操作散發

TvOS apps 可透過臨機操作散發進行使用者測試，這可在 **Apple 開發人員計畫**和 **Apple developer Enterprise 方案**上使用，並允許最多100的 Apple TV 裝置進行測試。 臨機操作散發的最佳使用案例是在您的公司內部散發，但無法選擇 iTunes Connect。

如需有關如何設定以及如何在內部散發您的應用程式的詳細資訊，請參閱臨機操作 [散發指南](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)。 同樣地，這份檔只適用于 iOS，但相同的技術用於 tvOS 應用程式。

<a name="Summary"></a>

## <a name="summary"></a>摘要

本文提供適用于 Xamarin. tvOS 應用程式的散發機制簡要總覽。 它引進了 Apple TV App Store、臨機操作和內部部署，以及提供更詳細資訊的連結。

## <a name="related-links"></a>相關連結

- [tvOS 範例](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS 人體介面輔助線](https://developer.apple.com/tvos/human-interface-guidelines/)
- [適用于 tvOS 的應用程式程式設計指南](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)