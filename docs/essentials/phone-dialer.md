---
title: Xamarin.Essentials 電話撥號員
description: PhoneDialer 類別可讓應用程式來最佳化的系統慣用的瀏覽器或外部瀏覽器中開啟網頁連結。
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6bea1b26e43459be12272f9dd2ecc6fafba2b8b4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials 電話撥號員

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**PhoneDialer**類別可讓應用程式來最佳化的系統慣用的瀏覽器或外部瀏覽器中開啟網頁連結。

## <a name="using-phone-dialer"></a>使用電話撥號員

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

電話撥號員 功能的運作方式是呼叫`Open`與要開啟與撥號的電話號碼的方法。 當`Open`要求 API 將會自動嘗試將依據國家 （地區） 的程式碼，如果指定的數字格式。

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

- [電話撥號員原始程式碼](https://github.com/xamarin/Essentials/tree/master/Essentials/PhoneDialer)
- [電話撥號員 API 文件](xref:Xamarin.Essentials.PhoneDialer)
