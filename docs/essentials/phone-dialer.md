---
title: Xamarin.Essentials：電話撥號程式
description: Xamarin.Essentials 中的 PhoneDialer 類別可讓應用程式在撥號程式中開啟電話號碼
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: bccc76e1602c475a5e4cc9a95d498d11f9a379b1
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675415"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials：電話撥號程式

![發行前的 NuGet](~/media/shared/pre-release.png)

**PhoneDialer** 類別可讓應用程式在撥號程式中開啟電話號碼。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>使用電話撥號程式

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

電話撥號程式功能透過呼叫含有電話號碼的 `Open` 方法來開啟撥號程式。 當要求 `Open` 時，API 會自動嘗試依據國碼 (地區碼) (若有指定) 來將號碼格式化。

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

- [電話撥號程式原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [電話撥號程式 API 文件](xref:Xamarin.Essentials.PhoneDialer)
