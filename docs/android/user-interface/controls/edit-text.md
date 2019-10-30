---
title: 編輯文字
description: 如何使用 EditText 小工具來接受使用者輸入。
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/09/2018
ms.openlocfilehash: 62859e19c533d7b200833996b44a65cb275805c6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029255"
---
# <a name="xamarinandroid-edit-text"></a>Xamarin. Android 編輯文字

在本節中，您將使用[EditText](xref:Android.Widget.EditText)小工具來建立使用者輸入的文字欄位。 在欄位中輸入文字之後， **Enter**鍵會在快顯訊息中顯示文字。

開啟**Resources/layout/activity_main. axml** ，並將[EditText](xref:Android.Widget.EditText)專案新增至包含的版面配置。 下列範例**activity_main。 axml**具有已新增至 `LinearLayout`的 `EditText`：

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

在此程式碼範例中，`android:imeOptions` 的 `EditText` 屬性設定為 [`actionGo`]。 此設定會將預設的 [完成][動作變更為 [](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) [執行](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE)中] 動作，讓按**Enter**鍵會觸發 `KeyPress` 輸入處理常式。
（一般而言，會使用 `actionGo`，讓 Enter 鍵將使用者帶到**輸入**之 URL 的目標）。

若要處理使用者文字輸入，請將下列程式碼新增至**MainActivity.cs**中[OnCreate](xref:Android.App.Activity.OnCreate*)方法的結尾：

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

此外，在**MainActivity.cs**的頂端新增下列 `using` 語句（如果尚未存在）：

```csharp
using Android.Views;
```

這個程式碼範例會從配置中擴大[EditText](xref:Android.Widget.EditText)專案，並加入[KeyPress](xref:Android.Views.View.KeyPress)處理常式，以定義在 widget 具有焦點時按下按鍵時要進行的動作。 在此情況下，會定義方法來接聽**Enter**鍵（按下時），然後使用已輸入的文字來顯示快[顯訊息。](xref:Android.Widget.Toast) 請注意，如果事件已處理，則已[處理](xref:Android.Views.View.KeyEventArgs.Handled)的屬性應該一律 `true`。 這是防止事件反升（這會導致文字欄位中的回車）的必要專案。

執行應用程式，並在文字欄位中輸入一些文字。 當您按下**enter**鍵時，將會顯示快顯通知，如下所示：

[![在 EditText 中輸入文字的範例](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*此頁面的部分是根據* [*Android 開放原始碼專案*](https://code.google.com/policies.html)所建立和共用的工作進行修改，並根據[*創意 Commons 2.5 屬性授權*](https://creativecommons.org/licenses/by/2.5/)*中所述的條款來使用* *。本教學課程是*以[*Android 表單內容教學課程*](https://developer.android.com/resources/tutorials/views/hello-formstuff.html)為基礎 *。*

## <a name="related-links"></a>相關連結

- [EditTextSample](https://docs.microsoft.com/samples/xamarin/monodroid-samples/userinterface-edittextsample)
