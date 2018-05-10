---
title: .NET 內嵌限制
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 8ea3e7c6ff2fc28700c46109a814fc5da6958500
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="net-embedding-limitations"></a>.NET 內嵌限制

本文件說明的.NET 內嵌限制，並盡可能提供因應措施為它們。

## <a name="general"></a>一般

### <a name="use-more-than-one-embedded-library-in-a-project"></a>使用多個內嵌的程式庫專案中

您不可以有兩個共置在相同的應用程式內現有的單聲道執行階段。 這表示您不能使用兩個不同.NET 內嵌所產生的程式庫相同的應用程式內。

**因應措施：**您可用來建立單一的程式庫包含數個組件 （從不同專案） 產生器。

### <a name="subclassing"></a>子類別化

.NET 內嵌時，可以減輕單聲道的執行階段應用程式內的整合工作由公開一組目標語言與平台的已備妥要使用 Api。

例如不是雙向整合，不過您無法子類別的 managed 型別並預期在您的原生程式碼，因為 managed 程式碼不會知道這個並存回撥的 managed 程式碼。

根據您的需求，可能會因應措施部分這項限制，例如：

* managed 程式碼可以使用 p/invoke 到您的原生程式碼。 這需要自訂以允許自訂原生程式碼; 從 managed 程式碼

* 使用 Xamarin.iOS 等產品，並公開 Objective C （在此情況下） 子類別化受管理某些 NSObject 子類別可讓 managed 程式庫。

## <a name="objective-c-generated-code"></a>Objective C 產生的程式碼

### <a name="nullability"></a>Null 屬性

沒有中繼資料中告訴我們是否可接受 null 參考的.NET 或不適用於應用程式開發介面。 大部分的應用程式開發介面將會擲回`ArgumentNullException`如果無法應付`null`引數。 這是有問題，因為 OBJECTIVE-C 處理的例外狀況是較佳避免。

因為我們無法在標頭檔中產生正確的 null 屬性註解，而且想要最小化 managed 例外狀況，所以我們預設為非 null 引數 (`NS_ASSUME_NONNULL_BEGIN`) 並加入一些特定時，會有效位數、 null 屬性註解。

### <a name="bitcode-ios"></a>Bitcode (iOS)

目前內嵌.NET 不支援 bitcode 在 iOS 上，會啟用一些 Xcode 專案範本。 這會有產生的連結架構已成功停用。

![Bitcode 選項](images/ios-bitcode-option.png)
