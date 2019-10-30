---
title: 自訂按鈕
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: d85c67cf18c61af04cf12bfab58a5b516d380f62
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029360"
---
# <a name="custom-button"></a>自訂按鈕

在本節中，您將使用 [ [`Button`](xref:Android.Widget.Button) widget] 和 [XML 檔案] 來建立具有自訂影像（而非文字）的按鈕，此檔案會定義三個不同的影像以用於不同的按鈕狀態。 按下按鈕時，將會顯示簡短的訊息。

以滑鼠右鍵按一下並下載下列三個影像，然後將它們複製到專案的 [**資源]/** [可繪製] 目錄。 這些會用於不同的按鈕狀態。

 [![處於正常狀態的綠色 android 圖示](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [![適用于焦點狀態的橙色](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox)Android 圖示[![黃色 android 圖示已按下狀態](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

在名為 android_button 的**Resources/** **xml**目錄中建立新檔案。 插入下列 XML：

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

這會定義單一的可繪製資源，這會根據按鈕的目前狀態來變更其影像。 第一個 `<item>` 會在按下按鈕時，將**android_pressed**定義為影像（已啟動）;第二個 `<item>` 會在按鈕為焦點時，將**android_focused**定義為影像（使用軌跡球或方向面板將按鈕反白顯示時）。第三個 `<item>` 會將**android_normal**定義為一般狀態的影像（未按下或專注時）。 這個 XML 檔案現在代表單一可繪製資源，當[`Button`](xref:Android.Widget.Button)用於其背景時，顯示的影像將會根據這三個狀態而變更。

> [!NOTE]
> `<item>` 元素的順序很重要。 參考此可繪製時，會依序進行 `<item>`s，以判斷哪一個適合目前的按鈕狀態。
> 因為「標準」影像是最後的，所以只有在條件 `android:state_pressed` 且 `android:state_focused` 同時評估為 false 時才會套用。

開啟**Resources/layout/axml**檔案，並新增[`Button`](xref:Android.Widget.Button)元素：

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

`android:background` 屬性會指定要用於按鈕背景的可繪製資源（當儲存在**資源/可繪製/android .xml**時，會以 `@drawable/android`的形式來參考）。 這會取代整個系統中用於按鈕的一般背景影像。 為了讓可繪製根據按鈕狀態變更其影像，必須將影像套用至背景。

若要讓按鈕在按下時執行一些動作，請在[`OnCreate()`](xref:Android.App.Activity.OnCreate*)結尾處新增下列程式碼：
方法

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

這會從配置中捕捉[`Button`](xref:Android.Widget.Button) ，然後新增要在按一下[`Button`](xref:Android.Widget.Button)時顯示的[`Toast`](xref:Android.Widget.Toast)訊息。

現在執行應用程式。

*此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改，並根據*
[*創意 Commons 2.5 屬性授權*](https://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。
