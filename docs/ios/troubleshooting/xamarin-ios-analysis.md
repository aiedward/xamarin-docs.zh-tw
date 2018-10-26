---
title: Xamarin.iOS 分析規則
description: 本文件說明一組檢查 Xamarin.iOS 專案設定，以協助您判斷是否可以使用更多/better optimized 設定的分析規則。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: 8a4990ce7b2bcacbd4b97b214458531b3d94122e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121031"
---
# <a name="xamarinios-analysis-rules"></a>Xamarin.iOS 分析規則

Xamarin.iOS 分析是一組規則，請檢查您的專案設定，以協助您判斷是否有更好或多個最佳化的設定。

盡早找到可能的改進，並節省開發時間通常執行分析規則。

若要執行規則，Visual Studio for Mac 的功能表中，選取**專案 > 執行程式碼分析**。

> [!NOTE]
> Xamarin.iOS 分析只會在您目前所選組態上執行。 我們強烈建議您執行偵錯工具**和**發行組態。

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001: DisabledLinkerRule

- **問題：** 連結器已停用裝置的偵錯模式。
- **修正：** 您應該嘗試使用連結器，以避免收到任何意外執行您的程式碼。
若要設定，請移至專案 > iOS 組建 > 連結器行為。

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002: TestCloudAgentReleaseRule

- **問題：** 初始化測試雲端代理程式的應用程式組建將會遭到拒絕由 Apple 提交時，因為它們使用私人的 API。
- **修正：** 新增或修正必要 #if，然後在程式碼中定義。

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003: IPADebugBuildsRule

- **問題：** 偵錯組態，會使用簽署金鑰的開發人員不應該產生 IPA，只需要散發，現在會使用發佈精靈。
- **修正：** 停用專案選項中的偵錯組態的 IPA 組建。

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004: Missing64BitSupportRule

- **問題：** 支援的架構，如 「 發行 |裝置 「 不相容，遺漏 ARM64 64 位元。 如 Apple 不接受 32 位元唯一 iOS 應用程式，AppStore，這會是問題。
- **修正：** Double 按一下 iOS 專案，請移至組建 > iOS 建置，並使其具有 ARM64 變更支援的架構。

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005: Float32Rule

- **問題：** 未使用的 float32 選項 (-aot 選項 =-O = float32) 會導致可觀的效能成本，尤其是在行動裝置、 雙精確度數學所在位置適當地變慢。 請注意，.NET 使用雙精確度就內部而言，即使是針對浮點數，所以啟用此選項會影響有效位數，而且可能相容性。
- **修正：** Double 按一下 iOS 專案，請移至組建 > iOS 建置，並取消核取 「 與 64 位元浮點數中執行所有的 32 位元浮點作業 」。

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006: HttpClientAvoidManaged

- **問題：** 我們建議使用原生的 HttpClient 處理常式，而不受管理的更佳的效能，較小的可執行檔大小，並輕鬆地支援較新標準。
- **修正：** Double 按一下 iOS 專案，請移至組建 > iOS 建置，並變更 HttpClient 實作 NSUrlSession (iOS 7 +) 或 CFNetwork 支援上述 iOS 7 的版本。
