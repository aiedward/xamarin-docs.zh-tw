---
title: 應用程式生命週期
description: 如何回應應用程式生命週期
ms.prod: xamarin
ms.assetid: 69B416CF-B243-4790-AB29-F030B32465BE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/31/2018
ms.openlocfilehash: a22ad8f3f272212f5c7f088ba2112f2771ff4a7f
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/07/2018
ms.locfileid: "34846340"
---
# <a name="app-lifecycle"></a>應用程式生命週期

[ `Application` ](xref:Xamarin.Forms.Application)基底類別提供下列功能：

* [存留週期方法](#Lifecycle_Methods) `OnStart`， `OnSleep`，和`OnResume`。
* [頁面導覽事件](#page) [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing)， [ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing)。
* [強制回應巡覽事件](#modal) `ModalPushing`， `ModalPushed`， `ModalPopping`，和`ModalPopped`。

<a name="Lifecycle_Methods" />

## <a name="lifecycle-methods"></a>存留週期方法

[ `Application` ](xref:Xamarin.Forms.Application)類別包含三種虛擬方法，您可以處理存留週期方法覆寫：

* **OnStart** -應用程式啟動時呼叫。

* **OnSleep** -應用程式移到背景每次呼叫。

* **OnResume** -繼續應用程式時之後要傳送至背景, 時呼叫。

請注意，沒有*沒有*終止應用程式的方法。
在一般情況下 (ie。 未損毀) 終止應用程式將會從*OnSleep*狀態，而沒有任何其他通知您的程式碼。

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

更新時*舊*Xamarin.Forms 應用程式 （例如。 建立使用 Xamarin.Forms 1.3 或更舊），確保 Android 主要活動包括`ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation`中`[Activity()]`屬性。 如果不存在將會觀察`OnStart`旋轉以及應用程式初次啟動時，會呼叫方法。 這個屬性會自動包含在目前的 Xamarin.Forms 應用程式範本中。

<a name="page" />

## <a name="page-navigation-events"></a>頁面導覽事件

有兩個事件上[ `Application` ](xref:Xamarin.Forms.Application)提供通知的顯示和消失頁面的類別：

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) -出現在畫面的頁面時引發。
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) -從畫面消失頁面時引發。

這些事件可在案例中您要追蹤頁面，因為它們會出現在螢幕上。

> [!NOTE]
> [ `PageAppearing` ](xref:Xamarin.Forms.Application.PageAppearing)和[ `PageDisappearing` ](xref:Xamarin.Forms.Application.PageDisappearing)事件所引發的[ `Page` ](xref:Xamarin.Forms.Page)基底類別之後立即[ `Page.Appearing`](xref:Xamarin.Forms.Page.Appearing)和[ `Page.Disappearing` ](xref:Xamarin.Forms.Page.Disappearing)事件，分別。

<a name="modal" />

## <a name="modal-navigation-events"></a>強制回應巡覽事件

上有四個事件[ `Application` ](xref:Xamarin.Forms.Application)類別，每個都有自己的事件引數，可讓您回應所顯示，並解除強制回應頁面：

* **ModalPushing** - `ModalPushingEventArgs`
* **ModalPushed** - `ModalPushedEventArgs`
* **ModalPopping** -`ModalPoppingEventArgs`類別包含`Cancel`屬性。 當`Cancel`設`true`強制回應的快顯已取消。
* **ModalPopped** - `ModalPoppedEventArgs`

> [!NOTE]
> 若要實作應用程式生命週期的方法和強制回應巡覽事件，所有預先`Application`建立 Xamarin.Forms 應用程式的方法 (ie。 撰寫 1.2 版或較舊的應用程式使用靜態`GetMainPage`方法) 來建立已更新預設`Application`方向會設為的父系`MainPage`。
>
> 使用這個舊版行為的 Xamarin.Forms 應用程式必須更新為`Application`子類別上所述[應用程式類別](~/xamarin-forms/app-fundamentals/application-class.md)頁面。
