---
title: 參數
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 0f4bfc3646f1ccd956ee8151468b3de20f6e1e2b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="switch"></a>參數

`Switch` Widget （如下所示） 可讓使用者切換兩種狀態，例如，在或 [關閉]。 `Switch`預設值是 OFF。 如下所示的小工具，在其 ON 和 OFF 的狀態：

[![交換器 widget 處於關閉狀態的螢幕擷取畫面](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)


## <a name="creating-a-switch"></a>建立參數

若要建立參數，只宣告`Switch`xml 項目，如下所示：

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

這會建立基本參數，如下所示：

[![在關閉狀態中顯示參數的示範應用程式的螢幕擷取畫面](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)


## <a name="changing-default-values"></a>變更預設值

控制項會顯示為 ON 或 OFF 狀態的文字和預設值是可設定。 例如，若要切換預設設為 ON，以及讀取否 / [是]，而不是 OFF/ON，我們可以設定`checked`， `textOn`，和`textOff`下列 XML 中的屬性。

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>提供標題

`Switch` Widget，也支援藉由設定包括文字標籤`text`屬性，如下所示：

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

這個標記會產生在執行階段的下列螢幕擷取畫面：

[![示範應用程式文字的水平前面 Switch 小工具的螢幕擷取畫面](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

當`Switch`的值變更，便會產生`CheckedChange`事件。
例如，下列程式碼我們擷取此事件並呈現`Toast`widget 以訊息為基礎`isChecked`值`Switch`，傳遞給事件處理常式的一部分`CompoundButton.CheckedChangeEventArg`引數。

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>相關連結

- [SwitchDemo （範例）](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ICS_Samples/SwitchDemo/)
- [教學課程中的版面配置 索引標籤](~/android/user-interface/layouts/tab-layout/index.md)
- [介紹的冰淇淋三明治](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 平台](http://developer.android.com/sdk/android-4.0.html)
