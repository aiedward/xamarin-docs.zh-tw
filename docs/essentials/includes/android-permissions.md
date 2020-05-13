---
ms.topic: include
author: jamesmontemagno
ms.author: jamont
ms.date: 05/11/2020
ms.openlocfilehash: b5396061de657690fa3f4cdf68e8a94382918613
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149746"
---
此 API 會使用 Android 上的執行時間許可權。 請確定 Xamarin 已完全初始化，且您的應用程式中已設定許可權處理。

在 Android 專案的 `MainLauncher` 或任何啟動的任何 `Activity` 中，Xamarin.Essentials都必須在 `OnCreate` 方法中初始化：

```csharp
protected override void OnCreate(Bundle savedInstanceState) 
{
    //...
    base.OnCreate(savedInstanceState);
    Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
    //...
}    
```

若要處理 Android 上的執行階段權限，Xamarin.Essentials 必須接收任 `OnRequestPermissionsResult`。 將下列程式碼新增到所 `Activity` 類別：

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Android.Content.PM.Permission[] grantResults)
{
    Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

    base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
}
```
