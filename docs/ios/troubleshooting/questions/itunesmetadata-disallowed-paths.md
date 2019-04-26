---
title: 為什麼我的應用程式提交失敗，而且出現：不允許的路徑 (iTunesMetadata.plist)，請參閱...？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: a4d2769bb0cb4e119f1c90353657471b44eb6c7c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61420901"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>為什麼我的應用程式提交失敗，而且出現：不允許的路徑 (iTunesMetadata.plist)，請參閱...？

> 錯誤：錯誤 ITMS-90047:「 不允許的路徑 ("iTunesMetadata.plist")，請參閱：Payload/iPhoneApp1.app"

此錯誤是因為若要防止使用者遇到問題，例如 Apple App Store 驗證程序中的變更[Bug 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180)。 這個特定的錯誤_未_相關的 Xamarin，您已安裝特定版本，因此降級會_不_幫助。

請參閱論壇討論[此處](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)因應措施資訊和最新的更新，此問題。
