---
title: "編輯文字"
ms.topic: article
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 280bb1ad8f717476ec425462a43a32c1f3eac381
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="edit-text"></a>編輯文字

在本節中，您將建立的使用者輸入，使用的文字欄位[EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/) widget。 一旦在欄位中輸入文字，"Enter"鍵會在快顯訊息中顯示文字。

開啟<code>Resources\layout\main.xml</code>檔案，然後加入[EditText](https://developer.xamarin.com/api/type/Android.Widget.EditText/)元素 (內[ `LinearLayout` ](https://developer.xamarin.com/api/type/Android.Widget.LinearLayout/)):

```xml
<pre><code class=" syntax brush-C#">&lt;EditText
    android:id="@+id/edittext"
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"/&gt;</code></pre>
```

若要運用使用者輸入時，將下列程式碼加入至結尾的文字[OnCreate](https://developer.xamarin.com/api/member/Android.App.Activity.OnCreate/)方法：

```csharp
EditText edittext = FindViewById<EditText>(Resource.Id.edittext);
edittext.KeyPress += (object sender, View.KeyEventArgs e) => {
 e.Handled = false;
 if (e.Event.Action == KeyEventActions.Down && e.KeyCode == Keycode.Enter) {
  Toast.MakeText (this, edittext.Text, ToastLength.Short).Show ();
  e.Handled = true;
         }
};
```

這會擷取[ `EditText` ](https://developer.xamarin.com/api/type/Android.Widget.EditText/)來自版面配置和集合的項目[ `KeyPress` ](https://developer.xamarin.com/api/event/Android.Views.View.KeyPress/)處理常式，定義可供此 widget 具有焦點時按下按鍵時的動作。 在此情況下，方法定義為接聽 Enter 鍵 （按下） 時，則快顯[ `Toast` ](https://developer.xamarin.com/api/type/Android.Widget.Toast/)已輸入的文字訊息。 [ `Handled` ](https://developer.xamarin.com/api/property/Android.Views.View+KeyEventArgs.Handled/)屬性應永遠為`true`如果事件已處理，如此事件不泡 （這會產生歸位字元的文字欄位中）。

執行應用程式。

*此頁面上的部分是修改依據所建立的工作和* [ *Android 的開放原始碼專案所共用*](http://code.google.com/policies.html) *和依據中所述的條款來使用* [ *2.5 的 c Commons Attribution 授權*](http://creativecommons.org/licenses/by/2.5/) *。本教學課程根據* [ *Android 表單東西教學課程*](http://developer.android.com/resources/tutorials/views/hello-formstuff.html) *。*



## <a name="related-links"></a>相關連結

- [EditTextSample](https://developer.xamarin.com/samples/monodroid/UserInterface/EditTextSample/)
