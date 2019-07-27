---
title: 將現有的應用程式更新至 Unified API
description: '本檔連結的各種指南, 說明如何將 Xamarin 應用程式更新為 Unified API。 其中討論了 Xamarin. iOS 應用程式、Xamarin. Mac 應用程式。 Xamarin: Forms 應用程式、跨平臺應用程式中的原生類型, 以及系結專案。'
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 61562eed8867b7a2e12b3d2e96feed28d957696b
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511208"
---
# <a name="updating-existing-apps-to-the-unified-api"></a>將現有的應用程式更新至 Unified API

> [!IMPORTANT]
> 在 Unified API 之前的 Xamarin Classic API 已被取代。
> - 支援 Classic API (monotouch) 的最新版本是 Xamarin. iOS 9.10。
> - Xamarin 仍然支援 Classic API, 但不會再更新。 由於它已被取代, 因此開發人員應該將他們的應用程式移至 Unified API。

## <a name="how-to-update-your-apps"></a>如何更新您的應用程式

更新您的應用程式有三個步驟:

1. 修正現有程式碼中的任何編譯器警告, 特別是與已淘汰之 Api 相關的警告。

2. 使用內建的遷移工具來 Visual Studio for Mac, 以更新您的專案檔案和命名空間。

3. 修正與新的[64 類型](~/cross-platform/macios/nativetypes.md)及其他已變更之 api 相關的[其他](~/cross-platform/macios/unified/overview.md#deprecated-typos)編譯器錯誤。 請查看[這些秘訣](~/cross-platform/macios/unified/updating-tips.md), 以取得可能需要之手動更新的其他資訊。

每項產品都有特定的指南, 可協助您將應用程式更新為 Unified API 和64位支援:

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Xamarin iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)

現有的 Xamarin iOS 應用程式可以使用內建于 Visual Studio for Mac 的自動化遷移工具, 更新為 Unified API。 接著, 可能需要一些額外的修正, 如[這些指示](~/cross-platform/macios/unified/updating-ios-apps.md)和[秘訣](~/cross-platform/macios/unified/updating-tips.md)中所述。

### <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Xamarin. Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)

您可以使用內建 Visual Studio for Mac 的自動化遷移工具, 將現有的 Xamarin 應用程式更新為 Unified API。 接著, 可能需要一些額外的修正, 如[這些指示](~/cross-platform/macios/unified/updating-mac-apps.md)和[秘訣](~/cross-platform/macios/unified/updating-tips.md)中所述。

### <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Xamarin. Forms 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

請遵循這些指示, 使用 iOS 專案來更新現有的 Xamarin Forms 解決方案, 以使用 Unified API。 Unified API 支援僅適用于 Xamarin. Forms 1.3 和更新版本, 因此[指示](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)也會說明如何將您的 Xamarin. forms 應用程式更新至1.3 版。 這些[提示](~/cross-platform/macios/unified/updating-tips.md)可協助更新自訂轉譯器或相依性服務中的任何原生 iOS 程式碼。

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[在跨平台應用程式中使用原生型別](~/cross-platform/macios/nativetypes.md)

本文說明如何在跨平臺應用程式中使用新的 iOS Unified API 原生類型 (nint、nuint、nfloat), 其中程式碼與非 iOS 裝置 (例如 Android 或 Windows Phone Os) 共用。 它可讓您深入瞭解原生類型的使用時機, 並提供數個可能的解決方案, 以便在必須搭配跨平臺程式碼使用新類型的情況下使用。

## <a name="update-bindings-to-the-unified-api"></a>更新 Unified API 的系結

已建立對目標 C 程式庫之系結的客戶必須更新系結專案, 以反映基礎 API 中的變更 (其中有些類型現在會是64位)。
請遵循這些指示來[更新現有的系結專案, 以支援 Unified API](~/cross-platform/macios/unified/update-binding.md)。

## <a name="related-links"></a>相關連結

- [更新 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin. Forms 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新系結](~/cross-platform/macios/unified/update-binding.md)
- [更新秘訣](~/cross-platform/macios/unified/updating-tips.md)
- [傳統與 Unified API 差異](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
