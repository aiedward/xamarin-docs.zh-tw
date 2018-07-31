---
title: 為什麼我的應用程式提交無法使用： 不允許的路徑 (iTunesMetadata.plist)，請參閱...？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/03/2018
ms.openlocfilehash: c243dc84f696a5ac08f11a5e74f41c5c0914d0ea
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351108"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>為什麼我的應用程式提交無法使用： 不允許的路徑 (iTunesMetadata.plist)，請參閱...？

> 錯誤： 錯誤 ITMS 90047: 「 不允許的路徑 ("iTunesMetadata.plist")，請參閱： Payload/iPhoneApp1.app"

此錯誤是因為若要防止使用者遇到問題，例如 Apple App Store 驗證程序中的變更[Bug 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180)。 這個特定的錯誤_未_相關的 Xamarin，您已安裝特定版本，因此降級會_不_幫助。

請參閱論壇討論[此處](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)因應措施資訊和最新的更新，此問題。
