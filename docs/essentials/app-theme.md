---
title: Xamarin.Essentials：應用程式主題
description: 本檔說明中所要求的應用程式主題 API Xamarin.Essentials ，其會提供針對執行中應用程式所要求的主題樣式資訊。
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 01/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c68f00b77f0b9f88d014334dc56e1e58ed057986
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436879"
---
# <a name="no-locxamarinessentials-app-theme"></a>Xamarin.Essentials：應用程式主題

**RequestedTheme** API 是類別的一部分 [`AppInfo`](app-information.md) ，可提供系統針對執行中應用程式所要求的主題的相關資訊。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>使用 RequestedTheme

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>取得主題資訊

您可以使用下列 API 來偵測到要求的應用程式主題：

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

這會為您的應用程式提供系統目前要求的主題。 傳回值將會是下列其中一項：

* [未指定]
* 輕量型
* 深色

當作業系統沒有要要求的特定使用者介面樣式時，將會傳回未指定的。 其中一個範例是在執行早于13.0 的 iOS 版本的裝置上。


## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

Android 使用設定模式來指定要向使用者要求的主題類型。 根據 Android 版本，使用者可以變更它，或在啟用省電模式模式時變更。

您可以閱讀深色主題的官方 [Android 檔](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme)。


# <a name="ios"></a>[iOS](#tab/ios)

在13.0 之前的 iOS 版本上，一律會傳回未指定的


# <a name="uwp"></a>[UWP](#tab/uwp)

您 `RequestedTheme` 必須在 UI 執行緒上呼叫呼叫，否則會擲回例外狀況。

UWP 應用程式會遵守您在 UWP 應用程式中的設定。 **RequestedTheme**下的 xaml。 如果設定為特定的主題， Xamarin.Essentials 一律會傳回這項設定。 若要使用 OS 的動態主題，請從您的應用程式中移除此節點，然後當您的應用程式執行時，將會在 [Windows 設定] 中傳回使用者所設定的主題 (**設定 > 個人化 > 色彩 > 選擇預設的應用程式模式**) 。

您可以在 [UWP 要求的主題檔](/uwp/api/windows.ui.xaml.application.requestedtheme)中深入瞭解。

--------------

## <a name="api"></a>API

- [AppInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/AppInfo)
- [AppInfo API 文件](xref:Xamarin.Essentials.AppInfo)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Theme-Detection-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]