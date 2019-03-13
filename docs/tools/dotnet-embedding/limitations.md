---
title: .NET 內嵌限制
description: 本文件說明的內嵌.NET，此工具，可讓您使用其他程式設計語言中的.NET 程式碼的限制。
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 7a162d632c98b4e412fa1b7b0c0c40ac945ff09f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114349"
---
# <a name="net-embedding-limitations"></a>.NET 內嵌限制

本文件說明的.NET 內嵌限制，並可能的話，請為他們提供因應措施。

## <a name="general"></a>一般

### <a name="use-more-than-one-embedded-library-in-a-project"></a>使用多個內嵌的文件庫中的專案

您不可以有兩個共置在相同的應用程式內現有的 Mono 執行階段。 這表示您無法使用兩個不同.NET 內嵌所產生的程式庫在相同的應用程式。

**因應措施：** 您可用來建立單一的程式庫，其中包含數個組件 （從不同的專案） 產生器。

### <a name="subclassing"></a>子類別化

.NET 內嵌簡化 Mono 執行階段應用程式內的整合藉由公開一組用於目標語言與平台已準備好可使用 Api。

例如不過這不是雙向整合，您無法子類別的 managed 類型和預期您的原生程式碼，在回撥，因為 managed 程式碼不會知道此並存的 managed 程式碼。

根據您的需求，可能會因應措施的組件的這項限制例如

* managed 程式碼可以 p/invoke 程式原生程式碼。 這需要自訂您受管理的程式碼，以允許自訂原生程式碼;

* 使用 Xamarin.iOS 等產品，並公開 （expose） 可讓 OBJECTIVE-C （在此情況下） 子類別化某些受管理的 NSObject 子類別的受管理程式庫。

## <a name="objective-c-generated-code"></a>OBJECTIVE-C 產生的程式碼

### <a name="nullability"></a>null 屬性

沒有在 告訴我們是否可接受 null 參考的.NET 或不適用於 API 中繼資料。 大部分的 Api 將會擲回`ArgumentNullException`如果無法應付`null`引數。 這是有問題，因為 OBJECTIVE-C 處理的例外狀況是較佳避免。

因為我們不能產生精確的 null 屬性註解標頭檔中，而且想要最小化的 managed 例外狀況，所以我們預設為非 null 引數 (`NS_ASSUME_NONNULL_BEGIN`)，並新增一些特定時，會有效位數、 null 屬性註釋。

### <a name="bitcode-ios"></a>Bitcode (iOS)

目前內嵌.NET 不支援 bitcode 在 iOS 上，啟用一些 Xcode 專案範本。 這必須停用已成功產生連結的架構。

![Bitcode 選項](images/ios-bitcode-option.png)
