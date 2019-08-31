---
title: 更新 Nuget 套件之後的遺失套件錯誤
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 49f0e57af1ad6b6e0f322eb9865ba99db4dd47ec
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199904"
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>更新 Nuget 套件之後的遺失套件錯誤

這個問題主要是在 Xamarin 上回報。表單範例應用程式解決方案, 但此問題的潛能可能發生在任何使用 NuGet 套件的專案上。

如果在更新專案或方案中的 Nuget 套件之後, 您會看到參考舊封裝版本號碼的錯誤, 例如:

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)
```

在此範例中, *1.3.1.6296*是使用 Nuget 套件更新移除的舊版本號碼。

如果您已手動新增或編輯 .csproj 檔案中參考舊封裝版本號碼的 XML 專案, Nuget 將不會移除或更新這些元素, 因此, 專案現在正在尋找已存在的套件。刪除.

若要修正此問題, 請手動編輯 .csproj 檔案, 並刪除所有參考舊版本號碼的元素。

要移除的範例元素 (如果它們具有舊的套件版本號碼):

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />
```
