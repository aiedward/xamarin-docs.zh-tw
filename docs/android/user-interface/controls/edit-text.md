---
title: 編輯文字
description: 如何使用 EditText 小工具接受使用者輸入。
ms.prod: xamarin
ms.assetid: E513BCBC-438E-15E8-B83A-4B768A8E8B32
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/09/2018
ms.openlocfilehash: fb29478791c97028ff4d62f97922c672f7a8b17b
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457249"
---
# <a name="xamarinandroid-edit-text"></a>Xamarin. Android 編輯文字

在本節中，您將使用 [EditText](xref:Android.Widget.EditText) 小工具來建立使用者輸入的文字欄位。 在欄位中輸入文字後， **Enter** 鍵會在快顯訊息中顯示文字。

開啟 [資源]/[配置] **/activity_main .axml** ，並將 [EditText](xref:Android.Widget.EditText) 專案加入至包含的配置。 下列範例 **activity_main .axml** 具有已 `EditText` 新增至的 `LinearLayout` ：

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

在此程式碼範例中， `EditText` 屬性 `android:imeOptions` 會設定為 `actionGo` 。 這項設定會將預設的 [ [完成](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_DONE) ] 動作變更為 [ [移至](https://developer.android.com/reference/android/view/inputmethod/EditorInfo#IME_ACTION_GO) ] 動作，讓您可以按 **Enter** 鍵來觸發 `KeyPress` 輸入處理常式。
通常 `actionGo` 會使用 (，讓 **Enter** 鍵將使用者帶到輸入的 URL 目標。 ) 

若要處理使用者文字輸入，請在**MainActivity.cs**中將下列程式碼新增至[>oncreate](xref:Android.App.Activity.OnCreate*)方法的結尾：

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

此外，將下列語句新增 `using` 至 **MainActivity.cs** 頂端（如果尚未存在）：

```csharp
using Android.Views;
```

這個程式碼範例會從配置中擴大 [EditText](xref:Android.Widget.EditText) 元素，並加入一個 [KeyPress](xref:Android.Views.View.KeyPress) 處理常式，此處理程式會定義在 widget 具有焦點時按下按鍵時要進行的動作。 在此情況下，方法會定義為在) 時接聽**Enter**鍵 (，然後使用已輸入的文字來顯示快[顯訊息。](xref:Android.Widget.Toast) 請注意， [Handled](xref:Android.Views.View.KeyEventArgs.Handled) `true` 如果事件已處理，則處理的屬性應該一律為。 這是為了防止事件反升 (這會導致文字欄位) 傳回換行的必要專案。

執行應用程式，並在文字欄位中輸入一些文字。 當您按下 **enter** 鍵時，就會顯示快顯通知，如右邊所示：

[![將文字輸入 EditText 的範例](edit-text-images/edit-text-sml.png)](edit-text-images/edit-text.png#lightbox)

*此頁面的部分是根據 Android 開放原始碼專案所建立和共用的工作進行修改，並根據*[*創意 Commons 2.5 屬性授權*](https://creativecommons.org/licenses/by/2.5/)中所述的條款來使用 *。本教學課程是*以[*Android 表單內容教學課程*](https://developer.android.com/resources/tutorials/views/hello-formstuff.html)為基礎 *。*

## <a name="related-links"></a>相關連結

- [EditTextSample](/samples/xamarin/monodroid-samples/userinterface-edittextsample)