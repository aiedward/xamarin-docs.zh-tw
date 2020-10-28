---
title: Xamarin.Essentials：電話撥號程式
description: 中的 >phonedialer 類別 Xamarin.Essentials 可讓應用程式在撥號程式中開啟電話號碼
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 07/02/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9bd281a61fd53ef3f6d0d3d2307f78a218f33cf4
ms.sourcegitcommit: db423d51356cf5a2dfa1b3925204797b1baf3cd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92734775"
---
# <a name="no-locxamarinessentials-phone-dialer"></a>Xamarin.Essentials：電話撥號程式

**PhoneDialer** 類別可讓應用程式在撥號程式中開啟電話號碼。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

# <a name="android"></a>[Android](#tab/android)

如果您專案的目標 Android 版本設為 **android 11 (R API 30)** 您必須使用與新的 [封裝可見度需求](https://developer.android.com/preview/privacy/package-visibility)搭配使用的查詢來更新 android 資訊清單。

開啟 [Properties]  資料夾下的  節點內新增下列內容：

```xml
<queries>
  <intent>
    <action android:name="android.intent.action.DIAL" />
    <data android:scheme="tel"/>
  </intent>
</queries>
```

# <a name="ios"></a>[iOS](#tab/ios)

不需要進行額外設定。

# <a name="uwp"></a>[UWP](#tab/uwp)

無平台差異。

-----

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
