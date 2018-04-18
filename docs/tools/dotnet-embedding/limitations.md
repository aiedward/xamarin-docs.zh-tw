---
title: .NET 內嵌限制
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: d8995946966020955a1d9378dea631387ed5f4bd
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/18/2018
---
# <a name="net-embedding-limitations"></a>.NET 內嵌限制

本文件說明的.NET 內嵌 (Embeddinator 4000) 限制，並盡可能提供因應措施為它們。

## <a name="general"></a>一般

### <a name="use-more-than-one-embedded-library-in-a-project"></a>使用多個內嵌的程式庫專案中

您不可以有兩個共置在相同的應用程式內現有的單聲道執行階段。 這表示您不能使用兩個不同 embeddinator 4000 產生庫相同的應用程式內。

**因應措施：**您可用來建立單一的程式庫包含數個組件 （從不同專案） 產生器。

### <a name="subclassing"></a>子類別化

Embeddinator 減輕單聲道的執行階段應用程式內的整合，藉由公開一組目標語言與平台的已備妥要使用 Api。

例如不是兩種方式整合在一起，但是您無法子類別的 managed 型別並預期在您的原生程式碼，因為 managed 程式碼不會知道這個並存回撥的 managed 程式碼。

根據您的需求，可能會因應措施部分這項限制，例如：

* managed 程式碼可以使用 p/invoke 到您的原生程式碼。 這需要自訂以允許自訂原生程式碼; 從 managed 程式碼

* 使用產品，例如 Xamarin.iOS 和公開 managed 程式庫，讓 ObjC （在此情況下） 子類別化某些受管理的 NSObject 子類別。


## <a name="objc-generated-code"></a>ObjC 產生程式碼

### <a name="nullability"></a>Null 屬性

沒有中繼資料，在.NET 中，會告訴我們是否可接受 null 參考或不適用於應用程式開發介面。 大部分的應用程式開發介面將會擲回`ArgumentNullException`如果無法應付`null`引數。 這是有問題，因為 ObjC 處理的例外狀況是較佳避免。

因為我們無法在標頭檔中產生正確的 null 屬性註解，而且想要最小化 managed 例外狀況，所以我們預設為非 null 引數 (`NS_ASSUME_NONNULL_BEGIN`) 並加入一些特定時，會有效位數、 null 屬性註解。
