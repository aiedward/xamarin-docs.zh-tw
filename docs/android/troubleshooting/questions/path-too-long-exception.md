---
title: 如何解決路徑過長異常錯誤?
description: 本文介紹如何解決構建應用時可能發生的 PathToolongException。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: ffe88546ff58387865d71268bd64ec05c8aec3c5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026795"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>如何解決路徑過長異常錯誤?

## <a name="cause"></a>原因

在 Xamarin.Android 專案中生成的路徑名稱可能相當長。
例如,可以在生成期間生成如下所示的路徑:

**C:\\\\\\某些\\\\目錄\\解決方案專案\\obj 除錯__library_projects__\\Xamarin.Forms.Platform.Androidlibrary_project_imports\\\\資產**

在 Windows 上(路徑的最大長度為[260 個字元](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)),如果生成的路徑超過最大長度,則在生成路徑超過最大長度時,可以在生成專案時生成**PathToolongException。** 

## <a name="fix"></a>修正

預設情況下`UseShortFileNames`,MSBuild 屬性`True`設置為 規避此錯誤。 當此屬性設置為`True`時 ,生成過程使用較短的路徑名稱來降低生成**PathToolongException**的可能性。
例如,當`UseShortFileNames`設定`True`為 時,上述路徑將縮短為類似於以下內容的路徑:

**C:\\\\\\某些\\\\目錄\\解決方案\\\\專案\\\\obj 除錯 lp 1 jl 資產**

要手動設定這個屬性,將以下 MSBuild 屬性新增到專案 **.csproj**檔案:

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

如果設定這個旗標不能修復**PathToolongException**錯誤,則另一種方法是透過在 project **.csproj**檔中設定`IntermediateOutputPath`,為解決方案中的項目指定[通用中間輸出根](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/)。 嘗試使用相對較短的路徑。 例如：

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

有關設定產生屬性的詳細資訊,請參閱[產生程序](~/android/deploy-test/building-apps/build-process.md)。
