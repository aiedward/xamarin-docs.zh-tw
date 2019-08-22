---
title: Xamarin. Android RatingBar
description: 如何將 RatingBar widget 新增至 Android 活動。
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/29/2018
ms.openlocfilehash: de63a0f3f6564671a50594c66b55ed095329c95c
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887628"
---
# <a name="xamarinandroid-ratingbar"></a>Xamarin. Android RatingBar

RatingBar 是一個 UI widget, 可顯示一到五顆星的評等。 使用者可以在本節中的星星點選選取評等, 您將建立一個 widget, 讓使用者可以使用[`RatingBar`](xref:Android.Widget.RatingBar) widget 提供評等。

![RatingBar 的範例](ratingbar-images/01-ratingbar.png)


## <a name="creating-a-ratingbar"></a>建立 RatingBar

1. 開啟**資源/layout/axml**檔案, 並新增[`RatingBar`](xref:Android.Widget.RatingBar)
   元素 (在內[`LinearLayout`](xref:Android.Widget.LinearLayout)):

   ```xml
   <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
   ```

   `android:numStars`屬性會定義要針對評等列顯示多少顆星。 屬性會定義每個星形的資料細微性 (例如, 的`0.5`值允許半星分級)。 `android:stepSize`

2. 若要在設定新的評等時執行某些動作, 請將下列程式碼新增至結尾[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
   方法

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    這會使用[`RatingBar`](xref:Android.Widget.RatingBar) [`FindViewById`](xref:Android.App.Activity.FindViewById*)從配置中捕捉 widget, 然後設定事件方法, 然後定義當使用者設定分級時要執行的動作。 在此情況下, 簡單[`Toast`](xref:Android.Widget.Toast)的訊息會顯示新的評等。

3. 執行應用程式。

