---
title: Xamarin.Forms 應用程式生命週期
description: 這篇文章說明如何回應應用程式生命週期，包括存留週期方法、 頁面瀏覽事件，以及強制回應導覽事件。
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: 5bdce8d1752b3d7273ffec233b120266909999c4
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675116"
---
# <a name="xamarinforms-app-lifecycle"></a>Xamarin.Forms 應用程式生命週期

[ `Application` ](xref:Xamarin.Forms.Application)基底類別提供下列功能：

* [生命週期方法](#Lifecycle_Methods) `OnStart`， `OnSleep`，和`OnResume`。
* [頁面瀏覽事件](#page) [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing)， [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing)。
* [強制回應導覽事件](#modal) `ModalPushing`， `ModalPushed`， `ModalPopping`，和`ModalPopped`。

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>生命週期方法

[ `Application` ](xref:Xamarin.Forms.Application)類別包含三個可以處理生命週期方法覆寫的虛擬方法：

* **OnStart** -應用程式啟動時呼叫。

* **OnSleep** -應用程式移到背景工作，每次呼叫。

* **OnResume** -繼續應用程式時之後要傳送到背景工作, 時呼叫。

請注意，沒有*沒有*終止應用程式的方法。
在正常情況下 （亦即未損毀） 終止應用程式會從*OnSleep*狀態，而不進行任何額外的通知，您的程式碼。

若要觀察呼叫這些方法時，實作`WriteLine`（如下所示），在每個呼叫，並在每個平台上進行測試。

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

更新時*舊*Xamarin.Forms 應用程式 （例如。 建立 Xamarin.Forms 1.3 或更舊版本），請確認 Android 的主要活動中包含`ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation`在`[Activity()]`屬性。 如果不存在，您會看到`OnStart`上旋轉，以及應用程式初次啟動時，會呼叫方法。 這個屬性會自動包含在目前的 Xamarin.Forms 應用程式範本。

<a name="page" />

## <a name="page-navigation-events"></a>頁面瀏覽事件

上有兩個事件[ `Application` ](xref:Xamarin.Forms.Application)提供的頁面出現和消失，通知的類別：

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) -出現在畫面的頁面時引發。
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) -當頁面從畫面消失時引發。

這些事件可用的案例中您要追蹤頁面，因為它們會出現在螢幕上。

> [!NOTE]
> [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing)並[ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing)事件所引發的[ `Page` ](xref:Xamarin.Forms.Page)基底類別後面[ `Page.Appearing`](xref:Xamarin.Forms.Page.Appearing)並[ `Page.Disappearing` ](xref:Xamarin.Forms.Page.Disappearing)事件，分別。

<a name="modal" />

## <a name="modal-navigation-events"></a>強制回應導覽事件

上有四個事件[ `Application` ](xref:Xamarin.Forms.Application)類別，每個都有自己的事件引數，可讓您回應所示，已解除的強制回應頁面：

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** -`ModalPoppingEventArgs`類別包含`Cancel`屬性。 當`Cancel`設為`true`強制回應快顯已取消。
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> 若要實作應用程式生命週期方法和強制回應導覽事件，所有預先`Application`建立 Xamarin.Forms 應用程式的方法 (也就是撰寫的應用程式版本 1.2 或更舊版本中，使用靜態`GetMainPage`方法) 已更新，以建立預設值`Application`方向會設為父系`MainPage`。
>
> 使用這個舊版行為的 Xamarin.Forms 應用程式必須更新，才能`Application`子類別上所述[應用程式類別](~/xamarin-forms/app-fundamentals/application-class.md)頁面。
