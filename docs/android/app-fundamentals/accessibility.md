---
title: 在 Android 上的協助工具
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/28/2018
ms.openlocfilehash: 2a49d15651b8c6ab7417a69d934af5d20bfc13d0
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763900"
---
# <a name="accessibility-on-android"></a>在 Android 上的協助工具

此頁面描述如何使用 Android 的協助工具應用程式開發介面建置應用程式根據[網頁可及性檢查清單](~/cross-platform/app-fundamentals/accessibility.md)。
請參閱[iOS 協助工具](~/ios/app-fundamentals/accessibility.md)和[OS X 的協助工具](~/mac/app-fundamentals/accessibility.md)其他平台應用程式開發介面的頁面。


## <a name="describing-ui-elements"></a>描述 UI 項目

Android 提供`ContentDescription`屬性，供螢幕助讀 Api 用來提供存取控制項的用途的描述。

在 C# 或 AXML 配置檔案中，可以設定內容的描述。

**C#**

描述可以設定任何字串 （或字串資源） 的程式碼中：

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

### <a name="use-hint-for-textview"></a>TextView 使用提示

如`EditText`和`TextView`資料輸入的控制項使用`Hint`屬性以提供預期何種輸入的描述 (而不是`ContentDescription`)。
輸入一些文字時文字本身會是 「 讀取 」 而不是此提示。

**C#**

設定`Hint`程式碼中的屬性：

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**AXML 版面配置**

在 XML 中配置檔案使用`android:hint`屬性：

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```


### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor 連結輸入標籤的欄位

若要將標籤與輸入控制項的資料產生關聯，使用`LabelFor`屬性

**C#**

在 C# 中，設定`LabelFor`資源識別碼，這是此內容控制項的屬性描述 （通常可在標籤上設定和一些其他的輸入的控制項的參考這個屬性）：

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**AXML 版面配置**

在配置中使用 XML`android:labelFor`屬性來參考另一個控制項的識別項：

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>通知協助工具

使用`AnnounceForAccessibility`方法在任何檢視通訊的使用者事件或狀態變更時已啟用協助工具的控制項。 這個方法不需要內建的旁白其中提供足夠的意見反應，但其中的其他資訊會很有幫助使用者應該使用的大部分作業。

下列程式碼示範簡單的範例呼叫`AnnounceForAccessibility`:

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>變更焦點設定

可存取的巡覽依賴控制項具有焦點，以協助使用者了解哪些作業。 Android 提供`Focusable`可以標記與特別能夠瀏覽時接收焦點的控制項屬性。

**C#**

若要防止控制項取得焦點，使用 C#，請設定`Focusable`屬性`false`:

```csharp
label.Focusable = false;
```

**AXML 版面配置**

版面配置中的 XML 檔案組`android:focusable`屬性：

```xml
<android:focusable="false" />
```

您也可以控制焦點順序與`nextFocusDown`， `nextFocusLeft`， `nextFocusRight`，`nextFocusUp`通常配置 AXML 中設定的屬性。 使用這些屬性，以確保使用者可以輕鬆地瀏覽螢幕上的控制項。


## <a name="accessibility-and-localization"></a>協助工具和當地語系化

上述範例中的提示與內容描述會直接設定為顯示值。 最好是使用中的值**Strings.xml**檔案，這類：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

使用文字字串檔案如下所示在 C# 和 AXML 版面配置檔案：

**C#**

而不是在程式碼中使用字串常值，查詢已翻譯值字串以從檔案`Resources.GetText`:

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

協助工具屬性要在配置 XML`hint`和`contentDescription`可設為字串識別項：

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

將文字儲存在個別檔案的優點是多個語言翻譯的檔案可供您的應用程式。 請參閱[Android 當地語系化指南](~/android/app-fundamentals/localization.md)若要了解如何將當地語系化的字串的檔案新增至應用程式專案。


## <a name="testing-accessibility"></a>測試協助工具

請遵循[步驟](http://developer.android.com/training/accessibility/testing.html#how-to)啟用 TalkBack 和觸控的瀏覽測試在 Android 裝置上的協助工具。

您可能需要安裝[TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback)從 Google Play，如果它不會顯示在**設定 > 協助工具**。


## <a name="related-links"></a>相關連結

- [跨平台的協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [Android 的協助工具應用程式開發介面](http://developer.android.com/guide/topics/ui/accessibility/index.html)
