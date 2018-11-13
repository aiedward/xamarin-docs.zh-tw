---
title: 在 Android 上的協助工具
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 3cce3270b9df2aad0037b1ab96f169cc4b564766
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528126"
---
# <a name="accessibility-on-android"></a>在 Android 上的協助工具

此頁面說明如何使用 Android 的協助工具 Api 來建置應用程式，根據[協助工具檢查清單](~/cross-platform/app-fundamentals/accessibility.md)。
請參閱[iOS 協助工具](~/ios/app-fundamentals/accessibility.md)並[OS X 的協助工具](~/mac/app-fundamentals/accessibility.md)其他平台 Api 的頁面。


## <a name="describing-ui-elements"></a>描述的 UI 項目

Android 提供`ContentDescription`供螢幕助讀 Api 用來提供控制項的用途的可存取描述的屬性。

可以設定內容的描述，在C#或 AXML 版面配置檔案中。

**C#**

描述可以在任何字串 （或字串資源） 的程式碼中設定：

```csharp
saveButton.ContentDescription = "Save data";
```

**AXML 版面配置**

在 XML 中的版面配置使用`android:contentDescription`屬性：

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>TextView 的 use Hint

針對`EditText`並`TextView`資料輸入的控制項使用`Hint`屬性，以提供預期輸入的描述 (而不是`ContentDescription`)。
在輸入一些文字，文字本身將會是 「 讀取 」 而不是提示。

**C#**

設定`Hint`在程式碼中的屬性：

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**AXML 版面配置**

在 XML 中使用版面配置檔`android:hint`屬性：

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor 連結輸入標籤的欄位

若要關聯資料的輸入控制項中的標籤，使用`LabelFor`屬性

**C#**

在C#，將`LabelFor`屬性可描述此內容之控制項的資源識別碼 （通常這個屬性被設定在標籤上，參考其他輸入的控制項）：

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**AXML 版面配置**

在配置中使用 XML`android:labelFor`來參考另一個控制項的識別項的屬性：

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>宣布的協助工具

使用`AnnounceForAccessibility`任何方法檢視通訊使用者事件或狀態變更時啟用協助工具的控制項。 這個方法就不需要進行大部分的操作，其中的內建的旁白提供足夠的意見反應，但應該適用於會很有幫助使用者的其他資訊。

下列程式碼示範簡單的範例呼叫`AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>變更焦點設定

可存取的導覽會依賴控制項具有焦點，以協助使用者了解哪些作業。 Android 提供`Focusable`可以標記為特別能夠在巡覽期間接收焦點的控制項屬性。

**C#**

若要防止控制項取得焦點C#，將`Focusable`屬性，以`false`:

```csharp
label.Focusable = false;
```

**AXML 版面配置**

在配置中的 XML 檔案集`android:focusable`屬性：

```xml
<android:focusable="false" />
```

您也可以控制與焦點順序`nextFocusDown`， `nextFocusLeft`， `nextFocusRight`，`nextFocusUp`通常 AXML 版面配置中設定的屬性。 使用這些屬性，以確保使用者可以輕鬆地瀏覽螢幕上的控制項。


## <a name="accessibility-and-localization"></a>協助工具和當地語系化

上述範例中的提示和內容描述會直接設定為顯示值。 建議您最好使用中的值**Strings.xml**檔案，如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

使用文字字串檔案如下所示的C#和 AXML 版面配置檔案：

**C#**

而不是在程式碼中使用字串常值，查詢已翻譯值從字串檔案`Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

配置 XML 中的協助工具屬性喜歡`hint`和`contentDescription`可以設定為字串識別項：

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

將文字儲存在個別的檔案的優點是您的應用程式可以提供多個語言翻譯的檔案。 請參閱[Android 當地語系化指南](~/android/app-fundamentals/localization.md)若要了解如何將當地語系化的字串的檔案新增至應用程式專案。


## <a name="testing-accessibility"></a>測試協助工具

請遵循[這些步驟](http://developer.android.com/training/accessibility/testing.html#how-to)啟用 TalkBack，以及透過觸控的瀏覽測試在 Android 裝置上的協助工具。

您可能需要安裝[TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback)從 Google Play，如果它不會出現在**設定 > 存取範圍**。


## <a name="related-links"></a>相關連結

- [跨平台的協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [Android 的協助工具 Api](http://developer.android.com/guide/topics/ui/accessibility/index.html)
