---
title: RatingBar
description: 如何將 RatingBar widget 新增至 Android 活動。
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: 97d2a126be70e210d2e8f4ebf4d7a25ff8777a02
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945433"
---
# <a name="ratingbar"></a>RatingBar

RatingBar 是一種 UI widget，顯示從一個到五顆星的評等。 使用者可能選擇評等，藉由點選在這一節中的星號上，您將建立一種 widget，可讓使用者提供評等[ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/)小工具。

![RatingBar 的範例](ratingbar-images/01-ratingbar.png)


## <a name="creating-a-ratingbar"></a>建立 RatingBar

1. 開啟**Resource/layout/Main.axml**檔案，並新增 [`RatingBar`](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/)
   項目 (在[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

    ```xml
    <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
    ```
   `android:numStars`屬性可讓您定義要顯示 [分級] 列的幾個星號。 `android:stepSize`屬性定義每一顆星星的資料粒度 (例如，值`0.5`允許半顆星的評等)。

2. 已經設定新的評等時，請執行一些作業，請將下列程式碼新增至結尾 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle)
   方法：

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    這會擷取[ `RatingBar` ](https://developer.xamarin.com/api/type/Android.Widget.RatingBar/)小工具的版面配置，與從[ `FindViewById` ](https://developer.xamarin.com/api/member/Android.App.Activity.FindViewById/)然後設定事件的方法，則會定義當使用者設定評等時要執行的動作。 在此情況下，簡單[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)訊息會顯示新的評比。

3.  執行應用程式。

