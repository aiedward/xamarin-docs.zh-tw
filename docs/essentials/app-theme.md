---
title: Xamarin.Essentials:應用程式主題
description: 此文件描述 Xamarin.Essentials 中的所要求應用程式佈景主題 API，其能提供針對執行中應用程式所要求的佈景主題樣式為何的資訊。
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 39f650a73f03888e50c6259c1052e24dbea0a4f5
ms.sourcegitcommit: ec62c7f28abc8e121656f1b93146657d90a4cab4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77555666"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin.Essentials:應用程式主題

**RequestedTheme** API 是 [AppInfo](/app-information.md) 類別的一部分，且能提供系統針對您的執行中應用程式所要求的佈景主題為何的資訊。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>使用 RequestedTheme

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>取得佈景主題資訊

可透過下列 API 來偵測所要求的應用程式佈景主題：

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

這將會提供系統針對您的應用程式目前所要求的佈景主題。 傳回值將會是下列其中一個：

* 未指定
* 亮色調
* 暗色調

如果作業系統沒有特定的使用者介面樣式可供要求，將會傳回「未指定」。 此情況的其中一個範例，是執行早於 13.0 之 iOS 版本的裝置。


## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

Android 會使用設定模式來指定要向使用者要求的佈景主題類型。 根據 Android 的版本而定，其可能會由使用者變更，或是在啟用節約耗電量模式時變更。

您可以在官方的[深色佈景主題 Android 文件](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme) \(英文\) 中深入閱讀。


# <a name="ios"></a>[iOS](#tab/ios)

針對早於 13.0 的 iOS 版本，將一律會傳回「未指定」 


# <a name="uwp"></a>[UWP](#tab/uwp)

根據預設，您的應用程式會使用由使用者在 Windows 設定 ([設定] > [個人化] > [色彩] > [選擇您的預設應用程式模式]) 中所設定的佈景主題來執行。 您可以設定應用程式的 RequestedTheme 屬性來覆寫使用者預設值，並指定要使用哪一個佈景主題。

您可以在 [UWP 所要求佈景主題文件](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme) \(英文\) 中深入閱讀。

--------------

## <a name="api"></a>API

- [AppInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文件](xref:Xamarin.Essentials.AppInfo)
