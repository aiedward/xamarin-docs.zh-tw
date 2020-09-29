---
title: Xamarin. Android 參數
description: 如何在 Xamarin Android 應用程式中使用切換小工具
ms.prod: xamarin
ms.assetid: 6E1F3324-EC41-454A-AEC0-0208813C7E50
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/29/2018
ms.openlocfilehash: dabd4b5bd7d6dbd118314e94fe04bc4623cf11e1
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457714"
---
# <a name="xamarinandroid-switch"></a>Xamarin. Android 參數

`Switch`以下顯示的小工具 (，) 可讓使用者在兩個狀態之間切換，例如開啟或關閉。 `Switch`預設值是 OFF。 此 widget 的開啟和關閉狀態如下所示：

[![切換小工具在 [關閉] 和 [開啟] 狀態的螢幕擷取畫面](switch-images/16-switch-onoff.png)](switch-images/16-switch-onoff.png#lightbox)

## <a name="creating-a-switch"></a>建立參數

若要建立參數，只要 `Switch` 在 XML 中宣告元素，如下所示：

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content" />
```

這會建立基本交換器，如下所示：

[![顯示處於關閉狀態之切換的示範應用程式螢幕擷取畫面](switch-images/07-switch.png)](switch-images/07-switch.png#lightbox)

## <a name="changing-default-values"></a>變更預設值

控制項顯示的 [開啟] 和 [關閉] 狀態的文字，以及預設值都是可設定的。 例如，若要讓切換開關預設為開啟，而不是關閉/開啟，我們可以 `checked` `textOn` `textOff` 在下列 XML 中設定、和屬性。

```xml
<Switch android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

## <a name="providing-a-title"></a>提供標題

`Switch`Widget 也支援藉由設定屬性來包含文字標籤，如下所示 `text` ：

```xml
<Switch android:text="Is Xamarin.Android great?"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:checked="true"
        android:textOn="YES"
        android:textOff="NO" />
```

此標記會在執行時間產生下列螢幕擷取畫面：

[![螢幕擷取畫面：在切換小工具前面有文字水準的示範應用程式](switch-images/08-switch.png)](switch-images/08-switch.png#lightbox)

當 `Switch` 值變更時，它會引發 `CheckedChange` 事件。
例如，在下列程式碼中，我們 `Toast` 會根據的值來捕捉此事件並顯示 widget `isChecked` `Switch` ，該訊息會傳遞至事件處理常式做為引數的一部分 `CompoundButton.CheckedChangeEventArg` 。

```csharp
Switch s = FindViewById<Switch> (Resource.Id.monitored_switch);
           
s.CheckedChange += delegate(object sender, CompoundButton.CheckedChangeEventArgs e) {
    var toast = Toast.MakeText (this, "Your answer is " +
        (e.IsChecked ?  "correct" : "incorrect"), ToastLength.Short);
    toast.Show ();
};
```

## <a name="related-links"></a>相關連結

- [SwitchDemo (範例) ](/samples/xamarin/monodroid-samples/switchdemo)
- [Tab 版面配置教學課程](~/android/user-interface/layouts/tab-layout/index.md)