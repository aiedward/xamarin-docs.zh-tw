---
title: Xamarin.Forms App 生命週期
description: 本文說明如何回應應用程式生命週期，包括週期方法、頁面導覽事件，以及強制回應導覽事件。
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 5bdce8d1752b3d7273ffec233b120266909999c4
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675116"
---
# <a name="xamarinforms-app-lifecycle"></a>Xamarin.Forms App 生命週期

[`Application`](xref:Xamarin.Forms.Application) 基底類別可提供下列功能：

* [生命週期方法](#Lifecycle_Methods) `OnStart`、`OnSleep` 和 `OnResume`。
* [頁面導覽事件](#page) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)、[`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing)。
* [強制回應導覽事件](#modal) `ModalPushing`、`ModalPushed`、`ModalPopping` 和 `ModalPopped`。

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>生命週期方法

[`Application`](xref:Xamarin.Forms.Application) 類別包含三個可供覆寫以處理生命週期方法的虛擬方法：

* **OnStart** - 在應用程式啟動時呼叫。

* **OnSleep** - 每當應用程式移到背景時呼叫。

* **OnResume** - 在應用程式傳送到背景之後並繼續時呼叫。

請注意，「沒有」任何終止應用程式的方法。
正常情況下 (亦即非當機時) 會從 *OnSleep* 狀態終止應用程式，而不會提供程式碼任何其他通知。

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

更新「舊版」Xamarin.Forms 應用程式 (亦即 使用 Xamarin.Forms 1.3 或更舊版本建立) 時，請確認 Android 主要活動的 `[Activity()]` 屬性中包含 `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation`。 如果此項目不存在，您將發現 `OnStart` 方法會以輪替方式呼叫，以及在應用程式初次啟動時呼叫。 這個屬性會自動包含在目前的 Xamarin.Forms 應用程式範本中。

<a name="page" />

## <a name="page-navigation-events"></a>頁面導覽事件

[`Application`](xref:Xamarin.Forms.Application) 類別上有兩個事件可提供頁面出現和消失的通知：

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) - 頁面即將出現在畫面上時引發。
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) - 頁面即將從畫面上消失時引發。

如果您想在畫面上出現頁面時進行追蹤，則可使用這些案例。

> [!NOTE]
> [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) 和 [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) 事件是分別在 [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) 和 [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) 事件引發之後，立即從 [`Page`](xref:Xamarin.Forms.Page) 基底類別引發。

<a name="modal" />

## <a name="modal-navigation-events"></a>強制回應導覽事件

[`Application`](xref:Xamarin.Forms.Application) 類別上有四個事件，每個都有自己的事件引數，可讓您回應顯示與關閉的強制回應頁面：

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** - `ModalPoppingEventArgs` 類別包含 `Cancel` 屬性。 當 `Cancel` 設為 `true` 時，強制回應快顯已取消。
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> 為了實作應用程式生命週期方法和強制回應導覽事件，所有建立 Xamarin.Forms 應用程式的 `Application` 前方法 (也就是以使用靜態 `GetMainPage` 方法的 1.2 版或更舊版本撰寫的應用程式) 均已更新，以建立預設 `Application` (其會設為 `MainPage` 的父系)。
>
> 您必須將使用此舊版行為的 Xamarin.Forms 應用程式更新為 `Application` 子類別，如[應用程式類別](~/xamarin-forms/app-fundamentals/application-class.md)頁面所述。
