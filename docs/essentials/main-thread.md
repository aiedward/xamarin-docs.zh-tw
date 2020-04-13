---
title: 'Xamarin.Essentials: MainThread'
description: MainThread 類別可讓應用程式在主執行緒上執行程式碼。
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E1
author: jamesmontemagno
ms.author: jamont
ms.date: 08/20/2019
ms.openlocfilehash: 9109e7bff4cfe60479e711240d290d77b60a9af6
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "70060112"
---
# <a name="xamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

**MainThread** 類別可讓應用程式在主執行緒上執行程式碼，並判斷特定程式碼區塊目前是否在主執行緒上執行。

## <a name="background"></a>背景

大部分的作業系統 (包括 iOS、Android 與通用 Windows 平台) 都為牽涉到使用者介面的程式碼使用單一執行緒處理模型。 需要此此模型才能適當地序列化使用者介面事件，包括按鍵點擊與觸控輸入。 此執行緒通常稱為「主執行緒」__ 或「使用者介面執行緒」__ 或「UI 執行緒」__。 此模型的缺點是存取使用者介面元素的所有程式碼都必須在應用程式的主執行緒上執行。 

應用程式有時候需要使用呼叫次要執行緒上之事件處理常式的事件。 (Xamarin.Essentials[`Accelerometer`](accelerometer.md)[`Compass`](compass.md)類[`Gyroscope`](gyroscope.md)[`Magnetometer`](magnetometer.md),[`OrientationSensor`](orientation-sensor.md), 和, 和當以更快的速度使用時,所有可能返回輔助線程上的資訊。如果事件處理程式需要存取使用者介面元素,它必須在主線程上運行該代碼。 **MainThread** 類別可讓應用程式在主執行緒上執行此程式碼。

## <a name="get-started"></a>開始使用

[!include[](~/essentials/includes/get-started.md)]

## <a name="running-code-on-the-main-thread"></a>在只執行緒上執行程式碼

在類別中新增對 Xamarin.Essentials 的參考：

```csharp
using Xamarin.Essentials;
```

若要在主執行緒上執行程式碼，請呼叫靜態 `MainThread.BeginInvokeOnMainThread` 方法。 參數是一個[`Action`](xref:System.Action)物件,它只是一個沒有參數和返回值的方法:

```csharp
MainThread.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

也可以針對必須在主執行緒上執行的程式碼定義不同的方法：

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

接著，您可以透過在 `BeginInvokeOnMainThread` 方法中參考它，以主執行緒上執行此方法：

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms 有一個稱為[`Device.BeginInvokeOnMainThread(Action)`](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread)
> 它會執行與 `MainThread.BeginInvokeOnMainThread(Action)` 相同的功能。 當您在 Xamarin.Forms 應用程式中使用任一方法時，請考慮發出呼叫的程式碼是否有對 Xamarin.Forms 任何其他相依性需求。 若沒有，`MainThread.BeginInvokeOnMainThread(Action)` 很可能是比較好的選項。

## <a name="determining-if-code-is-running-on-the-main-thread"></a>判斷程式碼是否是在主執行緒上執行

`MainThread` 類別也允許應用程式判斷特定程式碼區塊是否在主執行緒上執行。 若呼叫屬性的程式碼是在主執行緒上執行，`IsMainThread` 屬性會傳回 `true`。 程式可以使用此屬性為主執行緒與次要執行緒執行不同的程式碼：

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

您可能想要知道是否應該在呼叫 `BeginInvokeOnMainThread` 之前先檢查程式碼是否在次要執行緒上執行，例如，像這樣：

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

您可能會懷疑若程式碼區塊已在主執行緒上執行，此檢查是否會改善效能。

不過，此檢查並非必要。__ `BeginInvokeOnMainThread` 本身的平台實作會檢查呼叫是否針對主執行緒發出。 若在並非必要的情況下呼叫 `BeginInvokeOnMainThread`，會對效能造成極小的影響。

## <a name="additional-methods"></a>其他方法

`static` 類別包含下列其他 `MainThread`方法，可用來與背景執行緒中的使用者介面元素互動：

| 方法 | 引數 | 傳回值 | 目的 |
|---|---|---|---|
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | 在主要執行緒上叫用 `Func<T>`，並等候其完成。 |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | 在主要執行緒上叫用 `Action`，並等候其完成。 |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | 在主要執行緒上叫用 `Func<Task<T>>`，並等候其完成。 |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | 在主要執行緒上叫用 `Func<Task>`，並等候其完成。 |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | 傳回主要執行緒的 `SynchronizationContext`。 |

## <a name="api"></a>API

- [MainThread 原始程式碼](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/MainThread)
- [MainThread API 文件](xref:Xamarin.Essentials.MainThread)
