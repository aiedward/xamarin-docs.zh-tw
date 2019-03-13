---
title: 自訂按鈕
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b5ccefa1eb7e659584c1c82481bbd4473a3a8abc
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122851"
---
# <a name="custom-button"></a>自訂按鈕

在本節中，您將建立按鈕，使用自訂的映像，而不是文字，使用[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)小工具和 XML 檔案會定義三個不同的映像，以使用不同的按鈕狀態。 按下按鈕時，就會顯示簡短訊息。

以滑鼠右鍵按一下並下載下面的三個映像，然後將其複製到**資源/drawable**專案的目錄。 這些將用於不同按鈕狀態。

 [![綠色 Android 正常狀態圖示](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [![焦點狀態的橙色 Android 圖示](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) [![已按下狀態的黃色 Android 圖示](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

建立新的檔案中**資源/drawable**名為目錄**android_button.xml**。 插入下列 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/android_pressed"
          android:state_pressed="true" />
    <item android:drawable="@drawable/android_focused"
          android:state_focused="true" />
    <item android:drawable="@drawable/android_normal" />
</selector>
```

這會定義單一的可繪製資源，這將會變更其按鈕的目前狀態為基礎的映像。 第一個`<item>`定義**android_pressed.png**做為影像餂鈕蒢 （啟用）; 第二個`<item>`定義**android_focused.png**作為映像時（按鈕使用軌跡球或方向鍵反白顯示） 時，按鈕是已取得焦點;第三個`<item>`定義**android_normal.png**與正常的狀態 （當和都不按下已取得焦點） 映像。 此 XML 檔案現在會代表單一的可繪製資源和參考時[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)槲剢褁，所顯示的影像會隨這三種狀態。


> [!NOTE]
> 順序`<item>`項目是很重要。 這可繪製參考時，`<item>`會周遊才能判斷哪一項適用於目前的按鈕狀態。
> 「 標準 」 的影像是最後一個項目，因為它套用的時，才會在條件`android:state_pressed`和`android:state_focused`都評估為 false。

開啟**Resources/layout/Main.axml**檔案，並新增[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)項目：

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

`android:background`屬性會指定要用於按鈕背景的可繪製資源 (即在儲存時**Resources/drawable/android.xml**，參考為`@drawable/android`)。 這會取代整個系統的按鈕所用的一般背景影像。 為了讓可繪製來變更其按鈕的狀態為基礎的映像，映像必須套用到背景工作。

若要執行一些動作時按下按鈕，新增下列程式碼的結尾 [`OnCreate()`](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/p/Android.OS.Bundle/Android.OS.PersistableBundle/)
方法：

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

這會擷取[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)配置，然後加入[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)時顯示的訊息[ `Button` ](https://developer.xamarin.com/api/type/Android.Widget.Button/)按下。

現在執行應用程式。


*此頁面上的部分是根據工作建立及 Android 的開放原始碼專案所共用，並依據所述的條款來使用修改*
[*Creative Commons 2.5 Attribution License*](http://creativecommons.org/licenses/by/2.5/).
