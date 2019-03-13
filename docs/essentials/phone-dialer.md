---
title: Xamarin.Essentials：電話撥號程式
description: Xamarin.Essentials 中的 PhoneDialer 類別可讓應用程式在撥號程式中開啟電話號碼
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 8d4b0cdcae5e33ac2c48baa0b7749597314eae8c
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898262"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials：電話撥號程式

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
