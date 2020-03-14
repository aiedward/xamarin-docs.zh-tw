---
title: Xamarin. Essentials：應用程式主題
description: 本檔說明 Xamarin. Essentials 中要求的應用程式主題 API，它會提供針對執行中應用程式所要求的主題樣式資訊。
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: e31cae6ff639dbe261599a7cf78ae31fc09318b3
ms.sourcegitcommit: c83b55f60ece20e9163b3e587130250fdf113a16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/12/2020
ms.locfileid: "79190320"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin. Essentials：應用程式主題

**Application.requestedtheme** API 是[`AppInfo`](app-information.md)類別的一部分，並提供系統為執行中的應用程式要求的主題的相關資訊。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>使用 Application.requestedtheme

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>取得主題資訊

使用下列 API 可以偵測到要求的應用程式主題：

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

這會為您的應用程式提供系統目前要求的主題。 傳回值將會是下列其中一項：

* [未指定]
* 淡
* 深色

當作業系統沒有要要求的特定使用者介面樣式時，將會傳回未指定的。 其中一個範例是在執行的 iOS 版本比13.0 舊的裝置上執行。


## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

Android 使用設定模式來指定要向使用者要求的主題類型。 根據 Android 版本，使用者可以加以變更，或在啟用節電模式時變更。

若要深入瞭解，請參閱[深色主題的官方 Android 檔](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme)。


# <a name="ios"></a>[iOS](#tab/ios)

在13.0 之前的版本上，一律會傳回未指定的 


# <a name="uwp"></a>[UWP](#tab/uwp)

根據預設，您的應用程式會使用使用者在 Windows 設定 中設定的主題來執行（**設定 > 個人化 > 色彩 > 選擇您的預設應用程式模式**）。 您可以設定應用程式的 Application.requestedtheme 屬性來覆寫使用者預設值，並指定要使用的主題。

您可以在[UWP 要求的主題檔](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme)中閱讀更多。

--------------

## <a name="api"></a>API

- [AppInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文件](xref:Xamarin.Essentials.AppInfo)
