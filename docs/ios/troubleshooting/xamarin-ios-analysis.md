---
title: "Xamarin.iOS 分析規則"
ms.topic: article
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/26/2017
ms.openlocfilehash: 7cf627f369b666bb54d0f512dc1361d2a685a057
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinios-analysis-rules"></a>Xamarin.iOS 分析規則


## <a name="a-namexia0001xia0001-disabledlinkerrule"></a><a name="XIA0001"/>XIA0001: DisabledLinkerRule

- **問題：**連結器已停用偵錯模式的裝置上。
- **修正：**應該嘗試執行程式碼與連結器，以避免任何意外狀況。
若要設定，請移至專案 > iOS 組建 > 連結器行為。

## <a name="a-namexia0002xia0002-testcloudagentreleaserule"></a><a name="XIA0002"/>XIA0002: TestCloudAgentReleaseRule

- **問題：**初始化 Test Cloud 代理程式的應用程式組建將會拒絕的 Apple 時送出，因為它們使用私用應用程式開發介面。
- **修正：**新增或修正必要 #if，然後在程式碼中定義。

## <a name="a-namexia0003xia0003-ipadebugbuildsrule"></a><a name="XIA0003"/>XIA0003: IPADebugBuildsRule

- **問題：**只需要散發，現在會使用發佈精靈 」 時，偵錯組態使用簽署金鑰的開發人員不應該產生 IPA。
- **修正：**停用偵錯組態的 IPA 建置中專案選項。

## <a name="a-namexia0004xia0004-missing64bitsupportrule"></a><a name="XIA0004"/>XIA0004: Missing64BitSupportRule

- **問題：**支援的架構為"釋放 |裝置 「 不相容，遺漏 ARM64 64 位元。 這是 Apple 不接受 32 位元只有 iOS 應用程式中，AppStore 問題。
- **修正：** Double iOS 專案上按一下，請移至組建 > iOS 建置，並使其具有 ARM64 變更支援的架構。

## <a name="a-namexia0005xia0005-float32rule"></a><a name="XIA0005"/>XIA0005: Float32Rule

- **問題：**不使用 float32 選項 (-aot 選項 = O = float32) 通往沈效能成本，特別是在行動裝置、 雙精確度數學所在適當地變慢。 請注意，.NET 使用雙精確度就內部而言，即使的浮點數，所以啟用此選項會影響有效位數和可能的相容性。
- **修正：** Double iOS 專案上按一下，請移至組建 > iOS 建置，並取消核取 「 與 64 位元浮點數中執行所有的 32 位元浮點作業 」。
