---
title: 如何? 解決 PathTooLongException 錯誤？
description: 本文說明如何解決在建立應用程式時可能發生的 PathTooLongException。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: d58cb676b347caac00c39a381de94954219d1865
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456856"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>如何? 解決 PathTooLongException 錯誤？

## <a name="cause"></a>原因

Xamarin. Android 專案中產生的路徑名稱可能很長。
例如，在組建期間可能會產生如下的路徑：

**C： \\ 某些 \\ 目錄 \\ 方案 \\ 專案 \\ obj \\ Debug \\ __library_projects__ \\ Xamarin \\ library_project_imports \\ 資產**

在 Windows (路徑的最大長度是 [260 個字元](/windows/win32/fileio/naming-a-file)) 中，如果產生的路徑超過長度上限，則在建立專案時可能會產生 **PathTooLongException** 。 

## <a name="fix"></a>修正

`UseShortFileNames`根據預設，MSBuild 屬性會設定為 `True` 以規避此錯誤。 當這個屬性設定為時 `True` ，組建進程會使用較短的路徑名稱，以降低產生 **PathTooLongException**的可能性。
例如，當 `UseShortFileNames` 設定為時，會將 `True` 上述路徑縮寫為類似如下的路徑：

**C： \\ 某些 \\ 目錄 \\ 方案 \\ 專案 \\ obj \\ Debug \\ lp \\ 1 \\ jl \\ 資產**

若要手動設定此屬性，請將下列 MSBuild 屬性加入至專案 **.csproj** 檔案：

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

如果設定此旗標無法修正**PathTooLongException**錯誤，另一個方法是在專案 .csproj 檔案中設定，以針對方案中的專案指定[通用的中繼輸出根](/archive/blogs/kirillosenkov/using-a-common-intermediate-and-output-directory-for-your-solution) `IntermediateOutputPath` **.csproj** 。 請嘗試使用相對較短的路徑。 例如：

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

如需設定組建屬性的詳細資訊，請參閱 [組建流程](~/android/deploy-test/building-apps/build-process.md)。