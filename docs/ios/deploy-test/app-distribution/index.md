---
title: Xamarin.iOS 應用分發概述
description: 本文件提供散發技巧的概觀，其適用於 Xamarin iOS 應用程式，並作為本主題中更詳細文件的指標。
ms.prod: xamarin
ms.assetid: 341D36DB-BB07-FA94-BCC9-5F8C0B18C179
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 20126849027f735e9ecd3599c290b4e7a57f837e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "75886576"
---
# <a name="xamarinios-app-distribution-overview"></a>Xamarin.iOS 應用分發概述

本文件提供散發技術的概觀，其適用於 Xamarin iOS 應用程式，並作為本主題中更詳細文件的指標。__

完成 Xamarin.iOS 應用程式開發之後，軟體開發生命週期的下一步就是將應用程式散發給使用者，如下圖中醒目標示的部分所示：

[![開發 iOS 應用後,下一步是將應用分發給使用者,如此關係圖突出顯示的部分所示](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

Apple 提供了以下分發 iOS 應用程式的方法:

- [**App Store**](#app-store-distribution)
- [**內部(企業)**](#in-house-distribution)
- [**臨時**](#ad-hoc-distribution)
- [**自訂的自訂應用程式**](#custom-apps-for-business)

上述每樣方式都要求使用適當的*佈建設定檔*來佈建應用程式。 佈建設定檔是一種檔案，其包含程式碼簽署資訊、應用程式身分識別及預期的散發機制。 對於非 App Store 的散發，佈建設定檔也包含可用來部署應用程式之裝置的相關資訊。

## <a name="app-store-distribution"></a>App Store 散發

> [!IMPORTANT]
> Apple [已宣佈](https://developer.apple.com/ios/submit/)從 2019 年 3 月開始，提交至 App Store 的所有應用程式與更新，都必須使用 iOS 12.1 SDK 或更新版本 (包含在 Xcode 10.1 或更新版本中) 進行建置。
> 應用程式也應支援 iPhone XS 及 12.9" iPad Pro 的螢幕大小。

這是將 iOS 應用程式散發給 iOS 裝置使用者的主要方式。 提交到 App Store 的所有應用程式都需經過 Apple 核准。

應用程式是透過名為 *iTunes Connect* 的入口網站提交到 App Store。 [Configure your App in iTunes](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) Connect (在 iTunes Connect 中設定您的應用程式) 指南提供了詳細資訊：如何設定和使用此入口網站來準備 Xamarin.iOS 應用程式，以在 App Store 中發佈。

請務必注意：只有隸屬於 **Apple Developer Program** 的開發人員有權存取 iTunes Connect。 **Apple Developer Enterprise Program** 成員沒有存取權限。

如需詳細資訊，請瀏覽[應用程式市集散發](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)指南。

## <a name="in-house-distribution"></a>內部配置

內部散發 (有時稱為「企業散發」**) 允許 **Apple Developer Enterprise Program** 成員在組織內部散發應用程式給同組織的其他成員。 內部散發的優勢在於不需要應用程式市集審查，且不限制可安裝應用程式的裝置數量。 但是請注意，**Apple Developer Enterprise Program** 成員**沒有權限**存取 iTunes Connect，因此被授權者需負責散發應用程式。

有關設置以及如何在內部分發申請的詳細資訊,請參閱[內部分髮指南](~/ios/deploy-test/app-distribution/in-house-distribution.md)。

## <a name="ad-hoc-distribution"></a>臨機操作散發

Xamarin.iOS 應用程式可以通過臨時分發進行用戶測試,這在**Apple開發人員計劃和****Apple開發人員企業計劃中**都有,並且允許最多測試100台iOS設備。 臨機操作散發的最佳使用案例為在公司內部散發但無法使用 iTunes Connect 時。

有關設定以及如何在內部分發應用程式的詳細資訊,請參閱[臨時分發指南](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)。

## <a name="custom-apps-for-business"></a>自訂的自訂應用程式

Apple 允許[將應用程式自定義分發](https://developer.apple.com/business/custom-apps/)到企業和教育。 查看[Apple 業務經理使用者指南](https://support.apple.com/guide/apple-business-manager/welcome/web),瞭解相關資訊。

## <a name="related-links"></a>相關連結

- [App Store 散發](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [在 iTunes 連接中設定應用](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [發行至 App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [內部配置](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [臨機操作散發](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [iTunesMetadata.plist 檔案](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [IPA 支援](~/ios/deploy-test/app-distribution/ipa-support.md)
- [疑難排解](~/ios/deploy-test/troubleshooting.md)
