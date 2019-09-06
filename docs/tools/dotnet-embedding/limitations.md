---
title: .NET 嵌入限制
description: 本檔描述 .NET 內嵌的限制，這項工具可讓您使用其他程式設計語言的 .NET 程式碼。
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: cf431d4e3d30ac2ec06bfebc9cebe101411faa1c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292701"
---
# <a name="net-embedding-limitations"></a>.NET 嵌入限制

本檔說明 .NET 內嵌的限制，並盡可能為其提供因應措施。

## <a name="general"></a>一般

### <a name="use-more-than-one-embedded-library-in-a-project"></a>在專案中使用一個以上的內嵌程式庫

同一個應用程式內不可能有兩個 Mono 執行時間並存。 這表示您無法在同一個應用程式中使用兩個不同的 .NET 內嵌產生的程式庫。

**因應措施：** 您可以使用產生器來建立包含數個元件的單一程式庫（來自不同的專案）。

### <a name="subclassing"></a>種類

.NET 內嵌藉由公開適用于目的語言和平臺的一組立即可用的 Api，來減輕應用程式內 Mono 執行時間的整合。

不過，這不是雙向整合，例如，您無法將 managed 類型設為子類別，而且預期 managed 程式碼會在機器碼內回呼，因為您的受控碼不知道此共同並存。

視您的需求而定，可能會因應此限制的部分，例如

* 您的 managed 程式碼可以對機器碼執行 p/invoke。 這需要自訂您的 managed 程式碼，以允許來自機器碼的自訂;

* 使用像是 Xamarin 的產品，並公開可允許目標 C （在此案例中為）的 managed 程式庫，將某些 managed NSObject 子類別子類別化。

## <a name="objective-c-generated-code"></a>目標-C 產生的程式碼

### <a name="nullability"></a>性

.NET 中沒有中繼資料，告訴我們 API 是否可接受 null 參考。 `ArgumentNullException` 大部分`null`的 api 如果無法處理引數，就會擲回。 這會造成問題，因為例外狀況的目標 C 處理是更能避免的事。

因為我們無法在標頭檔中產生精確的 null 屬性注釋，而且想要將 managed 例外狀況降到最低，`NS_ASSUME_NONNULL_BEGIN`所以我們預設為非 null 引數（），並加入一些特定的（如果可能有效位數，則為 null 屬性注釋

### <a name="bitcode-ios"></a>Bitcode （iOS）

目前 .NET 內嵌不支援 iOS 上的 bitcode，其已針對某些 Xcode 專案範本啟用。 這將必須停用，才能成功連結產生的架構。

![Bitcode 選項](images/ios-bitcode-option.png)
