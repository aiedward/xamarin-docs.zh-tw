---
title: Xamarin. Android RatingBar
description: 如何將 RatingBar widget 新增至 Android 活動。
ms.prod: xamarin
ms.assetid: d7a1f9bb-926d-4f93-9e8e-0fa933e330e7
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 529fecb4e24e83ef7b783815843e132347d99262
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029146"
---
# <a name="xamarinandroid-ratingbar"></a>Xamarin. Android RatingBar

RatingBar 是一個 UI widget，可顯示一到五顆星的評等。 使用者可以在本節中的星星點選選取評等，您將建立一個 widget，讓使用者使用[`RatingBar`](xref:Android.Widget.RatingBar) widget 來提供評等。

![RatingBar 的範例](ratingbar-images/01-ratingbar.png)

## <a name="creating-a-ratingbar"></a>建立 RatingBar

1. 開啟**資源/layout/axml**檔案，並新增[`RatingBar`](xref:Android.Widget.RatingBar)
   元素（在[`LinearLayout`](xref:Android.Widget.LinearLayout)內）：

   ```xml
   <RatingBar android:id="@+id/ratingbar"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:numStars="5"
            android:stepSize="1.0"/>
   ```

   `android:numStars` 屬性會定義要針對評等列顯示多少顆星。 `android:stepSize` 屬性會定義每個星星的資料細微性（例如，`0.5` 的值可允許半星分級）。

2. 若要在設定新的評等時執行某些動作，請將下列程式碼新增至[`OnCreate()`](xref:Android.App.Activity.OnCreate*)的結尾
   方法

    ```csharp
    RatingBar ratingbar = FindViewById<RatingBar>(Resource.Id.ratingbar);

    ratingbar.RatingBarChange += (o, e) => {
            Toast.MakeText(this, "New Rating: " + ratingbar.Rating.ToString (), ToastLength.Short).Show ();
    };
    ```

    這會使用[`FindViewById`](xref:Android.App.Activity.FindViewById*)從配置中捕捉[`RatingBar`](xref:Android.Widget.RatingBar) widget，然後設定事件方法，然後定義當使用者設定評等時要執行的動作。 在此情況下，簡單的[`Toast`](xref:Android.Widget.Toast)訊息會顯示新的評等。

3. 執行應用程式。
