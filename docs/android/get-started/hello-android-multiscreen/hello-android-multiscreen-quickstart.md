---
title: Hello, Android 多重畫面：快速入門
description: 這份兩個部分的指南會展開 Phoneword 應用程式以處理第二個畫面。 過程中，會介紹基本 Android 應用程式建置組塊，並深入剖析 Android 架構。
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: ED99584A-BA3B-429A-AEE5-CF3CB0116762
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/05/2018
ms.openlocfilehash: d1a4d73242e9701ff9ffcfc58791986bca89a095
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755841"
---
# <a name="hello-android-multiscreen-quickstart"></a>Hello, Android 多重畫面：快速入門

_這份含有兩部分的指南會擴充 Phoneword 應用程式來處理第二個畫面。在此過程中，會介紹基本 Android 應用程式建置組塊，並深入剖析 Android 架構。_

在本指南的逐步解說部分，您會在 [Phoneword](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword) 應用程式中新增第二個畫面，以便持續追蹤使用應用程式翻譯之號碼的記錄。 [最終應用程式](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phonewordmultiscreen)將具有第二個畫面來顯示「已翻譯」的號碼，如右邉螢幕擷取畫面所示：

[![範例應用程式螢幕擷取畫面](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

所附的[深入剖析](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md)會檢閱建置的內容並討論架構、導覽和在過程中遇到的其他新 Android 概念。

## <a name="requirements"></a>需求

因為本指南接續 [Hello, Android](~/android/get-started/hello-android/index.md) 結束之處，所以它需要您先完成 [Hello, Android 快速入門](~/android/get-started/hello-android/hello-android-quickstart.md)。
如果您想要直接跳至下面的逐步解說，可以下載完成版本的 [Phoneword](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword) (從 Hello, Android 快速入門) 並用它來開始本逐步解說。

## <a name="walkthrough"></a>逐步解說

在此逐步解說中，您將新增 [Translation History] (翻譯記錄) 畫面到 **Phoneword** 應用程式。

::: zone pivot="windows"

先在 Visual Studio 開啟 **Phoneword** 應用程式，並從方案總管編輯 **Main.axml** 檔案。

> [!TIP]
> 較新版的 Visual Studio 支援在 Android Designer 中開啟 .xml 檔案。
>
> Android Designer 同時支援 .axml 和 .xml 檔案。

### <a name="updating-the-layout"></a>更新版面配置

從 [工具箱]，將**按鈕**拖曳至設計介面，並將它放置於 **TranslatedPhoneWord** 文字檢視下方。 在 [屬性] 窗格中，將按鈕 **Id** 變更為 `@+id/TranslationHistoryButton`

[![拖曳新按鈕](hello-android-multiscreen-quickstart-images/vs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/vs/02-new-button.png#lightbox)

將按鈕的 **Text** 屬性設為 `@string/translationHistory`。 Android Designer 會按字面解譯它，但您要進行一些變更，讓按鈕的文字可正確顯示：

[![設定翻譯記錄按鈕文字](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string-sml.png)](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string.png#lightbox)

在方案總管中，展開 [資源] 資料夾下的 [值] 節點，然後連按兩下字串資源檔 **Strings.xml**：

[![開啟 Strings.xml](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file.png#lightbox)

將 `translationHistory` 字串名稱和值新增至 **Strings.xml** 檔案，並加以儲存：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

[Translation History] (翻譯記錄) 按鈕文字應該更新以反映新的字串值：

[![按鈕會反映新的字串值](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png)](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png#lightbox)

在設計介面上選取 [Translation History] (翻譯記錄) 按鈕、在 [屬性] 窗格尋找 `enabled` 設定，並將其值設定為 `false` 來停用按鈕。 這會導致按鈕在設計介面上變得較暗：

[![停用翻譯記錄按鈕](hello-android-multiscreen-quickstart-images/vs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/vs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>建立第二個活動

建立第二個活動，以啟動第二個畫面。 在方案總管中，以滑鼠右鍵按一下 **Phoneword** 專案，然後選取 [新增] > [新增項目...]：

[![新增檔案](hello-android-multiscreen-quickstart-images/vs/07-add-new-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/07-add-new-file.png#lightbox)

在 [新增項目] 對話方塊中，選擇 [Visual C#] > [活動]，並將活動檔案命名為 **TranslationHistoryActivity.cs**。

使用下列程式碼取代 **TranslationHistoryActivity.cs** 中的範本程式碼：

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

在此類別中，您會建立 `ListActivity`，然後以程式設計的方式填入它，因此您不需要為這個活動建立新的版面配置檔案。 這會在 [Hello, Android 多重畫面深度剖析](~/android/get-started/hello-android/hello-android-deepdive.md)中更詳細地討論。

### <a name="adding-a-list"></a>新增清單

此應用程式會收集電話號碼 (使用者已在第一個畫面中翻譯)，然後將它們傳送至第二個畫面。 電話號碼會儲存為字串清單。 若要支援清單 (及稍後使用的意圖)，請將下列 `using` 指示詞新增至 **MainActivity.cs** 的頂端：

```csharp
using System.Collections.Generic;
using Android.Content;
```

接下來，建立可以填入電話號碼的空白清單。
`MainActivity` 類別會顯示如下：

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

在 `MainActivity` 類別中，新增下列程式碼以註冊 [Translation History] (翻譯記錄) 按鈕 (將此行放在 `translateButton` 宣告之後)：

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

將下列程式碼新增至 `OnCreate` 方法的結尾，以連接 [Translation History] (翻譯記錄) 按鈕：

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

更新 [翻譯] 按鈕，將電話號碼新增至 `phoneNumbers` 清單。 `translateButton` 的 `Click` 處理常式應類似於下列程式碼：

```csharp
// Add code to translate number
string translatedNumber = string.Empty;
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

儲存並建置應用程式，以確定沒有任何錯誤。

### <a name="running-the-app"></a>執行應用程式

將應用程式部署到模擬器或裝置。 下列螢幕擷取畫面說明執行中的 **Phoneword** 應用程式：

[![螢幕擷取畫面範例](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end
::: zone pivot="macos"

先在 Visual Studio for Mac 開啟 **Phoneword** 專案，並從 **Solution Pad** 編輯 **Main.axml** 檔案。

> [!TIP]
> 較新版的 Visual Studio 支援在 Android Designer 中開啟 .xml 檔案。
>
> Android Designer 同時支援 .axml 和 .xml 檔案。

### <a name="updating-the-layout"></a>更新版面配置

從 [工具箱]，將**按鈕**拖曳至設計介面，並將它放置於 **TranslatedPhoneWord** 文字檢視下方。 在 **Properties** Pad 中，將按鈕**識別碼**變更為 `@+id/TranslationHistoryButton`：

[![拖曳新按鈕](hello-android-multiscreen-quickstart-images/xs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/xs/02-new-button.png#lightbox)

將按鈕的 **Text** 屬性設為 `@string/translationHistory`。 Android Designer 會按字面解譯它，但您要進行一些變更，讓按鈕的文字可正確顯示：

[![設定翻譯記錄按鈕文字](hello-android-multiscreen-quickstart-images/xs/03-call-history-string-sml.png)](hello-android-multiscreen-quickstart-images/xs/03-call-history-string.png#lightbox)

在 **Solution Pad** 中，展開 [資源] 資料夾下的 [值] 節點，然後連按兩下字串資源檔 **Strings.xml**：

[![開啟字串](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file.png#lightbox)

將 `translationHistory` 字串名稱和值新增至 **Strings.xml** 檔案，並加以儲存：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

[Translation History] (翻譯記錄) 按鈕文字應該更新以反映新的字串值：

[![按鈕會反映新的字串值](hello-android-multiscreen-quickstart-images/xs/05-new-string-value-sml.png)](hello-android-multiscreen-quickstart-images/xs/05-new-string-value.png#lightbox)

在設計介面上選取 [Translation History] (翻譯記錄) 按鈕，在  **Properties Pad** 中開啟 [行為] 索引標籤，然後按兩下 [已啟用] 核取方塊以停用按鈕。 這會導致按鈕在設計介面上變得較暗：

[![停用翻譯記錄按鈕](hello-android-multiscreen-quickstart-images/xs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/xs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>建立第二個活動

建立第二個活動，以啟動第二個畫面。 在 **Solution Pad** 中，按一下 **Phoneword** 專案旁的灰色齒輪圖示，然後選擇 [新增] > [新增檔案...]：

從 [新增檔案] 對話方塊中，選擇 [Android] > [活動]、將活動命名為 `TranslationHistoryActivity`，然後按一下 [新增]。

使用下列程式碼取代 `TranslationHistoryActivity` 中的範本程式碼：

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

在此類別中，會建立 `ListActivity`，然後以程式設計的方式填入它，因此您不必為這個活動建立新的版面配置檔案。 這會在 [Hello, Android 多重畫面深度剖析](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md)中更詳細地說明。

### <a name="adding-a-list"></a>新增清單

此應用程式會收集電話號碼 (使用者已在第一個畫面中翻譯)，然後將它們傳送至第二個畫面。 電話號碼會儲存為字串清單。 若要支援清單 (及稍後使用的意圖)，請將下列 `using` 指示詞新增至 **MainActivity.cs** 的頂端：

```csharp
using System.Collections.Generic;
using Android.Content;
```

接下來，建立可以填入電話號碼的空白清單。 `MainActivity` 類別會顯示如下：

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

在 `MainActivity` 類別中，新增下列程式碼以註冊 [Translation History] (翻譯記錄) 按鈕 (將此行放在 `TranslationHistoryButton` 宣告之後)：

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

將下列程式碼新增至 `OnCreate` 方法的結尾，以連接 [Translation History] (翻譯記錄) 按鈕：

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

更新 [翻譯] 按鈕，將電話號碼新增至 `phoneNumbers` 清單。 `TranslateHistoryButton` 的 `Click` 處理常式應類似於下列程式碼：

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

### <a name="running-the-app"></a>執行應用程式

將應用程式部署到模擬器或裝置。 下列螢幕擷取畫面說明執行中的 **Phoneword** 應用程式：

[![螢幕擷取畫面範例](hello-android-multiscreen-quickstart-images/screenshot.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end

恭喜您完成第一個多重畫面的 Xamarin.Android 應用程式！ 現在是時候仔細分析您剛解到的工具與技能 &ndash; 接下來是 [Hello, Android 多重畫面深度剖析](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md)。

## <a name="related-links"></a>相關連結

- [Xamarin 應用程式圖示和啟動畫面 (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword)
- [PhonewordMultiscreen (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phonewordmultiscreen)
