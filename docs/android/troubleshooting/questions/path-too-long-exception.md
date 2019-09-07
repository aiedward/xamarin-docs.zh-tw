---
title: 如何? 解決 PathTooLongException 錯誤？
description: 本文說明如何解決建立應用程式時可能發生的 PathTooLongException。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 915f557db7955dc7b8b9f1bc5e014a683740052b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760806"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>如何? 解決 PathTooLongException 錯誤？

## <a name="cause"></a>原因

在 Xamarin Android 專案中產生的路徑名稱可能相當長。
例如，在組建期間可能會產生類似下列的路徑：

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

在 Windows 上（路徑的最大長度為[260 個字元](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)），如果產生的路徑超過最大長度，則會在建立專案時產生**PathTooLongException** 。 

## <a name="fix"></a>修正

根據預設， `True` MSBuild屬性會設定為以`UseShortFileNames`規避此錯誤。 當這個屬性設定為`True`時，組建程式會使用較短的路徑名稱，以降低產生**PathTooLongException**的可能性。
例如，當設`UseShortFileNames`為`True`時，上述路徑會縮短為類似下列的路徑：

**C：\\某些\\目錄\\解決方案專案\\ obj\\Debuglp\\1jl資產\\ \\ \\ \\**

若要手動設定此屬性，請將下列 MSBuild 屬性加入至專案 **.csproj**檔案：

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

如果設定此旗標無法修正**PathTooLongException**錯誤，另一種方法是在專案 **.csproj**檔案中設定`IntermediateOutputPath` ，為方案中的專案指定[通用的中繼輸出根](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/)。 嘗試使用相對較短的路徑。 例如：

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

如需設定組建屬性的詳細資訊，請參閱[建立進程](~/android/deploy-test/building-apps/build-process.md)。
