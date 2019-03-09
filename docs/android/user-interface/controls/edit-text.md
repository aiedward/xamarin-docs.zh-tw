---
title: 編輯文字
description: 如何使用接受使用者輸入的 EditText 小工具。
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/09/2018
ms.openlocfilehash: d42cec1ee0939bead9ede83a042f5b6cbb5298cd
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57670866"
---
# <a name="edit-text"></a>編輯文字

在本節中，您將使用[EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/)小工具建立的使用者輸入的文字欄位。 一旦在欄位中，輸入文字後**Enter**金鑰會顯示快顯訊息中的文字。

開啟**Resources/layout/activity_main.axml**並加入[EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/)要包含的版面配置項目。 下例**activity_main.axml**已`EditText`已加入至`LinearLayout`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <EditText
        android:id="@+id/edittext"
        android:layout_width="match_parent"
        android:imeOptions="actionGo"
        android:inputType="text"
        android:layout_height="wrap_content" />
</LinearLayout>
```

在此範例中，`EditText`屬性`android:imeOptions`設定為`actionGo`。 此設定會變更預設值[完成](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE)動作來[移](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO)動作以便點選**Enter**金鑰觸發程序`KeyPress`輸入處理常式。
(通常`actionGo`可讓**Enter**索引鍵會讓使用者存取 URL 中輸入的目標。)

若要處理使用者輸入文字，將下列程式碼新增至結尾[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/)方法中的**MainActivity.cs**:

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
    e.Handled = false;
    if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) 
    {
        Toast.MakeText(this, edittext.Text, ToastLength.Short).Show();
        e.Handled = true;
    }
};
```

此外，新增下列`using`頂端的陳述式**MainActivity.cs**如果尚不存在：

```csharp
using Android.Views;
```

此程式碼範例會擴大[EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/)版面配置項目，並將[KeyPress](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/)定義的動作，此 widget 具有焦點時按下按鍵時的處理常式。 在此情況下，方法定義來接聽**Enter**鍵 （當點選），然後快顯[快顯](https://developer.xamarin.com/api/type/Android.Widget.Toast/)已輸入的文字訊息。 請注意， [Handled](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/)屬性應該一律是`true`如果在處理事件。 這是為了防止事件反昇向上 （這會產生歸位字元的文字欄位中）。

執行應用程式，並在文字欄位中輸入一些文字。 當您按下**Enter**索引鍵、 快顯通知將會顯示在右側所示：

[![文字輸入 EditText 範例](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*此頁面上的部分是根據所建立的工作修改並* [ *Android 的開放原始碼專案所共用*](http://code.google.com/policies.html) *和依據中所述的條款來使用* [ *2.5 的 creative Commons Attribution License* ](http://creativecommons.org/licenses/by/2.5/) *。本教學課程根據* [ *Android 表單項目教學課程*](https://developer.android.com/resources/tutorials/views/hello-formstuff.html) *。*


## <a name="related-links"></a>相關連結

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
