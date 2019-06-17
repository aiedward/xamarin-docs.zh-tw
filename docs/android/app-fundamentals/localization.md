---
title: Android 的當地語系化
description: 本文件介紹 Android SDK 以及如何加以存取，而 Xamarin 的當地語系化功能。
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 1f3e6dcc81c75d12a1dbba8fefc62a85178c8c24
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61019204"
---
# <a name="android-localization"></a>Android 的當地語系化

_本文件介紹 Android SDK 以及如何加以存取，而 Xamarin 的當地語系化功能。_

## <a name="android-platform-features"></a>Android 平台功能

本章節描述 Android 的主要當地語系化功能。 請跳至[下一節](#basics)若要查看特定的程式碼和範例。

### <a name="locale"></a>地區設定

使用者選擇其語言**設定 > 語言和輸入**。 此選項會控制顯示的語言和地區設定使用 （例如。 日期和數字格式）。

可查詢目前的地區設定，透過目前的內容`Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

這個值會包含語言程式碼和地區設定代碼，底線隔開的地區設定識別碼。 如需參考，以下是[Java 地區設定清單](https://www.oracle.com/technetwork/java/javase/locales-137662.html)並[Android 支援的地區設定透過 StackOverflow](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android)。

常見範例包括︰

* `en_US` 適用於英文 （美國）
* `es_ES` 西班牙文 （西班牙）
* `ja_JP` 日文 （日本）
* `zh_CN` 中文 （中國）
* `zh_TW` 中文 （台灣）
* `pt_PT` 葡萄牙文 （葡萄牙）
* `pt_BR` 葡萄牙文 （巴西）

### <a name="localechanged"></a>LOCALE_CHANGED

Android 會產生`android.intent.action.LOCALE_CHANGED`當使用者變更其語言選取項目。

活動可以選擇藉由設定處理此`android:configChanges`屬性上的活動，像這樣：

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>在 Android 中的國際化基本概念

Android 的本地化策略具有下列索引鍵部分：

- 包含當地語系化的字串、 影像和其他資源的資源資料夾。

- `GetText` 用來擷取當地語系化的字串，在程式碼中的方法

- `@string/id` 在 AXML 檔案中，自動將當地語系化的字串放在版面配置中。

### <a name="resource-folders"></a>資源資料夾

Android 應用程式管理資源資料夾中的大部分內容，例如：

* **版面配置**-包含 AXML 版面配置檔。
* **可繪製**-包含影像和其他可繪製資源。
* **值**-包含字串。
* **原始**-包含資料檔案。

大部分的開發人員已熟悉使用**dpi**在字尾**drawable**目錄所提供的映像，讓 Android 選擇正確的版本，每個裝置的多個版本。 提供多種語言的翻譯上尾碼語言和文化特性識別項的資源目錄，會使用相同的機制。

![多個文化特性識別項的可繪製資源/和資源/值資料夾的螢幕擷取畫面](localization-images/resources.png)

> [!NOTE]
> 指定這類的最上層語言時`es`只有兩個字元是必要項; 不過，在指定完整的地區設定時，目錄名稱格式需要虛線和小寫**r**來分隔的兩個部分，例如**pt rBR**或是**zh rCN**。 傳回在程式碼，（例如有底線的值比較。 `pt_BR`)。 這些都是不同的值.NET`CultureInfo`類別使用，其中包含以連字號只能 （例如。 `pt-BR`)。 使用 Xamarin 平台時，請記住這些差異。

#### <a name="stringsxml-file-format"></a>Strings.xml 檔案格式

當地語系化**值**目錄 （例如。 **值 es**或是**值-pt-rBR**) 應該包含檔案，稱為**Strings.xml** ，就會包含該地區設定翻譯的文字。

每個可翻譯的字串是 XML 項目與指定的資源識別碼做`name`屬性和翻譯的字串，做為值：

```xml
<string name="app_name">TaskyL10n</string>
```

您必須根據一般的 XML 規則逸出和`name`必須是有效的 Android 資源識別碼 （不含空格或連字號）。 如需範例預設 （英文） 的字串檔的範例如下：

**values/Strings.xml**

```xml
<resources>
    <string name="app_name">TaskyL10n</string>
    <string name="taskadd">Add Task</string>
    <string name="taskname">Name</string>
    <string name="tasknotes">Notes</string>
    <string name="taskdone">Done</string>
    <string name="taskcancel">Cancel</string>
</resources>
```

西班牙文的目錄**值 es**包含具有相同名稱的檔案 (**Strings.xml**)，其中包含的翻譯：

**values-es/Strings.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">TaskyLeon</string>
    <string name="taskadd">agregar tarea</string>
    <string name="taskname">Nombre</string>
    <string name="tasknotes">Notas</string>
    <string name="taskdone">Completo</string>
    <string name="taskcancel">Cancelar</string>
</resources>
```

![螢幕擷取畫面的多個值的資料夾，每一個都會包含 Strings.xml 檔案](localization-images/values.png)

使用字串的檔案設定妥當，翻譯的值，可參考版面配置和程式碼中。

### <a name="axml-layout-files"></a>AXML 版面配置檔

若要參考的版面配置檔案中的當地語系化的字串，請使用`@string/id`語法。 此範例會示範這個 XML 程式碼片段`text`要設定與屬性的當地語系化資源識別碼 （已省略某些其他屬性）：

```xml
<TextView
    android:id="@+id/NameLabel"
    android:text="@string/taskname"
    ... />
<CheckBox
    android:id="@+id/chkDone"
    android:text="@string/taskdone"
    ... />
```

### <a name="gettext-method"></a>GetText 方法

若要擷取翻譯的字串，在程式碼中，使用`GetText`方法並傳遞的資源識別碼：

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>數量的字串

Android 的字串資源也可讓您建立*quantity 字串*可讓翻譯人員提供不同的轉譯，對於不同的數量，例如：

* 「 沒有剩下 1 個工作。 」
* 「 有 2 工作仍在執行。 」

(而不是泛型"There are n 個工作左")。

在  **Strings.xml**

```xml
<plurals name="numberOfTasks">
   <!--
      As a developer, you should always supply "one" and "other"
      strings. Your translators will know which strings are actually
      needed for their language.
    -->
   <item quantity="one">There is %d task left.</item>
   <item quantity="other">There are %d tasks still to do.</item>
 </plurals>
```

要呈現完整的字串使用`GetQuantityString`方法，並傳遞要顯示的資源識別碼和值 （這會傳遞兩次）。 Android 會使用第二個參數來判斷*這*`quantity`来使用的字串，第三個參數的值實際上取代成 （兩者都是必要的） 字串。

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

有效`quantity`參數如下：

* 零
* one
* 兩個
* 幾個
* many
* 另一個

它們會更詳細地描述[Android docs](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals)。如果給定的語言不需要 「 特殊 」 處理 」，這些`quantity`會忽略字串 (例如英文只會使用`one`並`other`; 指定`zero`字串會有任何作用，它將不會使用)。

### <a name="images"></a>影像

當地語系化的映像，請依照下列字串檔案相同的規則： 應用程式中參考的所有映像應該置於**drawable**目錄，因此沒有後援。

地區設定特定的映像然後應放在限定的可繪製資料夾這類**drawable es**或是**drawable 日本**（dpi 規範也可以加入）。

在此螢幕擷取畫面中，四個映像會儲存在**drawable**目錄，但只有一**flag.png**，已當地語系化的另一個目錄中的複本。

![螢幕擷取畫面的多個可繪製資料夾中，每一個都包含一或多個當地語系化的.png 檔案](localization-images/drawable.png)


#### <a name="other-resource-types"></a>其他資源類型

您也可以提供其他類型的替代方案，包括版面配置、 動畫和原始檔案的語言特定資源。 這表示您可以提供一個或多個目標語言的特定畫面版面配置時，例如您可以建立版面配置專為德文，以便很長的文字標籤。

Android 4.2 導入的支援[由右至左 (RTL) 語言](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html)如果您將應用程式設定`android:supportsRtl="true"`。 資源限定詞`"ldrtl"`可以納入包含專為從右至左顯示的自訂配置目錄名稱。

如需有關資源目錄命名和後援的詳細資訊，請參閱 Android 文件[提供替代資源](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)。


### <a name="app-name"></a>應用程式名稱

應用程式名稱很容易可以使用當地語系化`@string/id`中的`MainLauncher`活動：

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>從右至左 (RTL) 語言

Android 4.2 和更新版本提供完整支援從右至左配置，詳述[原生支援的從右至左的部落格](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html)。

使用 Android 4.2 (API level 17) 時和更新版本中，您可以使用指定值的對齊`start`並`end`而不是`left`並`right`(例如`android:paddingStart`)。 另外還有新的 Api，例如`LayoutDirection`， `TextDirection`，和`TextAlignment`可協助您從右至左的讀取器中建立調整的畫面。

下列螢幕擷取畫面示[當地語系化**Tasky**範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)以阿拉伯數字：

[![阿拉伯文 Tasky 應用程式的螢幕擷取畫面](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

下一步 的螢幕擷取畫面顯示[當地語系化**Tasky**範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)希伯來文：

[![希伯來文 Tasky 應用程式的螢幕擷取畫面](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

使用當地語系化從右至左的文字**Strings.xml**一樣 LTR 文字中的檔案。

## <a name="testing"></a>測試

請務必徹底測試的預設地區設定。 如果基於某些原因 （也就是它們是遺失） 無法載入預設資源，將會損毀您的應用程式。

### <a name="emulator-testing"></a>模擬器測試

請參閱 Google[在 Android 模擬器上測試](https://developer.android.com/guide/topics/resources/localization.html#testing)區段，如需有關如何將模擬器設定為特定地區設定使用 ADB 殼層。

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>裝置測試

若要測試的裝置上，變更的語言**設定**應用程式。
**提示：** 請記下的圖示和功能表項目的位置，以便您可以還原成原來的設定語言。


## <a name="summary"></a>總結

本文章涵蓋當地語系化使用內建資源處理的 Android 應用程式基本的概念。 您可以進一步了解 i18n 和 L10n 適用於 iOS、 Android 和跨平台 （包括 Xamarin.Forms) 中的應用程式[此跨平台指南](~/cross-platform/app-fundamentals/localization.md)。



## <a name="related-links"></a>相關連結

- [Tasky （程式碼中已當地語系化） （範例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Android 資源當地語系化](https://developer.android.com/guide/topics/resources/localization.html)
- [跨平台當地語系化概觀](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin.Forms 當地語系化](~/xamarin-forms/app-fundamentals/localization/index.md)
- [iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)
