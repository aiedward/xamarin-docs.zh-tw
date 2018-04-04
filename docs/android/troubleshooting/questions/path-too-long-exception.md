---
title: 如何解決 PathTooLongException 錯誤？
description: 本文說明如何解決 PathTooLongException 建置應用程式時可能發生的。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/20/2018
ms.openlocfilehash: f50ca3e738cb781f9c80e83f58f2e0fa1fa8e113
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>如何解決 PathTooLongException 錯誤？

## <a name="cause"></a>原因

Xamarin.Android 專案中產生的路徑名稱可能相當長。
比方說，無法在建置期間產生的路徑，如下所示：

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

在 Windows 上 (其中一個路徑的最大長度是[260 個字元](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx))、 **PathTooLongException**無法建置專案，如果產生的路徑超過最大長度時產生。 

## <a name="fix"></a>修正

開頭為 Xamarin.Android 8.0 `UseShortFileNames` MSBuild 屬性可以設定為規避這項錯誤。 當這個屬性設定為`True`(預設值是`False`)，建置程序會使用較短的路徑名稱來減少產生的可能性**PathTooLongException**。
例如，當`UseShortFileNames`設`True`，上述的路徑會縮短成類似於下列路徑：

**C:\\某些\\目錄\\方案\\專案\\obj\\偵錯\\lp\\1\\jl\\資產**

若要設定這個屬性，將下列 MSBuild 屬性加入至專案**.csproj**檔案：

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

如需設定建置屬性的詳細資訊，請參閱[建置流程](~/android/deploy-test/building-apps/build-process.md)。
