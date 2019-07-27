---
title: Xamarin Android 交換器
description: 如何在 Xamarin Android 應用程式中使用切換 widget
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: 7ff10433ffe11965ccfb8c9a46a785b8cb0304e6
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510177"
---
# <a name="xamarinandroid-switch"></a>Xamarin Android 交換器

`Switch`小工具 (如下所示) 可讓使用者在兩個狀態之間切換, 例如開啟或關閉。 `Switch`預設值為 OFF。 此 widget 的開啟和關閉狀態如下所示:

[![[關閉] 和 [開啟] 狀態的切換小工具螢幕擷取畫面](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>建立交換器

若要建立參數, 請直接`Switch`在 XML 中宣告元素, 如下所示:

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

這會建立基本參數, 如下所示:

[![示範應用程式的螢幕擷取畫面, 顯示處於關閉狀態的交換器](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>變更預設值

控制項針對 [開啟] 和 [關閉] 狀態所顯示的文字, 以及預設值都是可設定的。 例如, 若要讓參數預設為 ON, 並讀取 NO/YES 而不是 OFF/ON, 我們可以在`checked`下列`textOn`XML 中`textOff`設定、和屬性。

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>提供標題

小工具也支援藉由`text`設定屬性來包含文字標籤, 如下所示: `Switch`

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

此標記會在執行時間產生下列螢幕擷取畫面:

[![在切換 widget 前面加上文字的示範應用程式螢幕擷取畫面](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

當的值變更時, 它會`CheckedChange`引發事件。 `Switch`
例如, 在下列程式碼中, 我們`Toast`會使用以的`isChecked`值`Switch`為基礎的訊息來捕捉此事件並呈現一個小工具, 這會傳遞`CompoundButton.CheckedChangeEventArg`至事件處理常式做為引數的一部分。

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>相關連結

- [SwitchDemo (範例)](https://developer.xamarin.com/samples/monodroid/SwitchDemo/)
- [索引標籤版面配置教學課程](~/android/user-interface/layouts/tab-layout/index.md)
