---
title: "統一的 API 來更新現有的應用程式"
ms.topic: article
ms.prod: xamarin
ms.assetid: 8A654C95-5DCA-4BB5-A582-F96C2BECC81C
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: b1b6338494b9be98e677cf9d338410eae759feb8
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="updating-existing-apps-to-the-unified-api"></a>統一的 API 來更新現有的應用程式

> [!IMPORTANT]
> **傳統的設定檔已被取代：** Xamarin.iOS 中加入新的平台時我們開始逐漸淘汰它們從傳統的設定檔 (monotouch.dll) 的功能。 例如，非 NRC （新 ref-計數） 選項已經移除。 NRC 一律已啟用所有整合的應用程式 （亦即非 NRC 從未選項），並沒有已知的問題。 未來版本將移除使用 Boehm 為記憶體回收行程的選項。 這也是整合的應用程式永遠不會使用的選項。 Xamarin.iOS 10.0 版的下一步 改為排定的傳統支援完整移除。




## <a name="how-to-update-your-apps"></a>如何更新您的應用程式

Xamarin 大學上具有的免費視訊**升級至 iOS 統一的 API**。 請瀏覽[Xamarin 大學閃電演講](http://university.xamarin.com/lightninglectures)監看 ！

[ ![](updating-apps-images/xamu-video-sml.png "Xamarin 大學")](http://university.xamarin.com/lightninglectures)

有三個步驟來更新您的應用程式：

1. 修正任何編譯器警告中現有的程式碼，特別是有關已被取代的 Api。

2. 若要更新您的專案檔和命名空間中使用內建於 Visual Studio for Mac 移轉工具。

3. 剩餘的與新的編譯器錯誤修正[64 類型](~/cross-platform/macios/nativetypes.md)和[其他 Api](~/cross-platform/macios/unified/index.md#deprecated-typos)變更過的。 簽出[這些秘訣](~/cross-platform/macios/unified/updating-tips.md)如需有關可能需要手動更新。

有可用來協助您更新您的應用程式的統一的 API 和 64 位元支援的每項產品的特定指南：

### <a name="xamarinios-appscross-platformmaciosunifiedupdating-ios-appsmd"></a>[Xamarin.iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)

現有 Xamarin.iOS 應用程式可以更新以統一的 API 使用自動化的移轉工具內建於 Visual Studio for mac。 某些其他修正然後，可能需要述[這些指示](~/cross-platform/macios/unified/updating-ios-apps.md)和[秘訣](~/cross-platform/macios/unified/updating-tips.md)。

###  <a name="xamarinmac-appscross-platformmaciosunifiedupdating-mac-appsmd"></a>[Xamarin.Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)

現有 Xamarin.Mac 應用程式可以更新以統一的 API 使用自動化的移轉工具內建於 Visual Studio for mac。 某些其他修正然後，可能需要述[這些指示](~/cross-platform/macios/unified/updating-mac-apps.md)和[秘訣](~/cross-platform/macios/unified/updating-tips.md)。

###  <a name="xamarinforms-appscross-platformmaciosunifiedupdating-xamarin-forms-appsmd"></a>[Xamarin.Forms 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)

請遵循這些指示來更新現有的 Xamarin.Forms 方案與使用統一的 API 的 iOS 專案。 統一的 API 支援僅用於 Xamarin.Forms 1.3 和更新版本，因此[指示](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)也說明如何更新的版本 1.3 Xamarin.Forms 應用程式。 這些[秘訣](~/cross-platform/macios/unified/updating-tips.md)可協助更新任何自訂轉譯器或相依性服務中的原生 iOS 程式碼。

## <a name="working-with-native-types-in-cross-platform-appscross-platformmaciosnativetypesmd"></a>[使用跨平台應用程式中的原生類型](~/cross-platform/macios/nativetypes.md)

本文說明如何使用新的 iOS 原生整合應用程式開發介面的型別 （nint、 nuint、 nfloat） 跨平台應用程式中使用非 iOS 裝置，例如 Android 或 Windows Phone 作業系統版本之共用程式碼的位置。 它提供深入了解何時應該使用的原生類型，並提供以跨平台程式碼必須使用新類型的情況下幾個可能的解決方案。

## <a name="update-bindings-to-the-unified-api"></a>統一的 API 來更新繫結

建立繫結至 Objective C 程式庫的客戶必須更新以反映變更基礎的 API （其中有些類型現在會是 64 位元） 的繫結專案。
請遵循下列指示[更新現有繫結的專案以支援統一的 API](~/cross-platform/macios/unified/update-binding.md)。




## <a name="related-links"></a>相關連結

- [更新的 iOS 應用程式](~/cross-platform/macios/unified/updating-ios-apps.md)
- [更新 Mac 應用程式](~/cross-platform/macios/unified/updating-mac-apps.md)
- [更新 Xamarin.Forms 應用程式](~/cross-platform/macios/unified/updating-xamarin-forms-apps.md)
- [更新繫結](~/cross-platform/macios/unified/update-binding.md)
- [更新的秘訣](~/cross-platform/macios/unified/updating-tips.md)
- [傳統的 vs 統一的 API 差異](http://developer.xamarin.comhttps://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
