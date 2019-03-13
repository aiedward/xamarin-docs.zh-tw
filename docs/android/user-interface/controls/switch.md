---
title: 參數
description: 如何在 Xamarin.Android 應用程式中使用參數的小工具
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/29/2018
ms.openlocfilehash: ef400aaa31992b577762ad695418b865882e2e2d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115954"
---
# <a name="switch"></a>參數

`Switch` Widget （如下所示） 可讓使用者切換兩個狀態，例如，在] 或 [關閉。 `Switch`預設值是 OFF。 如下所示的小工具，在其 ON 和 OFF 的狀態：

[![交換器 widget 或開啟狀態的螢幕擷取畫面](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)


## <a name="creating-a-switch"></a>建立交換器

若要建立的交換器，只是宣告`Switch`中 XML 項目，如下所示：

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

這會建立基本的參數，如下所示：

[![在 「 關閉 」 狀態中顯示交換器的示範應用程式的螢幕擷取畫面](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)


## <a name="changing-default-values"></a>變更預設值

控制項會顯示為 ON 或 OFF 狀態的文字和預設值是可設定。 例如，若要讓此參數預設為 ON，以及讀取否 / 是，而不是 OFF/ON，我們可以設定`checked`， `textOn`，和`textOff`下列 XML 程式碼中的屬性。

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```



## <a name="providing-a-title"></a>提供標題

`Switch`小工具也支援設定，包括文字標籤`text`屬性，如下所示：

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

此標記會產生在執行階段的下列螢幕擷取畫面：

[![使用文字水平前面參數的小工具示範應用程式的螢幕擷取畫面](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

當`Switch`的值變更時，便會產生`CheckedChange`事件。
例如，下列程式碼中我們擷取此事件，並呈現`Toast`訊息的小工具根據`isChecked`的值`Switch`，傳遞至事件處理常式的一部分`CompoundButton.CheckedChangeEventArg`引數。

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```


## <a name="related-links"></a>相關連結

- [SwitchDemo （範例）](https://developer.xamarin.com/samples/monodroid/SwitchDemo/)
- [教學課程中的版面配置 索引標籤](~/android/user-interface/layouts/tab-layout/index.md)
