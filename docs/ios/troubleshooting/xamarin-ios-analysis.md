---
title: Xamarin.iOS 分析規則
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/06/2018
ms.openlocfilehash: d942e21208d5a66da3dd849d217be2489311b9a0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinios-analysis-rules"></a>Xamarin.iOS 分析規則

Xamarin.iOS 分析是一組規則，請檢查您的專案設定，可協助您判斷是否會提供更好/最佳化的設定。

儘可能及早找出可能的增強功能，並儲存開發時間通常執行分析規則。

若要執行規則，在 Visual Studio for Mac 的功能表中，選取**專案 > 執行程式碼分析**。

> [!NOTE]
> 您目前所選組態上僅執行 Xamarin.iOS 分析。 我們強烈建議您執行偵錯工具**和**發行組態。

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **問題：**連結器已停用偵錯模式的裝置上。
- **修正：**應該嘗試執行程式碼與連結器，以避免任何意外狀況。
若要設定，請移至專案 > iOS 組建 > 連結器行為。

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **問題：**初始化 Test Cloud 代理程式的應用程式組建將會拒絕的 Apple 時送出，因為它們使用私用應用程式開發介面。
- **修正：**新增或修正必要 #if，然後在程式碼中定義。

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **問題：**只需要散發，現在會使用發佈精靈 」 時，偵錯組態使用簽署金鑰的開發人員不應該產生 IPA。
- **修正：**停用偵錯組態的 IPA 建置中專案選項。

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **問題：**支援的架構為"釋放 |裝置 「 不相容，遺漏 ARM64 64 位元。 這是 Apple 不接受 32 位元只有 iOS 應用程式中，AppStore 問題。
- **修正：** Double iOS 專案上按一下，請移至組建 > iOS 建置，並使其具有 ARM64 變更支援的架構。

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **問題：**不使用 float32 選項 (-aot 選項 = O = float32) 通往沈效能成本，尤其是在行動裝置、 雙精確度數學所在適當地變慢。 請注意，.NET 使用雙精確度就內部而言，即使的浮點數，所以啟用此選項會影響有效位數和可能的相容性。
- **修正：** Double iOS 專案上按一下，請移至組建 > iOS 建置，並取消核取 「 與 64 位元浮點數中執行所有的 32 位元浮點作業 」。

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **問題：**建議使用原生 HttpClient 處理常式，而不受管理的其中一個，以提升效能、 較小的可執行檔大小，並輕鬆地支援較新的標準。
- **修正：** Double iOS 專案上按一下，請移至組建 > iOS 建置和 HttpClient 實作變更 NSUrlSession (iOS 7 +) 或 CFNetwork 支援前面 iOS 7 的版本。
