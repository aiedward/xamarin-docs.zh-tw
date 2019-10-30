---
title: Android 上的協助工具
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/28/2018
ms.openlocfilehash: 6e86663be0bb06697fbfe0e8c360d733bca18da0
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017025"
---
# <a name="accessibility-on-android"></a>Android 上的協助工具

此頁面說明如何根據[協助工具檢查清單](~/cross-platform/app-fundamentals/accessibility.md)，使用 Android 協助工具 api 來建立應用程式。
如需其他平臺 Api，請參閱[iOS 協助工具](~/ios/app-fundamentals/accessibility.md)和[OS X 輔助](~/mac/app-fundamentals/accessibility.md)功能頁面。

## <a name="describing-ui-elements"></a>描述 UI 元素

Android 提供一個 `ContentDescription` 屬性，可供螢幕讀取 Api 用來提供控制項用途的可存取描述。

內容描述可以在 AXML 配置檔案中C#的或中設定。

**C#**

可以在程式碼中將描述設定為任何字串（或字串資源）：

```csharp
saveButton.ContentDescription = "Save data";
```

**AXML 版面配置**

在 XML 版面配置中，使用 `android:contentDescription` 屬性：

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>TextView 的 Use 提示

針對資料輸入的 `EditText` 和 `TextView` 控制項，請使用 `Hint` 屬性來提供預期的輸入（而不是 `ContentDescription`）的描述。
輸入部分文字時，文字本身將會是 "read"，而不是提示。

**C#**

在程式碼中設定 `Hint` 屬性：

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**AXML 版面配置**

在 XML 版面配置檔案中，使用 `android:hint` 屬性：

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```

### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor 會連結具有標籤的輸入欄位

若要將標籤與資料輸入控制項建立關聯，請使用 `LabelFor` 屬性來

**C#**

在C#中，將 [`LabelFor`] 屬性設為此內容所描述之控制項的資源識別碼（通常會在標籤上設定此屬性，並參考一些其他輸入控制項）：

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**AXML 版面配置**

在版面配置 XML 中，使用 `android:labelFor` 屬性來參考另一個控制項的識別碼：

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>公告以取得協助工具

當啟用協助工具時，在任何 view 控制項上使用 `AnnounceForAccessibility` 方法，將事件或狀態變更傳達給使用者。 大部分的作業都不需要這個方法，因為內建旁白會提供足夠的意見反應，但應使用於其他資訊對使用者很有説明的地方。

下列程式碼顯示呼叫 `AnnounceForAccessibility` 的簡單範例：

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>變更焦點設定

可存取的導覽依賴具有焦點的控制項，以協助使用者瞭解有哪些作業可供使用。 Android 提供 `Focusable` 屬性，可將控制項標記為在導覽期間特別能夠接收焦點。

**C#**

若要防止控制項取得焦點C#，請將 `Focusable` 屬性設定為 `false`：

```csharp
label.Focusable = false;
```

**AXML 版面配置**

在 [版面配置 XML 檔案] 中，設定 `android:focusable` 屬性：

```xml
<android:focusable="false" />
```

您也可以使用 `nextFocusDown`、`nextFocusLeft`、`nextFocusRight` `nextFocusUp` 屬性來控制焦點順序，通常是在版面配置 AXML 中設定。 使用這些屬性可確保使用者可以輕鬆地流覽螢幕上的控制項。

## <a name="accessibility-and-localization"></a>協助工具和當地語系化

在上述範例中，[提示] 和 [內容] 描述會直接設定為顯示值。 最好是使用**字串 .xml**檔案中的值，如下所示：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

使用字串檔案中C#的文字，如下所示和 AXML 配置檔案：

**C#**

不使用程式碼中的字串常值，而是從字串檔案中，使用 `Resources.GetText` 來查詢已翻譯的值：

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

在版面配置 XML 可存取性屬性（如 `hint` 和 `contentDescription`）可以設定為字串識別碼：

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

將文字儲存在不同檔案中的優點是可以在您的應用程式中提供檔案的多種語言翻譯。 請參閱[Android 當地語系化指南](~/android/app-fundamentals/localization.md)，以瞭解如何將當地語系化的字串檔案加入至應用程式專案。

## <a name="testing-accessibility"></a>測試協助工具

請遵循[下列步驟](https://developer.android.com/training/accessibility/testing.html#how-to)來啟用 TalkBack，並透過觸控來測試 Android 裝置上的協助工具。

You may need to install [TalkBack](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) from Google Play if it does not appear in **Settings > Accessibility**.

## <a name="related-links"></a>相關連結

- [跨平台協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [Android Accessibility APIs](https://developer.android.com/guide/topics/ui/accessibility/index.html)
