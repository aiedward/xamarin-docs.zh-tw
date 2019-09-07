---
title: Android 當地語系化
description: 本檔介紹 Android SDK 的當地語系化功能，以及如何使用 Xamarin 來存取它們。
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 08d12a58b4a0d8a8f757df965bd9dfb73f1639b1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755379"
---
# <a name="android-localization"></a>Android 當地語系化

_本檔介紹 Android SDK 的當地語系化功能，以及如何使用 Xamarin 來存取它們。_

## <a name="android-platform-features"></a>Android 平台功能

本節說明 Android 的主要當地語系化功能。 請跳到[下一節](#basics)，以查看特定的程式碼和範例。

### <a name="locale"></a>地區設定

使用者在 設定 中選擇其語言 **> 語言 & 輸入**。 此選項會控制所顯示的語言和使用的地區設定（例如 適用于日期和數位格式）。

您可以透過目前內容`Resources`來查詢目前的地區設定：

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

這個值會是地區設定識別碼，其中同時包含語言代碼和地區設定代碼（以底線分隔）。 如需參考，以下是[JAVA 地區設定的清單](https://www.oracle.com/technetwork/java/javase/locales-137662.html)，以及透過[StackOverflow 的 Android 支援地區](https://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android)設定。

常見範例包括︰

- `en_US`適用于英文（美國）
- `es_ES`西班牙文（西班牙）
- `ja_JP`適用于日文（日本）
- `zh_CN`適用于中文（中國）
- `zh_TW`適用于中文（臺灣）
- `pt_PT`葡萄牙文（葡萄牙）
- `pt_BR`葡萄牙文（巴西）

### <a name="locale_changed"></a>LOCALE_CHANGED

當使用者`android.intent.action.LOCALE_CHANGED`變更其語言選擇時，會產生 Android。

活動可以藉由在活動上設定`android:configChanges`屬性來選擇處理此項，如下所示：

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>Android 中的國際化基本概念

Android 的當地語系化策略具有下列主要部分：

- 包含當地語系化字串、影像和其他資源的資源資料夾。

- `GetText`方法，用來在程式碼中取出當地語系化的字串

- `@string/id`在 AXML 檔中，自動將當地語系化的字串放在版面配置中。

### <a name="resource-folders"></a>資源資料夾

Android 應用程式會管理資源資料夾中的大部分內容，例如：

- **layout** -包含 AXML 版面配置檔案。
- **繪製**-包含影像和其他的繪製資源。
- **values** -包含字串。
- **raw** -包含資料檔案。

大部分的開發人員已經很熟悉在可**繪製**目錄上使用**DPI**尾碼來提供映射的多個版本，讓 Android 為每個裝置選擇正確的版本。 相同的機制是用來透過 suffixing 具有語言和文化特性識別碼的資原始目錄，來提供多個語言翻譯。

![多個文化特性識別碼的資源/可繪製和資源/值資料夾的螢幕擷取畫面](localization-images/resources.png)

> [!NOTE]
> 指定最上層語言`es`時，只需要兩個字元，但在指定完整的地區設定時，目錄名稱格式需要破折號和小寫**r**來分隔兩個部分，例如**pt-rBR**或**zh-rCN**。 將此與程式碼中傳回的值進行比較，其具有底線（例如 `pt_BR`)。 這兩個都不同于 .net `CultureInfo`類別使用的值，它只會有破折號（例如 `pt-BR`)。 跨 Xamarin 平臺工作時，請記住這些差異。

#### <a name="stringsxml-file-format"></a>字串 .xml 檔案格式

當地語系化**值**目錄（例如 **值-es**或**values-rBR**）應包含名為**string .xml**的檔案，該檔案將包含該地區設定的翻譯文字。

每個可翻譯的字串都是 XML 元素，其中的資源`name`識別碼指定為屬性，而翻譯後的字串為值：

```xml
<string name="app_name">TaskyL10n</string>
```

您需要根據一般 XML 規則來進行 escape，而且`name`必須是有效的 Android 資源識別碼（不含空格或連字號）。 以下是範例中的預設（英文）字串檔案範例：

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

西班牙文目錄**值-es**包含具有相同名稱（**字串 .xml**）的檔案，其中包含翻譯：

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

![多個值資料夾的螢幕擷取畫面，其中每個都包含一個字串 .xml 檔案](localization-images/values.png)

設定字串檔案之後，就可以在配置和程式碼中參考轉譯的值。

### <a name="axml-layout-files"></a>AXML 版面配置檔案

若要參考版面配置檔案中的`@string/id`當地語系化字串，請使用語法。 範例中的這個 XML 程式碼片段`text`會顯示使用當地語系化資源識別碼設定的屬性（已省略一些其他屬性）：

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

若要在程式碼中取出翻譯的`GetText`字串，請使用方法並傳遞資源識別碼：

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>數量字串

Android 字串資源也可讓您建立*數量字串*，讓翻譯人員針對不同的數量提供不同的翻譯，例如：

- 「剩下1個工作」。
- 「還有2個工作仍在執行」。

（而不是一般的「有 n 個工作會 left」）。

在**字串 .xml**中

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

若要呈現完整的字串， `GetQuantityString`請使用方法，傳遞資源識別碼和要顯示的值（傳遞兩次）。 第二個參數是由 Android 用來決定要使用*的* `quantity`字串，第三個參數是實際取代成字串的值（兩者都是必要的）。

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

有效`quantity`的參數包括：

- 零
- one
- 兩個
- 幾
- many
- 另一個

這些檔將在[Android](https://developer.android.com/guide/topics/resources/string-resource.html#Plurals)檔中更詳細地說明。如果指定的語言不需要「特殊」處理，則會`quantity`忽略這些字串（例如，英文僅使用`one`和`other`; 指定`zero`字串不會有任何作用，將不會使用）。

### <a name="images"></a>影像

當地語系化的影像遵循與字串檔案相同的規則：應用程式中參考的所有影像都應該放在可**繪製**的目錄中，因此會有一個回溯。

地區設定特定的映射應該會放在**可繪製的**可繪製資料夾中，例如，或可**繪製-ja-jp** （也可以加入 DPI 規範）。

在此螢幕擷取畫面中，四個影像會儲存在可**繪製**的目錄中，但只有一個是**旗標 .png**，而在其他目錄中有當地語系化的複本。

![多個繪製資料夾的螢幕擷取畫面，其中每個資料夾都包含一或多個當地語系化的 .png 檔案](localization-images/drawable.png)

#### <a name="other-resource-types"></a>其他資源類型

您也可以提供其他類型的替代語言特定資源，包括版面配置、動畫和原始檔案。 這表示您可以為一或多個目的語言提供特定的螢幕版面配置，例如，您可以建立特別針對德文的版面配置，以允許非常長的文字標籤。

如果您設定應用程式設定`android:supportsRtl="true"`，Android 4.2 引進了[從右至左（RTL）語言](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html)的支援。 資源辨識符號`"ldrtl"`可以包含在目錄名稱中，以包含專為 RTL 顯示而設計的自訂配置。

如需資原始目錄命名和回溯的詳細資訊，請參閱 Android 檔，以[提供替代資源](https://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)。

### <a name="app-name"></a>應用程式名稱

您可以使用`@string/id`中`MainLauncher`的活動，輕鬆地將應用程式名稱當地語系化：

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>由右至左（RTL）語言

Android 4.2 和更新版本提供 RTL 配置的完整支援，在[原生 Rtl 支援的 blog](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html)中有詳細說明。

使用 Android 4.2 （API 層級17）和更新版本時，可以使用`start`和`end`來指定對齊值`left` ， `right`而不是`android:paddingStart`和（例如）。 還有新的 api （例如`LayoutDirection`、 `TextDirection`和`TextAlignment` ），可協助建立適應 RTL 讀取器的畫面。

下列螢幕擷取畫面顯示阿拉伯文的[當地語系化**Tasky**範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)：

[![阿拉伯文中 Tasky 應用程式的螢幕擷取畫面](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

下一個螢幕擷取畫面顯示希伯來文中的[當地語系化**Tasky**範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)：

[![希伯來文中 Tasky 應用程式的螢幕擷取畫面](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

RTL 文字是使用**字串 .xml**檔案進行當地語系化，其方式與 LTR 文字相同。

## <a name="testing"></a>測試

請務必徹底測試預設的地區設定。 如果因為某些原因而無法載入預設資源（也就是遺失），您的應用程式將會損毀。

### <a name="emulator-testing"></a>模擬器測試

如需如何使用 ADB shell 將模擬器設為特定地區設定的指示，請參閱 Google[在 Android Emulator](https://developer.android.com/guide/topics/resources/localization.html#testing)一節的測試。

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>裝置測試

若要在裝置上進行測試，請變更 [**設定**] 應用程式中的語言。

> [!TIP]
> 記下功能表項目的圖示和位置，讓您可以將語言還原成原始設定。

## <a name="summary"></a>總結

本文涵蓋使用內建資源處理來當地語系化 Android 應用程式的基本概念。 您可以在[此跨平臺指南](~/cross-platform/app-fundamentals/localization.md)中深入瞭解適用于 IOS、Android 和跨平臺（包括 Xamarin）應用程式的 I18n 和 L10n。

## <a name="related-links"></a>相關連結

- [Tasky （在程式碼中當地語系化）（範例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [使用資源進行當地語系化的 Android](https://developer.android.com/guide/topics/resources/localization.html)
- [跨平臺當地語系化總覽](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin. 表單當地語系化](~/xamarin-forms/app-fundamentals/localization/index.md)
- [iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)
