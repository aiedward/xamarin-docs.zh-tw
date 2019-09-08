---
title: Apple 平臺（iOS 和 Mac）
description: 本檔描述與 Xamarin 和 Xamarin 開發相關的各種主題：程式碼共用、Unified API、系結目標-C 程式庫、原生參考、原生類型等等。
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 10ab9b379344ab6c514eba84f1ef3fd9c7400b73
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765540"
---
# <a name="apple-platform-ios-and-mac"></a>Apple 平臺（iOS 和 Mac）

## <a name="code-sharing"></a>程式碼共用

對於沒有使用者介面專案的程式碼元素，在 iOS 和 Mac 之間共用程式碼的最佳方式仍然是使用[可移植的類別庫](~/cross-platform/app-fundamentals/pcl.md)。

對於必須執行一些使用者介面工作的程式碼，您想要共用，您應該使用[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)，讓您將程式碼放在單一專案中共用，並在參考時以 Mac 和 iOS 進行編譯。

## <a name="unified-apiunifiedindexmd"></a>[Unified API](unified/index.md)

IOS 和 Mac 專案的 Unified API 針對架構使用相同的命名空間，因此相同的程式碼檔案可以跨兩個平臺使用，以進行順暢的程式碼共用。 它也會同時啟用32和64位組建。 Unified API 是自2015年初起的範本預設值，建議用於所有新的專案-*僅*Unified API 專案可以提交至 App Store。

### <a name="classic-apis"></a>傳統 Api

> [!NOTE]
> **傳統設定檔淘汰：** 當 Xamarin 中新增了新的平臺時，我們就開始從傳統設定檔（monotouch）逐漸取代功能。 例如，已移除 [非 NRC （新的-參考計數）] 選項。 所有整合的應用程式（也就是非 NRC 的選項）都已啟用 NRC，而且沒有任何已知問題。 未來的版本將會移除使用 Boehm 做為垃圾收集行程的選項。 這也是不適用於整合應用程式的選項。 傳統支援的完整移除已排程于2016版與 Xamarin. iOS 10.0。

原始的（非統一） Xamarin iOS 和 Xamarin api 使程式碼共用變得更棘手，因為原生架構具有`MonoTouch.`或`MonoMac.`命名空間前置詞。  我們提供了一些空的命名空間，可讓開發人員`using`藉由在相同檔案上加入參考 MonoMac 和 MonoTouch 命名空間的語句來共用程式碼，但這有點不太容易。 Classic API 應該只會繼續用於內部散發的繼承應用程式（建議升級至 Unified API）。

### <a name="updating-from-classic-to-the-unified-api"></a>從傳統更新到 Unified API

有詳細的指示可將應用程式從傳統更新為 Unified API。

## <a name="binding-objective-c-librariesbindingindexmd"></a>[繫結 Objective-C 程式庫](binding/index.md)

Xamarin 可讓您將原生程式庫帶入具有系結的應用程式。 本節說明：

- 系結的操作方式，
- 如何手動建立系結專案，讓您將目標 C 程式碼帶入 Xamarin，以及
- 如何使用我們的**目標 Sharpie**工具協助自動化程式。

## <a name="native-referencesnative-referencesmd"></a>[原生參考](native-references.md)

## <a name="macios-native-typesnativetypesmd"></a>[Mac/iOS 原生類型](nativetypes.md)

為了從和C# F#以透明的方式支援32和64位的程式碼，我們引進了新的資料類型。   在這裡深入瞭解。

## <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[建立32和64位應用程式](32-and-64/index.md)

支援32和64位應用程式所需知道的事項。

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[在跨平台應用程式中使用原生型別](native-types-cross-platform.md)

本文說明如何在跨平臺應用程式中使用新`nint`的`nuint`iOS `nfloat`Unified API 原生類型（、、），其中的程式碼會與非 iOS 裝置（例如 Android 或 Windows Phone os）共用。
它可讓您深入瞭解原生類型的使用時機，並提供數個可能的解決方案，以便在必須搭配跨平臺程式碼使用新類型的情況下使用。

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[HttpClient 堆疊和 SSL/TLS 實作選擇器](http-stack.md)

新的 HttpClient 堆疊選取器可控制要在您的 tvOS 和 Xamarin 應用程式中使用的 HttpClient 執行。 您現在可以切換至使用 iOS、tvOS 或 os X 的原生傳輸（`NSUrlSession`或`CFNetwork`視作業系統而定）的執行。

SSL （安全通訊端層）及其後續的 TLS （傳輸層安全性），透過提供對 HTTP 和其他網路`System.Net.Security.SslStream`連線的支援。 新的 SSL/TLS 組建選項會切換 Mono 本身的 TLS 堆疊，以及 Mac 和 iOS 中的 Apple TLS 堆疊所提供的一個。
