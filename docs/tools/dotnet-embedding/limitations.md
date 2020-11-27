---
title: .NET 嵌入限制
description: 本檔說明 .NET 內嵌的限制，這是可讓您以其他程式設計語言使用 .NET 程式碼的工具。
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 2fbd42efcdb3de10a7f094db2197a75d88d27b66
ms.sourcegitcommit: 8fa0cb9ccbc107d697aa5b9113a4e5d1e75d6eb9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303022"
---
# <a name="net-embedding-limitations"></a>.NET 嵌入限制

本檔說明 .NET 內嵌的限制，並盡可能提供解決方法的因應措施。

## <a name="general"></a>一般

### <a name="use-more-than-one-embedded-library-in-a-project"></a>在專案中使用一個以上的內嵌程式庫

在相同的應用程式中，不可能有兩個共有的 Mono 執行時間。 這表示您不能在同一個應用程式中使用兩個不同的 .NET 嵌入產生的程式庫。

因應措施 **：** 您可以使用產生器來建立單一程式庫，其中包含數個從不同專案 (的元件) 。

### <a name="subclassing"></a>種類

.NET 內嵌可針對目的語言和平臺公開一組現成可用的 Api，藉此簡化應用程式內 Mono 執行時間的整合。

不過這不是雙向整合，例如，您無法將 managed 型別的子類別化，並預期 managed 程式碼在機器碼內部回呼，因為您的 managed 程式碼不會察覺到這種共存情形。

視您的需求而定，可能會因應這項限制的部分，例如

* 您的 managed 程式碼可以在機器碼中進行 p/invoke。 這需要自訂您的 managed 程式碼，以允許從機器碼進行自訂;

* 使用像是 Xamarin 的產品，並公開受管理的程式庫，以允許目標 C (在此案例中) 將某些 managed NSObject 子類別子類別化。

## <a name="objective-c-generated-code"></a>目標-C 產生的程式碼

### <a name="nullability"></a>Null 屬性

.NET 中沒有中繼資料可讓我們知道 API 是否可接受 null 參考。 如果大部分的 Api `ArgumentNullException` 無法處理引數，將會擲回 `null` 。 這會造成問題，因為例外狀況的目標 C 處理是更能避免的問題。

由於我們無法在標頭檔中產生精確的可 null 性注釋，而且想要將 managed 例外狀況降至最低，因此預設為非 null 引數 (`NS_ASSUME_NONNULL_BEGIN`) 並新增一些特定的，可以是可 null 性注釋。

### <a name="bitcode-ios"></a>Bitcode (iOS) 

目前，.NET 內嵌不支援 iOS 上的 bitcode，可針對某些 Xcode 專案範本啟用。 您必須停用此功能，才能成功連結產生的 framework。

* 針對 iOS，bitcode 可選擇性地將應用程式提交至 Apple 的 AppStore。 因為產生的 bitcode 是「內嵌元件」，所以不支援 iOS 版的 ios。 這對 iOS 平臺沒有任何好處，因為它無法優化伺服器端，但會使二進位檔變得更大且組建時間更長。

* 針對 tvOS 和 watchOS，需要 bitcode 才能將應用程式提交至 Apple 的 AppStore。 Xamarin 支援在 tvOS (上 bitcode 為「內嵌元件」 ) ，並將 watchOS (為 "LLVM/IR" ) 以滿足這項需求。

* 針對 macOS，目前不需要 bitcode 支援，也不支援 Xamarin。

![Bitcode 選項](images/ios-bitcode-option.png)
