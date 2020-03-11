---
title: 如何? 解決 PathTooLongException 錯誤？
description: 本文說明如何解決建立應用程式時可能發生的 PathTooLongException。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: ffe88546ff58387865d71268bd64ec05c8aec3c5
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73026795"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>如何? 解決 PathTooLongException 錯誤？

## <a name="cause"></a>原因

在 Xamarin Android 專案中產生的路徑名稱可能相當長。
例如，在組建期間可能會產生類似下列的路徑：

**C：\\部分\\目錄\\方案\\Project\\obj\\__Debug\\library_projects\\\\__ 資產 library_project_imports**\\

在 Windows 上（路徑的最大長度為[260 個字元](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)），如果產生的路徑超過最大長度，則會在建立專案時產生**PathTooLongException** 。 

## <a name="fix"></a>修正

根據預設，`UseShortFileNames` MSBuild 屬性設定為 `True` 以規避此錯誤。 當此屬性設定為 `True`時，組建程式會使用較短的路徑名稱，以降低產生**PathTooLongException**的可能性。
例如，當 `UseShortFileNames` 設定為 `True`時，上述路徑會縮短為類似下列的路徑：

**C：\\一些\\目錄\\方案\\Project\\obj\\Debug\\lp\\1\\jl\\資產**

若要手動設定此屬性，請將下列 MSBuild 屬性加入至專案 **.csproj**檔案：

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

如果設定此旗標無法修正**PathTooLongException**錯誤，另一種方法是在專案 **.csproj**檔案中設定 `IntermediateOutputPath`，為方案中的專案指定[通用的中繼輸出根](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/)。 嘗試使用相對較短的路徑。 例如：

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

如需設定組建屬性的詳細資訊，請參閱[建立進程](~/android/deploy-test/building-apps/build-process.md)。
