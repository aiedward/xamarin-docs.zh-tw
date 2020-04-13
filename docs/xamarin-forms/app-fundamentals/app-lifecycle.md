---
title: Xamarin.Forms App 生命週期
description: 本文說明如何回應應用程式生命週期，包括週期方法、頁面通知事件，以及強制回應導覽事件。
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 41e8d073982bf7963b3a77a939bf28e52e86feaa
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "67675182"
---
# <a name="xamarinforms-app-lifecycle"></a>Xamarin.Forms App 生命週期

基[`Application`](xref:Xamarin.Forms.Application)類別提供以下功能:

- [生命週期方法](#Lifecycle_Methods)`OnStart``OnSleep``OnResume`與 。
- [網頁瀏覽事件](#page)[`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)[`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing)。
- [模式導覽事件](#modal)`ModalPushing``ModalPushed``ModalPopping`,`ModalPopped`與 .

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>生命週期方法

該[`Application`](xref:Xamarin.Forms.Application)類別包含三個虛擬方法,可以重寫以回應生命週期更改:

- `OnStart` - 會在應用程式啟動時呼叫。
- `OnSleep` - 會在每次應用程式被移到背景時呼叫。
- `OnResume` - 會在應用程式被傳送到背景後又再次繼續時呼叫。

> [!NOTE]
> 「沒有」** 任何終止應用程式的方法。 正常情況下 (亦即非當機時) 會從 *OnSleep* 狀態終止應用程式，而不會提供程式碼任何其他通知。

若要觀察這些方法的呼叫時機，請在每個方法中實作 `WriteLine` 呼叫 (如下所示)，並在每個平台上進行測試。

```csharp
protected override void OnStart()
{
    Debug.WriteLine ("OnStart");
}
protected override void OnSleep()
{
    Debug.WriteLine ("OnSleep");
}
protected override void OnResume()
{
    Debug.WriteLine ("OnResume");
}
```

> [!IMPORTANT]
> 在 Android 上，`OnStart` 方法將會被輪流呼叫，同時也會在應用程式首次啟動時被呼叫，前提是主要活動在 `[Activity()]` 屬性中缺少 `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation`。

<a name="page" />

## <a name="page-notification-events"></a>頁面通知事件

[`Application`](xref:Xamarin.Forms.Application)類別上有兩個事件,它們提供頁面出現和消失的通知:

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)- 當頁面即將出現在螢幕上時,凸起。
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing)- 當頁面即將從螢幕上消失時凸起。

如果您想在畫面上出現頁面時追蹤它們，則可使用這些事件。

> [!NOTE]
> 和[`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)[`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing)事件分別[`Page`](xref:Xamarin.Forms.Page)從基類[`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing)[`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing)和事件之後引發。

<a name="modal" />

## <a name="modal-navigation-events"></a>強制回應導覽事件

[`Application`](xref:Xamarin.Forms.Application)類別上有四個事件,每個事件都有自己的事件參數,允許您回應顯示和取消的模式頁面:

- `ModalPushing` - 會在頁面被強制推送時引發。
- `ModalPushed` - 會在頁面被強制推送後引發。
- `ModalPopping` - 會在頁面被強制快顯時引發。
- `ModalPopped` - 會在頁面被強制快顯後引發。

> [!NOTE]
> `ModalPopping` 事件引數 (`ModalPoppingEventArgs` 型別) 包含 `Cancel` 屬性。 當 `Cancel` 設為 `true` 時，強制回應快顯已取消。
