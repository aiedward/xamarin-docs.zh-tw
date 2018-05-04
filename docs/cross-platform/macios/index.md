---
title: Apple 平台 （iOS 和 Mac）
description: 本節中，我們會討論 Xamarin.iOS 和 Xamarin.Mac 專案之間共用程式碼的策略。
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: a842e89b74419a03e4ea8f4355162960d9109f9b
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2018
---
# <a name="apple-platform-ios-and-mac"></a>Apple 平台 （iOS 和 Mac）

_本節中，我們會討論 Xamarin.iOS 和 Xamarin.Mac 專案之間共用程式碼的策略。_

## <a name="code-sharing"></a>共用程式碼

如不有任何使用者介面項目，最好来共用您的程式碼項目 iOS 與 Mac 之間的程式碼仍在使用[可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)。

必須執行一些工作，使用者介面的程式碼和您想要共用的棒的是，您應該使用[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)可讓您將共用單一專案中，並讓它使用 Mac 和 iOS 參考時編譯的程式碼。

##  <a name="unified-apiunifiedindexmd"></a>[Unified API](unified/index.md)

統一的 API，針對 iOS 和 Mac 專案會使用相同的命名空間的架構，使相同的程式碼檔案可以跨越兩個平台，用於以無縫式的程式碼共用。 它也可讓 32 和 64 位元組建。 統一的 API 自 2015 年早期，已預設範本，而且建議所有新專案-*只*統一的 API 專案可以提交至應用程式市集。

### <a name="classic-apis"></a>傳統應用程式開發介面

> [!NOTE]
> **傳統的設定檔已被取代：** Xamarin.iOS 中加入新的平台時我們開始逐漸淘汰它們從傳統的設定檔 (monotouch.dll) 的功能。 例如，非 NRC （新 ref-計數） 選項已經移除。 NRC 一律已啟用所有整合的應用程式 （亦即非 NRC 從未選項），並沒有已知的問題。 未來版本將移除使用 Boehm 為記憶體回收行程的選項。 這也是整合的應用程式永遠不會使用的選項。 支援的傳統完整移除已排定要改 2016 Xamarin.iOS 10.0 版。

原始的 （非統一） Xamarin.iOS 和 Xamarin.Mac Api 進行程式碼共用更加困難因為原生架構 `MonoTouch.`或`MonoMac.`命名空間前置詞。  我們提供一些可讓開發人員共用的程式碼加入的空白命名空間`using`陳述式參考 MonoMac 和 MonoTouch 命名空間上相同的檔案，但這是有點麻煩。 傳統應用程式開發介面只應該繼續以用於內部散發的舊版應用程式 （升級至統一的 API 建議選項）。


### <a name="updating-from-classic-to-the-unified-api"></a>從傳統更新統一的 api

有更新統一的 API 透過傳統的任何應用程式的詳細的指示。

## <a name="binding-objective-c-librariesbindingindexmd"></a>[繫結 Objective-C 程式庫](binding/index.md)

Xamarin 可讓您帶入您的應用程式中的原生程式庫，以繫結。 本章節將說明：

- 繫結的運作方式，
- 如何手動建立繫結的專案，可讓您帶入 Xamarin，Objective C 程式碼和
- 如何使用我們**目標 Sharpie**工具，以協助自動化的程序。

## <a name="native-referencesnative-referencesmd"></a>[原生參考](native-references.md)



##  <a name="macios-native-typesnativetypesmd"></a>[Mac/iOS 原生類型](nativetypes.md)

為了支援 32 和 64 位元會明確地從 C# 和 F # 的程式碼，我們引進新的資料類型。   資訊，請在這裡了解。

##  <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[建置 32 和 64 位元應用程式](32-and-64/index.md)

您需要知道支援 32 和 64 位元應用程式。

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[在跨平台應用程式中使用原生型別](native-types-cross-platform.md)

本文將說明如何使用新的 iOS 原生整合應用程式開發介面的型別 (`nint`， `nuint`， `nfloat`) 在跨平台應用程式中使用非 iOS 裝置，例如 Android 或 Windows Phone 作業系統版本之共用程式碼的位置。
它提供深入了解何時應該使用的原生類型，並提供以跨平台程式碼必須使用新類型的情況下幾個可能的解決方案。


## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[HttpClient 堆疊和 SSL/TLS 實作選擇器](http-stack.md)

新的 HttpClient 堆疊選取器控制 HttpClient 實作 Xamarin.iOS、 Xamarin.tvOS 和 Xamarin.Mac 應用程式中使用。 您現在可以切換到使用 iOS 的實作，tvOS 的或 OS X 原生傳輸 (`NSUrlSession`或`CFNetwork`視作業系統而定)。

SSL （安全通訊端層） 和 TLS （傳輸層安全性），後續提供支援 HTTP 和其他網路連線，透過`System.Net.Security.SslStream`。 新的 SSL/TLS 實作建置選項切換 Mono 自己 TLS 堆疊和一個由存在於 Mac 和 iOS 的 Apple 的 TLS 堆疊。
