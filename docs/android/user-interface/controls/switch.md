---
title: Xamarin Android 交換器
description: 如何在 Xamarin Android 應用程式中使用切換 widget
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: 73becb5e4424854c9be6cdc3554f6cf93507b9a9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029131"
---
# <a name="xamarinandroid-switch"></a>Xamarin Android 交換器

`Switch` widget （如下所示）可讓使用者在兩個狀態之間切換，例如開啟或關閉。 `Switch` 預設值為 OFF。 此 widget 的開啟和關閉狀態如下所示：

[[關閉] 和 [開啟] 狀態的切換 widget![螢幕擷取畫面](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>建立交換器

若要建立參數，請直接在 XML 中宣告 `Switch` 元素，如下所示：

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

這會建立基本參數，如下所示：

[![示範應用程式的螢幕擷取畫面，顯示處於關閉狀態的交換器](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>變更預設值

控制項針對 [開啟] 和 [關閉] 狀態所顯示的文字，以及預設值都是可設定的。 例如，若要讓參數預設為 ON，並讀取 NO/YES 而不是 OFF/ON，我們可以在下列 XML 中設定 `checked`、`textOn`和 `textOff` 屬性。

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

## <a name="providing-a-title"></a>提供標題

`Switch` widget 也支援藉由設定 `text` 屬性來包含文字標籤，如下所示：

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

此標記會在執行時間產生下列螢幕擷取畫面：

[![螢幕擷取畫面，其中包含在切換 widget 前面的文字的示範應用程式](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

當 `Switch`的值變更時，就會引發 `CheckedChange` 事件。
例如，在下列程式碼中，我們會根據 `Switch`的 `isChecked` 值（傳遞至事件處理常式做為 `CompoundButton.CheckedChangeEventArg` 引數的一部分）來捕捉此事件並顯示 `Toast` widget。

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```

## <a name="related-links"></a>相關連結

- [SwitchDemo （範例）](https://docs.microsoft.com/samples/xamarin/monodroid-samples/switchdemo)
- [索引標籤版面配置教學課程](~/android/user-interface/layouts/tab-layout/index.md)
