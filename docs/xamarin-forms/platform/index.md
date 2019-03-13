---
title: Xamarin.Forms 的平台功能
description: 本指南說明如何利用平台專屬功能中的 Xamarin.Forms 應用程式使用各種技術。
ms.prod: xamarin
ms.assetid: 2C6CE42C-E380-4BB9-90CC-D0F4E60C4C03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: 3f0156926f8d7a31e2e80318d7b05a909f158653
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207722"
---
# <a name="xamarinforms-platform-features"></a>Xamarin.Forms 的平台功能

Xamarin.Forms 是可延伸，並讓您使用的平台專屬功能納入[效果](~/xamarin-forms/app-fundamentals/effects/index.md)，[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)，則[DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)， [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)，等等。

## <a name="androidandroidindexmd"></a>[Android](android/index.md)

本指南說明提供 Xamarin.Forms，以及如何藉由更新現有的 Xamarin.Forms Android 應用程式中實作 Material Design Android 平台特性。

## <a name="device-classdevicemd"></a>[裝置類別](device.md)

如何使用`Device`類別來建立共用的程式碼和使用者介面 （包括使用 XAML） 中的平台專屬行為。 也涵蓋`BeginInvokeOnMainThread`修改 UI 控制項，從背景執行緒時，這是不可或缺。

## <a name="iosiosindexmd"></a>[iOS](ios/index.md)

本指南說明 Xamarin.Forms 和如何執行透過設定樣式的其他 iOS 所提供的 iOS 平台特性**Info.plist**而`UIAppearance`API。

## <a name="native-formsnative-formsmd"></a>[原生格式](native-forms.md)

原生格式允許 Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-衍生原生的 Xamarin.iOS、 Xamarin.Android 和通用 Windows 平台 (UWP) 專案所使用的頁面。

## <a name="native-viewsnative-viewsindexmd"></a>[原生檢視](native-views/index.md)

從 iOS、 Android 和通用 Windows 平台的原生檢視可以從 Xamarin.Forms 直接參考。 原生的檢視，可以設定屬性和事件處理常式，它們可以與 Xamarin.Forms 檢視互動。

## <a name="platform-specificsplatform-specificsindexmd"></a>[平台特性](platform-specifics/index.md)

平台特性可讓您使用的功能只可在特定的平台，而不需要自訂轉譯器或影響。 此外，廠商也可以建立自己的平台特性，會有的效果。

## <a name="windowswindowsindexmd"></a>[Windows](windows/index.md)

本指南說明 Windows 平台特性 Xamarin.Forms，以及如何將 UWP 專案新增至現有的 Xamarin.Forms 方案所提供。
