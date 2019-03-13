---
title: 如何解決 PathTooLongException 錯誤？
description: 這篇文章說明如何解決 PathTooLongException 建置應用程式時可能發生。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 4cb3e13ebbe3d9e8aed153528a35ab16c92e2145
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108700"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>如何解決 PathTooLongException 錯誤？

## <a name="cause"></a>原因

Xamarin.Android 專案中產生的路徑名稱可能相當長。
比方說，無法在建置期間產生的路徑，如下所示：

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

在 Windows 上 (其中一個路徑的最大長度是[260 個字元](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx))，則**PathTooLongException**無法建置專案，如果產生的路徑超過最大長度時產生。 

## <a name="fix"></a>修正

開始使用 Xamarin.Android 8.0 `UseShortFileNames` MSBuild 屬性可以設定為規避這項錯誤。 當這個屬性設定為`True`(預設值是`False`)，建置程序會使用較短的路徑名稱來降低的產生可能性**PathTooLongException**。
例如，當`UseShortFileNames`設為`True`，上述的路徑已縮短為類似下列的路徑：

**C:\\某些\\目錄\\解決方案\\專案\\obj\\偵錯\\lp\\1\\jl\\資產**

若要設定這個屬性，將下列 MSBuild 屬性加入至專案 **.csproj**檔案：

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

如果設定這個旗標不會修正**PathTooLongException**錯誤，另一種方法是指定[常見的中繼輸出根目錄](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/)藉由設定方案中的專案`IntermediateOutputPath`中專案 **.csproj**檔案。 嘗試使用相對較短的路徑。 例如: 

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

如需有關設定建置屬性的詳細資訊，請參閱 <<c0> [ 建置程序](~/android/deploy-test/building-apps/build-process.md)。
