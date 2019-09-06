---
title: 為什麼我的應用程式提交失敗，而且出現：在 ...？找到不允許的路徑（Itunesmetadata.plist. plist）
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: AE1BBDC6-4D3A-4471-876B-FE28B6E59A24
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: ad663c47520826cc549011c9e5fc7cbb078d6214
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291024"
---
# <a name="why-does-my-app-submission-fail-with-disallowed-paths--itunesmetadataplist--found-at--"></a>為什麼我的應用程式提交失敗，而且出現：在 ...？找到不允許的路徑（Itunesmetadata.plist. plist）

> 糾錯錯誤 ITMS-90047：在下列位置找到「不允許的路徑（"Itunesmetadata.plist. plist"）：裝載/iPhoneApp1 應用程式」

此錯誤是因為 Apple 的 App Store 驗證程式中的變更，而導致使用者無法碰到[錯誤 29180](https://bugzilla.xamarin.com/show_bug.cgi?id=29180)之類的問題。 此特定錯誤與您安裝的特定 Xamarin_版本無關，_ 因此降級將_不_會有説明。

如需此問題的因應措施資訊和最新更新，請參閱[這裡](https://forums.xamarin.com/discussion/40388/disallowed-paths-itunesmetadata-plist-found-at-when-submitting-to-app-store/p1)的論壇討論。
