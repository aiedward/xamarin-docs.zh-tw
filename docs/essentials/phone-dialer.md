---
title: Xamarin.Essentials： 電話撥號員
description: Xamarin.Essentials PhoneDialer 類別可讓應用程式以開啟 撥號員中的 電話號碼
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 34a6c80836d8cb42b1f8fd95718fe248d4701c0f
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130789"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials： 電話撥號員

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**PhoneDialer**類別可讓您的應用程式，以開啟 撥號員中的 電話號碼。

## <a name="using-phone-dialer"></a>使用電話撥號員

在您的類別加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

電話撥號員功能的運作方式是呼叫`Open`與要開啟 使用撥號的電話號碼的方法。 當`Open`要求 API 將會自動嘗試將依據國家/地區的程式碼，如果指定的數字格式。

```csharp
public class PhoneDialerTest
{
    public async Task PlacePhoneCall(string number)
    {
        try
        {
            PhoneDialer.Open(number);
        }
        catch (ArgumentNullException anEx)
        {
            // Number was null or white space
        }
        catch (FeatureNotSupportedException ex)
        {
            // Phone Dialer is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [電話撥號員原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [電話撥號員 API 文件](xref:Xamarin.Essentials.PhoneDialer)
