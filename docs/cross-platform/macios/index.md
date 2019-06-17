---
title: Apple 平台 （iOS 和 Mac）
description: 本文件說明 Xamarin.iOS 和 Xamarin.Mac 開發相關的各種主題： 程式碼共用、 統一的 API、 繫結 Objective C 程式庫、 原生參考、 原生類型，和更多功能。
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: b40758fa562e57415cd3c0818763ef0a7ce5dcca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199763"
---
# <a name="apple-platform-ios-and-mac"></a>Apple 平台 （iOS 和 Mac）

## <a name="code-sharing"></a>共用程式碼

您的程式碼不有任何使用者介面項目共用的最佳方式的項目 iOS 與 Mac 之間的程式碼仍是使用[可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)。

必須執行一些工作，使用者介面的程式碼和您想要共用的您應該使用[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)可讓您放入單一專案中的共用，並讓它使用 Mac 和 iOS 參考時所編譯的程式碼。

##  <a name="unified-apiunifiedindexmd"></a>[Unified API](unified/index.md)

統一 API 適用於 iOS 和 Mac 專案架構使用相同的命名空間，使相同的程式碼檔案可以用於跨這兩個平台，以無縫式的程式碼共用。 它也可讓 32 和 64 位元的組建。 統一的 API 2015 年初，因為已範本預設值，而且建議所有新專案-*只*Unified API 專案可以提交至 App Store。

### <a name="classic-apis"></a>傳統的 Api

> [!NOTE]
> **傳統的設定檔已被取代：** 在 Xamarin.iOS 中加入新的平台時我們將開始逐漸淘汰功能從傳統的設定檔 (monotouch.dll)。 例如，已移除 [非 NRC （新-ref-計數）] 選項。 NRC 一律已啟用所有整合的應用程式 （亦即非 NRC 從未選項） 且沒有已知的問題。 未來版本將移除使用 Boehm 記憶體回收行程的選項。 這也是永遠不會提供統一的應用程式的選項。 完成移除傳統的支援已排程進行 Xamarin.iOS 10.0 版的 fall 2016。

原始 （非統一的） 的 Xamarin.iOS 和 Xamarin.Mac Api 進行程式碼共用更困難因為原生架構有其中一個`MonoTouch.`或`MonoMac.`命名空間前置詞。  我們提供可讓開發人員共用程式碼，加上一些空白命名空間`using`陳述式參考 MonoMac 和 MonoTouch 命名空間，在相同的檔案，但這是有點麻煩。 傳統的 API 只應該繼續使用舊版內部散發的應用程式中 （升級至 Unified API 建議使用）。


### <a name="updating-from-classic-to-the-unified-api"></a>從傳統更新至 Unified API

沒有更新任何從傳統至統一 API 的應用程式的詳細的指示。

## <a name="binding-objective-c-librariesbindingindexmd"></a>[繫結 Objective-C 程式庫](binding/index.md)

Xamarin 可讓您帶入您的應用程式中的原生程式庫，以繫結。 本章節將說明：

- 繫結的運作方式，
- 如何手動建立一個可讓您將 OBJECTIVE-C 程式碼帶入 Xamarin 的繫結專案及
- 如何使用我們**目標 Sharpie**工具，可協助自動化程序。

## <a name="native-referencesnative-referencesmd"></a>[原生參考](native-references.md)

##  <a name="macios-native-typesnativetypesmd"></a>[Mac/iOS 原生類型](nativetypes.md)

若要支援 32 和 64 位元程式碼，以透明的方式從C#和F#，我們引進了新的資料類型。   這裡了解這些。

##  <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[建置 32 位元及 64 位元應用程式](32-and-64/index.md)

您需要知道支援 32 和 64 位元應用程式。

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[在跨平台應用程式中使用原生型別](native-types-cross-platform.md)

這篇文章將說明如何使用新的 iOS Unified API 原生型別 (`nint`， `nuint`， `nfloat`) 中使用非 iOS 裝置，例如 Android 或 Windows Phone 作業系統，其中共用程式碼的跨平台應用程式。
它提供深入了解何時應該使用的原生類型，並提供數個可能的解決方案，可使用跨平台程式碼必須的使用新的類型。

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[HttpClient 堆疊和 SSL/TLS 實作選擇器](http-stack.md)

新的 HttpClient 堆疊選取器控制使用 Xamarin.iOS Xamarin.tvOS，Xamarin.Mac 應用程式中的 HttpClient 實作。 您現在可以切換到使用 iOS 的實作，tvOS 的或 OS X 的原生傳輸 (`NSUrlSession`或`CFNetwork`視作業系統而定)。

SSL （安全通訊端層） 和及其後繼者 TLS （傳輸層安全性），提供支援 HTTP 及其他網路連線，透過`System.Net.Security.SslStream`。 新的 SSL/TLS 實作組建選項切換為 Mono 自己的 TLS 堆疊，並由出現在 Mac 和 iOS 的 Apple 的 TLS 堆疊的一個。
