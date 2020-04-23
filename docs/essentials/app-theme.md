---
title: Xamarin.要點:應用主題
description: 本文件介紹 Xamarin 中請求的應用主題 API。要點,它提供有關正在運行的應用請求的主題樣式的資訊。
ms.assetid: F6F6D496-A8A9-4B9A-AF1A-370D937E5073
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: f322855f26d7a57acc06e97e0c97ab201c3fa586
ms.sourcegitcommit: a9280318bf7bb69e4e5744ee739e76a9cba36b28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82047396"
---
# <a name="xamarinessentials-app-theme"></a>Xamarin.要點:應用主題

**請求主題**API 是類[`AppInfo`](app-information.md)的一部分,提供有關系統為正在運行的應用請求的主題的資訊。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-requestedtheme"></a>使用要求的主題

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

## <a name="obtaining-theme-information"></a>取得主題資訊

可以使用以下 API 偵測要求的應用程式主題:

```csharp
AppTheme appTheme = AppInfo.RequestedTheme;

```

這將為應用程式提供系統當前請求的主題。 傳回值將是以下值之一:

* [未指定]
* 淡
* 深色

當操作系統沒有要請求的特定使用者界面樣式時,將返回未指定的使用者名。 例如,在運行版本超過 13.0 的 iOS 的設備上。


## <a name="platform-implementation-specifics"></a>平台實作特性

# <a name="android"></a>[Android](#tab/android)

Android 使用配置模式指定要向使用者請求的主題類型。 根據 Android 版本,用戶可以更改該版本,或在啟用節電模式時更改。

你可以閱讀更多關於[官方Android文檔的黑暗主題](https://developer.android.com/guide/topics/ui/look-and-feel/darktheme)。


# <a name="ios"></a>[iOS](#tab/ios)

未指定的版本將始終返回早於 13.0 的 iOS 版本 


# <a name="uwp"></a>[UWP](#tab/uwp)

UWP 應用程式將尊重您在 UWP App.xaml 下 **"請求主題**"中的設置。 如果它設置為特定主題,Xamarin.基本將始終返回此設置。 要使用作業系統的動態主題從應用程式中刪除此節點,然後當應用運行時,它將返回使用者在 Windows 設置中設置的主題(**設置>個人化>顏色>選擇預設應用模式**)。

您可以閱讀更多關於[UWP 要求的主題文件](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.requestedtheme)。

--------------

## <a name="api"></a>API

- [AppInfo 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/AppInfo)
- [AppInfo API 文件](xref:Xamarin.Essentials.AppInfo)
