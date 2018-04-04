---
title: 平台功能
description: 利用 xamarin.forms 特定平台功能
ms.prod: xamarin
ms.assetid: 2C6CE42C-E380-4BB9-90CC-D0F4E60C4C03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/20/2017
ms.openlocfilehash: 454969d8a59128423d632452033b8ba84ee0bc98
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="platform-features"></a>平台功能

Xamarin.Forms 是可延伸和可讓您使用的平台專屬功能併入[效果](~/xamarin-forms/app-fundamentals/effects/index.md)，[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)、 [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)， [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)，以及其他更多。

## <a name="androidandroidindexmd"></a>[Android](android/index.md)

本指南說明如何實作材料設計藉由更新現有 Xamarin.Forms Android 應用程式。

## <a name="application-indexing-and-deep-linkingdeep-linkingmd"></a>[應用程式編製索引和深層連結](deep-linking.md)

應用程式編製索引，可讓會後一些使用搜尋結果中出現的停留相關否則忘記的應用程式。 深層連結，可讓應用程式來瀏覽至頁面，參考從深層連結通常包含應用程式資料的搜尋結果的回應。

## <a name="device-classdevicemd"></a>[裝置類別](device.md)

如何使用`Device`類別以建立共用的程式碼和使用者介面 （包括使用 XAML） 中的平台專屬行為。 另外也涵蓋了`BeginInvokeOnMainThread`修改 UI 控制項，從背景執行緒時，這是不可或缺。

## <a name="iosiosindexmd"></a>[iOS](ios/index.md)

可透過執行一些 iOS 樣式**Info.plist**和`UIAppearance`應用程式開發介面。 本指南包含如何將 iOS 應用程式的 Xamarin.Forms 方案，包括核心 Spotlight 搜尋包含 iOS 9 功能的範例。

## <a name="macmacmd"></a>[Mac](mac.md)

Xamarin.Forms 現在具有 macOS 應用程式的預覽支援。

## <a name="native-formsnative-formsmd"></a>[原生格式](native-forms.md)

原生格式允許 Xamarin.Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-衍生頁面，以供原生 Xamarin.iOS、 Xamarin.Android 和通用 Windows 平台 (UWP) 專案。

## <a name="native-viewsnative-viewsindexmd"></a>[原生檢視](native-views/index.md)

原生 iOS、 Android 和通用 Windows 平台從檢視可以從 Xamarin.Forms 直接參考。 屬性和事件處理常式可以設定在原生的檢視，以及他們可以與互動 Xamarin.Forms 檢視。

## <a name="platform-specificsplatform-specificsindexmd"></a>[Platform-Specifics](platform-specifics/index.md)

平台特性可讓您使用才有特定的平台，而不需要自訂轉譯器或影響的功能。

## <a name="pluginspluginsmd"></a>[外掛程式](plugins.md)

有各種不同的開放原始碼外掛程式適用於 Github、 Nuget 和 Xamarin 元件存放區，協助延長 Xamarin.Forms 應用程式。

## <a name="windowswindowsindexmd"></a>[Windows](windows/index.md)

Xamarin.Forms 可支援四種不同的 Windows 專案：

* Windows Phone 8 Silverlight （原始 Windows 支援的平台 Xamarin.Forms），
* Windows Phone 8.1 (WinRT)，
* Windows 8.1 (WinRT)，以及
* 通用 Windows 平台 (Windows 10)。

本章節描述，以及如何將它們加入至現有的 Xamarin.Forms 方案之間的差異。
