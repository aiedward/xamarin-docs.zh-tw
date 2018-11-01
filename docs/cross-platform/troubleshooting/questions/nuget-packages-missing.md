---
title: 更新 Nuget 套件之後的遺失套件錯誤
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 7cb802dd60d4e4879a260ff56d4f94ea5acb2965
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50674843"
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>更新 Nuget 套件之後的遺失套件錯誤

主要的 Xamarin.Forms 範例應用程式解決方案，報告此問題，但此問題的可能性可能位於任何使用 NuGet 套件的專案。 

之後更新您的專案或方案中的 Nuget 套件，您會看到錯誤，參考舊的套件版本號碼，例如：

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.  
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)
```

在此範例中*Xamarin.Forms.1.3.1.6296*是 Nuget 套件的更新已移除的舊版本號碼。

如果已手動新增參考舊的套件版本號碼的 XML 項目.csproj 檔案中此情形，或編輯，Nuget 會移除或更新它們，如果它們一直以手動方式新增/編輯，因此，已套件現在尋找專案刪除。 

若要修正此問題，請手動編輯.csproj 檔案，並刪除所有參考舊的版本號碼的項目。 

若要移除 （如果它們有舊的套件版本號碼） 的範例項目：

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />
```