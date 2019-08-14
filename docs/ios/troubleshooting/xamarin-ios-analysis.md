---
title: Xamarin iOS 分析規則
description: 本檔說明一組分析規則, 可檢查 Xamarin iOS 專案設定, 以協助判斷是否有更多/更好的優化設定。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C29B69F5-08E4-4DCC-831E-7FD692AB0886
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/06/2018
ms.openlocfilehash: 5f968e01cc0b866f94f524728b4bba1e759e8bf8
ms.sourcegitcommit: 9f37dc00c2adab958025ad1cdba9c37f0acbccd0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2019
ms.locfileid: "69012390"
---
# <a name="xamarinios-analysis-rules"></a>Xamarin iOS 分析規則

「Xamarin iOS 分析」是一組規則, 可檢查您的專案設定, 協助您判斷是否有更好/更多的優化設定可供使用。

盡可能執行分析規則, 儘早找出可能的改進, 並節省開發時間。

若要執行規則, 請在 Visual Studio for Mac 的功能表中, 選取 [**專案 > 執行程式碼分析**]。

> [!NOTE]
> Xamarin。 iOS 分析只會在您目前選取的設定上執行。 我們強烈建議您執行此工具來進行 debug**和**release 設定。

<a name="XIA0001" />

## <a name="xia0001-disabledlinkerrule"></a>XIA0001:DisabledLinkerRule

- **問題：** 已在裝置上停用連結器以進行 debug 模式。
- **補丁**您應該嘗試使用連結器來執行您的程式碼, 以避免發生任何意外的情況。
若要設定, 請移至 Project > iOS Build > 連結器行為。

<a name="XIA0002" />

## <a name="xia0002-testcloudagentreleaserule"></a>XIA0002:TestCloudAgentReleaseRule

- **問題：** Apple 會在提交時拒絕將測試雲端代理程式初始化的應用程式組建, 因為他們使用私用 API。
- **補丁**新增或修正必要的 #if, 並在程式碼中定義。

<a name="XIA0003" />

## <a name="xia0003-ipadebugbuildsrule"></a>XIA0003:IPADebugBuildsRule

- **問題：** 使用開發人員簽署金鑰的 Debug 設定不應產生 .IPA, 因為它只需要進行散發, 現在會使用發佈嚮導。
- **補丁**針對 Debug 設定, 停用專案選項中的 .IPA 組建。

<a name="XIA0004" />

## <a name="xia0004-missing64bitsupportrule"></a>XIA0004:Missing64BitSupportRule

- **問題：** 支援的架構 "release |裝置「不是64位相容, 缺少 ARM64。 這是問題, 因為 Apple 不接受 AppStore 中32位的 iOS 應用程式。
- **補丁**按兩下您的 iOS 專案, 移至 [組建] > [iOS] [組建], 然後變更支援的架構, 使其 ARM64。

<a name="XIA0005" />

## <a name="xia0005-float32rule"></a>XIA0005:Float32Rule

- **問題：** 不使用 float32 選項 (--aot-options =-O = float32) 會導致等於沉重的效能成本, 尤其是在行動裝置上, 雙精確度運算的顯著提升速度較慢。 請注意, .NET 會在內部使用雙精確度 (即使是 float), 因此, 啟用此選項會影響精確度和可能的相容性。
- **補丁**按兩下您的 iOS 專案, 移至 [組建] > [iOS 組建], 然後取消核取 [以64位浮點數執行所有32位浮點數作業]。

<a name="XIA0006" />

## <a name="xia0006-httpclientavoidmanaged"></a>XIA0006:HttpClientAvoidManaged

- **問題：** 我們建議使用原生 HttpClient 處理常式, 而不是受控的, 以獲得更好的效能、較小的可執行檔案大小, 以及輕鬆地支援較新的標準。
- **補丁**按兩下您的 iOS 專案, 移至 [組建] > [iOS] [組建], 然後將 HttpClient 執行變更為 NSUrlSession (iOS 7 +) 或 CFNetwork, 以支援 iOS 7 之前的版本。

<a name="XIA0007" />

## <a name="xia0007-usellvmrule"></a>XIA0007:UseLLVMRule

- **問題：** 針對 [發行] | [iPhone 設定], 建議您啟用 LLVM 編譯器, 它會產生更快速執行的程式碼, 以建立時間的代價。
- **補丁**按兩下您的 iOS 專案, 移至 [組建] > [iOS 組建], 然後針對 [發行] | [iPhone], 檢查 [LLVM 優化編譯器] 選項。
