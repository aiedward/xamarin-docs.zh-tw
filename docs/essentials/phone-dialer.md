---
title: Xamarin.Essentials：電話撥號程式
description: 中的命名為 phonedialer 類別 Xamarin.Essentials 可讓應用程式在撥號程式中開啟電話號碼
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 07/02/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d642005e9aed663570c251e955c6a3af4704ed5c
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84802205"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials：電話撥號程式

**PhoneDialer** 類別可讓應用程式在撥號程式中開啟電話號碼。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>使用電話撥號程式

Xamarin.Essentials在您的類別中新增的參考：

```csharp
using Xamarin.Essentials;
```

電話撥號程式功能透過呼叫含有電話號碼的 `Open` 方法來開啟撥號程式。 當要求 `Open` 時，API 會自動嘗試依據國碼 (地區碼) (若有指定) 來將號碼格式化。

```csharp
public class PhoneDialerTest
{
    public void PlacePhoneCall(string number)
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

- [電話撥號程式原始程式碼](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/PhoneDialer)
- [電話撥號程式 API 文件](xref:Xamarin.Essentials.PhoneDialer)

## <a name="related-video"></a>相關影片

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Phone-Dialer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
