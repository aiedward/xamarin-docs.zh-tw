---
title: Nuget 封裝在更新之後，遺失封裝錯誤
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
author: asb3993
ms.author: amburns
ms.openlocfilehash: 4f1c4f51b690e35711efc0fc4fac56565b9cb3c7
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
ms.locfileid: "33917378"
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>Nuget 封裝在更新之後，遺失封裝錯誤

主要 Xamarin.Forms 範例應用程式方案，在報告此問題，但此問題的可能性可能發生在任何使用 NuGet 封裝的專案上。 

如果在更新之後，您的專案或方案中的 Nuget 封裝，您會看到錯誤，參考舊封裝的版本號碼，例如：

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.  
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)

```

在此範例中*Xamarin.Forms.1.3.1.6296*是以 Nuget 套件更新已移除的舊版本號碼。

這種情況.csproj 檔案的 XML 元素參考舊封裝的版本號碼必須以手動方式加入或編輯，Nuget 會移除或，如果它們一直以手動方式加入/編輯，因此現在正在尋找專案封裝已更新刪除。 

若要修正此問題，請手動編輯.csproj 檔案，並刪除所有項目會參考舊的版本號碼。 

若要移除 （如果有舊封裝的版本號碼） 的範例項目：

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />

```

