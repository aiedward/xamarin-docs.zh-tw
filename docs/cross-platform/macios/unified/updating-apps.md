---
title: 將現有的應用程式更新至 Unified API
description: 說明如何更新至 Unified API 的 Xamarin 應用程式的各種指南的這個文件連結。 它討論 Xamarin.iOS 應用程式，Xamarin.Mac 應用程式。 跨平台應用程式和繫結專案中的原生類型的 Xamarin.Forms 應用程式。
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 7730d19e4f261a0e414e4946fd0add5312f5d030
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864366"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>將現有的應用程式更新至 Unified API

> [!IMPORTANT]
> Xamarin 傳統 API，前面有統一的 API，已被取代。
> - Xamarin.iOS 支援傳統的 API (monotouch.dll) 的最後一個版本是 Xamarin.iOS 9.10。
> - Xamarin.Mac 仍支援傳統的 API，但它不會再更新。 因為它已被取代，開發人員應該將移至統一的 API 應用程式。

## <a name="how-to-update-your-apps"></a>如何更新您的應用程式

有三個步驟來更新您的應用程式：

1. 修正任何編譯器警告中現有的程式碼，特別是那些有關已被取代的 Api。

2. 若要更新您的專案檔和命名空間中使用 Visual Studio for Mac 內建移轉工具。

3. 剩餘的相關新的編譯器錯誤修正[64-型別](~/cross-platform/macios/nativetypes.md)並[其他 Api](~/cross-platform/macios/unified/overview.md#deprecated-typos)變更過的。 請參閱[祕訣](~/cross-platform/macios/unified/updating-tips.md)如需手動更新可能需要其他資訊。

有適用於每個產品，可協助您更新至 Unified API 和 64 位元支援的應用程式的特定指南：

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Xamarin.iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)

可以更新現有的 Xamarin.iOS 應用程式，以統一的 API 使用自動化的移轉工具內建於 Visual Studio for mac。 一些其他的修正程式則可能是必要的如所述[這些指示](~/cross-platform/macios/unified/updating-ios-apps.md)並[秘訣](~/cross-platform/macios/unified/updating-tips.md)。

### <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Xamarin.Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)

可以更新現有的 Xamarin.Mac 應用程式，以統一的 API 使用自動化的移轉工具內建於 Visual Studio for mac。 一些其他的修正程式則可能是必要的如所述[這些指示](~/cross-platform/macios/unified/updating-mac-apps.md)並[秘訣](~/cross-platform/macios/unified/updating-tips.md)。

### <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Xamarin.Forms 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

請遵循這些指示來更新現有的 Xamarin.Forms 方案 iOS 專案時要使用統一的 API。 統一的 API 支援只是用於 Xamarin.Forms 1.3 和更新版本，因此[指示](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)也說明如何更新為版本 1.3 的 Xamarin.Forms 應用程式。 這些[秘訣](~/cross-platform/macios/unified/updating-tips.md)有助於更新中自訂轉譯器或相依性服務的任何原生 iOS 程式碼。

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[在跨平台應用程式中使用原生型別](~/cross-platform/macios/nativetypes.md)

這篇文章說明如何使用新的 iOS Unified API 原生型別 （nint、 nuint nfloat） 跨平台應用程式中使用非 iOS 裝置，例如 Android 或 Windows Phone 作業系統，其中共用程式碼。 它提供深入了解何時應該使用的原生類型，並提供數個可能的解決方案，可使用跨平台程式碼必須的使用新的類型。

## <a name="update-bindings-to-the-unified-api"></a>更新繫結至 Unified API

已建立繫結 Objective C 程式庫的客戶必須更新以反映變更基礎的 API （其中有些類型現在會是 64 位元） 的繫結專案。
請遵循下列指示[更新現有繫結的專案以支援統一的 API](~/cross-platform/macios/unified/update-binding.md)。

## <a name="related-links"></a>相關連結

- [更新 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin.Forms 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新繫結](~/cross-platform/macios/unified/update-binding.md)
- [更新祕訣](~/cross-platform/macios/unified/updating-tips.md)
- [傳統的 vs 統一的 API 差異](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
