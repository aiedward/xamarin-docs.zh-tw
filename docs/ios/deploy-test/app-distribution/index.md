---
title: Xamarin.iOS 應用程式散發概觀
description: 本文件提供散發技巧的概觀，其適用於 Xamarin iOS 應用程式，並作為本主題中更詳細文件的指標。
ms.prod: xamarin
ms.assetid: 341D36DB-BB07-FA94-BCC9-5F8C0B18C179
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 815277e9a4f9384d92bf17376f426cacd40dbc9f
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209436"
---
# <a name="xamarinios-app-distribution-overview"></a>Xamarin.iOS 應用程式散發概觀

本文件提供散發技術的概觀，其適用於 Xamarin iOS 應用程式，並作為本主題中更詳細文件的指標。

完成 Xamarin.iOS 應用程式開發之後，軟體開發生命週期的下一步就是將應用程式散發給使用者，如下圖中醒目標示的部分所示：


[![](images/publishingdiagram.png "Xamarin.iOS 應用程式開發完成後，下一步就是散發給使用者，如本圖中醒目標示的部分所示")](images/publishingdiagram.png#lightbox)


Apple 提供以下方式散發 iOS 應用程式，這些方式都受到 Xamarin.iOS 支援：

1. [**App Store**](#App_Store_Distribution)
2. [**內部 (Enterprise)**](#In-House_Distribution)
2. [**臨機操作**](#Ad_Hoc_Distribution)

上述每樣方式都要求使用適當的*佈建設定檔*來佈建應用程式。 佈建設定檔是一種檔案，其包含程式碼簽署資訊、應用程式身分識別及預期的散發機制。 對於非 App Store 的散發，佈建設定檔也包含可用來部署應用程式之裝置的相關資訊。

<a name="App_Store_Distribution"/>

## <a name="app-store-distribution"></a>App Store 散發

> [!IMPORTANT]
> Apple [已宣告](https://developer.apple.com/news/?id=05072018a)從 2018 年 7 月開始，提交至 App Store 的所有應用程式和更新都必須是使用 iOS 11 SDK 建置，且必須[支援 iPhone X 顯示器](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md)。

這是將 iOS 應用程式散發給 iOS 裝置使用者的主要方式。 提交到 App Store 的所有應用程式都需經過 Apple 核准。

應用程式是透過名為 *iTunes Connect* 的入口網站提交到 App Store。 [Configure your App in iTunes](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) Connect (在 iTunes Connect 中設定您的應用程式) 指南提供了詳細資訊：如何設定和使用此入口網站來準備 Xamarin.iOS 應用程式，以在 App Store 中發佈。

請務必注意：只有隸屬於 **Apple Developer Program** 的開發人員有權存取 iTunes Connect。 **Apple Developer Enterprise Program** 成員沒有存取權限。

如需詳細資訊，請瀏覽[應用程式市集散發](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)指南。

<a name="In-House_Distribution"/>

## <a name="in-house-distribution"></a>內部散發

內部散發 (有時稱為「企業散發」) 允許 **Apple Developer Enterprise Program** 成員在組織內部散發應用程式給同組織的其他成員。 內部散發的優勢在於不需要應用程式市集審查，且不限制可安裝應用程式的裝置數量。 但是請注意，**Apple Developer Enterprise Program** 成員**沒有權限**存取 iTunes Connect，因此被授權者需負責散發應用程式。

對於著手設定與如何使用內部方式來散發應用程式的詳細資訊，請參閱[內部散發指南](~/ios/deploy-test/app-distribution/in-house-distribution.md)。

<a name="Ad_Hoc_Distribution"/>

## <a name="ad-hoc-distribution"></a>臨機操作散發

Xamarin.iOS 應用程式可透過臨機操作散發進行使用者測試，臨機操作散發可在 **Apple Developer Program** 和 **Apple Developer Enterprise Program** 中取得，最多可測試 100 部 iOS 裝置。 臨機操作散發的最佳使用案例為在公司內部散發但無法使用 iTunes Connect 時。

對於著手設定與如何使用內部方式來散發應用程式的詳細資訊，請參閱[臨機操作散發指南](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)。

## <a name="summary"></a>總結

本文提供了適用於 Xamarin.iOS 應用程式散發機制的簡要概觀。 其介紹 iTunes App Store、臨機操作、內部部署，並提供可取得詳細資料的相關連結。

## <a name="related-links"></a>相關連結

- [散發](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [在 iTunes Connect 中設定應用程式](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [發行至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [內部作業散發](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [臨機操作散發](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [iTunesMetadata.plist 檔案](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [IPA 支援](~/ios/deploy-test/app-distribution/ipa-support.md)
- [疑難排解](~/ios/deploy-test/troubleshooting.md)
