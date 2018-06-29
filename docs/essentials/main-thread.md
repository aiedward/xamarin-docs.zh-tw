---
title: 'Xamarin.Essentials: MainThread'
description: MainThread 類別可讓應用程式主執行緒上執行的程式碼。
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E1
author: charlespetzold
ms.author: chape
ms.date: 06/26/2018
ms.openlocfilehash: e07d36d3e9a5492e6e170b62dbacb36be44dbfa9
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080450"
---
# <a name="xamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

![發行前版本的 NuGet](~/media/shared/pre-release.png)

**MainThread**類別可讓應用程式執行時，主執行緒上執行的程式碼，以判斷特定程式碼區塊是目前在主執行緒上執行。

## <a name="background"></a>背景

大部分的作業系統，包括 iOS、 Android 和通用 Windows 平台 — 涉及使用者介面的程式碼中使用單一執行緒模型。 此模型是正確的序列化使用者介面事件，包括按鍵輸入與觸控輸入必要的。 這個執行緒通常稱為_主執行緒_或_使用者介面執行緒_或_UI 執行緒_。 此模型的缺點是所有程式碼，存取使用者介面項目必須在應用程式的主執行緒上執行。 

應用程式有時需要使用在次要執行的執行緒呼叫事件處理常式的事件。 (Xamarin.Essentials 類別[ `Accelerometer` ](accelerometer.md)， [ `Compass` ](compass.md)， [ `Gyroscope` ](gyroscope.md)， [ `Magnetometer` ](magnetometer.md)，和[ `OrientationSensor` ](orientation-sensor.md)所有可能會傳回次要執行緒更快的速度搭配使用時的相關資訊。)如果事件處理常式需要存取使用者介面項目，它必須在主執行緒上執行該程式碼。 **MainThread**類別可讓應用程式的主執行緒上執行此程式碼。

## <a name="running-code-on-the-main-thread"></a>在主執行緒上執行的程式碼

在您類別中加入 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

若要在主執行緒上執行程式碼，可以呼叫靜態`MainThread.BeginInvokeOnMainThread`方法。 引數是[ `Action` ](xref:System.Action)物件，它是只具有任何引數且沒有傳回值的方法：

```csharp
MainThread.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

此外，也可以定義不同的方法必須在主執行緒上執行的程式碼：

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

您可以再執行這個方法在主執行緒上參考在`BeginInvokeOnMainThread`方法：

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms 已呼叫的方法[ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread) ，會執行相同動作`MainThread.BeginInvokeOnMainThread(Action)`。 雖然您可以使用任一種方法，在 Xamarin.Forms 應用程式，請考慮呼叫程式碼有 Xamarin.Forms 上任何其他需要的相依性。 如果沒有，`MainThread.BeginInvokeOnMainThread(Action)`可能是較好的選擇。

## <a name="determining-if-code-is-running-on-the-main-thread"></a>決定是否主執行緒上執行的程式碼

`MainThread`類別也可讓應用程式判斷，是否主執行緒上執行特定程式碼區塊。 `IsMainThread`屬性會傳回`true`如果主執行緒上執行的程式碼呼叫屬性。 程式可以使用這個屬性，來執行不同的程式碼的主執行緒或次要執行緒：

```csharp
if (MainThread.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

您可能想知道是否應該檢查的程式碼執行之前先呼叫次要執行緒是否`BeginInvokeOnMainThread`，比方說，如下所示：

```csharp
if (MainThread.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

您可能會懷疑如果已在程式碼的區塊在主執行緒上執行這項檢查可能會改善效能。

_不過，不需要這項檢查。_ 平台實作`BeginInvokeOnMainThread`本身檢查是否主執行緒上進行呼叫。 如果您呼叫就很少的效能產生負面影響`BeginInvokeOnMainThread`當它不是真有必要。

## <a name="api"></a>API

- [MainThread 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/MainThread)
- [MainThread API 文件](xref:Xamarin.Essentials.MainThread)
