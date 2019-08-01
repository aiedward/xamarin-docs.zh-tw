---
title: 自訂按鈕
ms.prod: xamarin
ms.assetid: C523D41E-5855-248D-079D-6B12B74B7617
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: ecb745f2f50b5aa0e22e331a4def0be9d8f86aa5
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510402"
---
# <a name="custom-button"></a>自訂按鈕

在本節中, 您將使用[`Button`](xref:Android.Widget.Button)小工具和 XML 檔案 (定義三個不同的影像以用於不同的按鈕狀態) 來建立具有自訂影像 (而非文字) 的按鈕。 按下按鈕時, 將會顯示簡短的訊息。

以滑鼠右鍵按一下並下載下列三個影像, 然後將它們複製到專案的 [**資源]/** [可繪製] 目錄。 這些會用於不同的按鈕狀態。

 綠色 android [圖示![ ](custom-button-images/android-focused.png)](custom-button-images/android-focused.png#lightbox) , 適用于聚焦狀態[ ![ ](custom-button-images/android-normal.png)](custom-button-images/android-normal.png#lightbox) [ ![的黃色 android 圖示已按下狀態的黃色 android 圖示](custom-button-images/android-pressed.png)](custom-button-images/android-pressed.png#lightbox)

在名為 android_button 的**Resources/** **xml**目錄中建立新檔案。 插入下列 XML:

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

這會定義單一的可繪製資源, 這會根據按鈕的目前狀態來變更其影像。 當按`<item>`下按鈕時, 第一個會將**android_pressed**定義為影像 (已啟動); 第二個`<item>`會將 [ **android_focused** ] 定義為焦點時的影像 (當按鈕為使用軌跡或方向面板反白顯示;而第三`<item>`個會將**android_normal**定義為一般狀態的影像 (未按下也沒有焦點時)。 這個 XML 檔案現在代表單一的可繪製資源, 當由[`Button`](xref:Android.Widget.Button)所參考時, 所顯示的影像將會根據這三個狀態而變更。


> [!NOTE]
> `<item>`元素的順序很重要。 參考此可繪製時, 會`<item>`依序進行, 以判斷哪一個適用于目前的按鈕狀態。
> 因為「標準」影像是最後的, 所以只會在條件`android:state_pressed`和`android:state_focused`同時評估為 false 時套用。

開啟**Resources/layout/axml**檔案, 並新增[`Button`](xref:Android.Widget.Button)元素:

```xml
<Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:padding="10dp"
        android:background="@drawable/android_button" />
```

屬性會指定要用於按鈕背景的可繪製資源 (當儲存在**資源/可繪製/android .xml**時, 會以形式`@drawable/android`參考)。 `android:background` 這會取代整個系統中用於按鈕的一般背景影像。 為了讓可繪製根據按鈕狀態變更其影像, 必須將影像套用至背景。

若要讓按鈕在按下時執行一些動作, 請在結尾處新增下列程式碼:[`OnCreate()`](xref:Android.App.Activity.OnCreate*)
方法

```csharp
Button button = FindViewById<Button>(Resource.Id.button);

button.Click += (o, e) => {
    Toast.MakeText (this, "Beep Boop", ToastLength.Short).Show ();
};
```

這會[`Button`](xref:Android.Widget.Button)從配置中捕捉, 然後[`Toast`](xref:Android.Widget.Toast)新增[`Button`](xref:Android.Widget.Button)要在按一下時顯示的訊息。

現在執行應用程式。


*此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改, 並根據*
[*創意 Commons 2.5 屬性授權*](http://creativecommons.org/licenses/by/2.5/)中所述的條款來使用。
