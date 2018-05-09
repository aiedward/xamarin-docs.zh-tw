---
title: Android 的當地語系化
description: 本文件介紹當地語系化功能的 Android SDK，以及如何使用 Xamarin 存取它們。
ms.prod: xamarin
ms.assetid: D1277939-A1E8-468E-B136-820D816AF853
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 076cadd16c3953ee4e06193190b59035ad57f2c1
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="android-localization"></a>Android 的當地語系化

_本文件介紹當地語系化功能的 Android SDK，以及如何使用 Xamarin 存取它們。_

## <a name="android-platform-features"></a>Android 平台功能

本章節描述 Android 主要當地語系化的功能。 請跳至[下一節](#basics)若要查看特定程式碼和範例。

### <a name="locale"></a>地區設定

使用者選擇其語言**設定 > 語言和輸入**。 此選取項目控制項顯示的語言和地區設定使用 （例如。 日期和數字格式）。

目前的地區設定可透過目前的內容查詢`Resources`:

```csharp
var lang = Resources.Configuration.Locale; // eg. "es_ES"
```

這個值會包含語言程式碼和地區設定代碼，底線隔開的地區設定識別碼。 如需參考，以下是[Java 地區設定的清單](http://www.oracle.com/technetwork/java/javase/locales-137662.html)和[Android 支援的地區設定透過 StackOverflow](http://stackoverflow.com/questions/7973023/what-is-the-list-of-supported-languages-locales-on-android)。

常見範例包括︰

* `en_US` 英文 (美國 Statees)
* `es_ES` 西班牙文 （西班牙）
* `ja_JP` 日文 （日本）
* `zh_CN` 中文 （中國）
* `zh_TW` 中文 （台灣）
* `pt_PT` 葡萄牙文 （葡萄牙）
* `pt_BR` 葡萄牙文 （巴西）

### <a name="localechanged"></a>LOCALE_CHANGED

Android 產生`android.intent.action.LOCALE_CHANGED`當使用者變更其選擇的語言。

活動可以選擇要處理這種設定`android:configChanges`屬性上的活動，就像這樣：

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
        ConfigurationChanges = ConfigChanges.Locale | ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
```

<a name="basics" />

## <a name="internationalization-basics-in-android"></a>在 Android 中的國際化基本概念

Android 的當地語系化策略具有下列索引鍵部分：

- 資源資料夾包含當地語系化的字串、 影像和其他資源。

- `GetText` 方法，用來擷取程式碼中的當地語系化的字串

- `@string/id` 在 AXML 檔案中，以自動版面配置中將當地語系化的字串。

### <a name="resource-folders"></a>資源資料夾

Android 應用程式管理資源資料夾中的大部分內容，例如：

* **版面配置**-包含 AXML 配置檔案。
* **drawable** -包含影像和其他 drawable 的資源。
* **值**-包含的字串。
* **原始**-含有資料檔案。

大部分的開發人員已熟悉使用**dpi**上後置字元**drawable**目錄所提供的映像，讓 Android 選擇針對每個裝置的正確版本的多個版本。 使用相同的機制是藉由尾碼識別項的語言和文化特性的資源目錄，提供多個語言的翻譯。

![資源/drawable 和資源/值的多個文化特性識別項的資料夾的螢幕擷取畫面](localization-images/resources.png)

> [!NOTE]
> 指定這類的上層語言時`es`只有兩個字元是必要項; 不過，在指定完整的地區設定時，目錄名稱格式需要虛線和小寫**r**來分隔兩個部分，例如**pt rBR**或**zh-chs 和 rCN**。 傳回在程式碼中 （例如有底線的值相比較 `pt_BR`)。 這些都是不同的值.NET`CultureInfo`類別使用，其中包含破折號只能 （例如 `pt-BR`)。 Xamarin 平台上使用時，請記住這些差異。

#### <a name="stringsxml-file-format"></a>Strings.xml 檔案格式

當地語系化**值**目錄 （例如。 **值 es**或**值-點-rBR**) 應該包含檔案，稱為**Strings.xml** ，就會包含該地區設定翻譯的文字。

每個可翻譯的字串是 XML 項目與資源識別碼指定為`name`屬性和值的已翻譯的字串：

```xml
<string name="app_name">TaskyL10n</string>
```

您必須根據標準的 XML 規則，逸出和`name`必須是有效的 Android 資源 ID （沒有空格或連字號）。 以下是範例的預設 （英文） 的字串檔的範例：

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

西班牙文目錄**值 es**包含具有相同名稱的檔案 (**Strings.xml**)，其中包含的翻譯：

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

與字串的檔案安裝，可配置和程式碼中參考翻譯的值。

### <a name="axml-layout-files"></a>AXML 版面配置檔案

若要參考版面配置檔案中的當地語系化的字串，使用`@string/id`語法。 從這個範例會示範這個 XML 程式碼片段`text`屬性與設定當地語系化的資源識別碼 （已省略某些其他屬性）：

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

若要擷取在程式碼中的已翻譯的字串，請使用`GetText`方法，然後傳遞的資源識別碼：

```csharp
var cancelText = Resources.GetText (Resource.String.taskcancel);
```

#### <a name="quantity-strings"></a>數量的字串

Android 的字串資源也可讓您建立*數量字串*能夠將轉譯程式來提供不同的轉譯為不同的數量，例如：

* 「 沒有左 1 的工作。 」
* "有 2 工作仍在執行。 」

(而不是泛型"There are n 任務左")。

在**Strings.xml**

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

要呈現完整字串使用`GetQuantityString`方法，傳遞要顯示的資源識別碼和值 （這會傳遞兩次）。 第二個參數可由 Android 來判斷*其中*`quantity`字串來使用，但第三個參數值實際上取代 （兩者都是必要的） 的字串。

```csharp
var translated = Resources.GetQuantityString (
                    Resource.Plurals.numberOfTasks, taskcount, taskcount);`
```

有效`quantity`參數：

* 零
* one
* 兩個
* 幾個
* many
* 另一個

它們更詳細地描述[Android 文件](http://developer.android.com/guide/topics/resources/string-resource.html#Plurals)。如果給定的語言不需要 「 特殊 」 處理那些`quantity`字串將會被忽略 (例如英文僅使用`one`和`other`; 指定`zero`字串會沒有作用，它將不會使用)。

### <a name="images"></a>影像

當地語系化的映像遵循相同的規則，以字串的檔案： 在應用程式中參考的所有映像應該放在**drawable**目錄，因此沒有後援。

地區設定特定映像然後應放在限定 drawable 資料夾例如**drawable es**或**drawable ja** （dpi 規範也可以加入）。

在這個螢幕擷取畫面中，四個影像會儲存在**drawable**目錄，但只有一個， **flag.png**，當地語系化的其他目錄中的複本。

![螢幕擷取畫面的多個 drawable 資料夾，每一個都會包含一個或多個當地語系化的.png 檔案](localization-images/drawable.png)


#### <a name="other-resource-types"></a>其他資源類型

您也可以提供其他類型的替代方式，將語言特定資源，包括版面配置、 動畫和 raw 檔案。 這表示您無法為一或多種目標語言提供特定螢幕的配置，例如您無法建立配置特別為德文，允許很長的文字標籤。

Android 4.2 引進支援[由右至左 (RTL) 語言](http://android-developers.blogspot.fr/2013/03/native-rtl-support-in-android-42.html)如果您將應用程式設定`android:supportsRtl="true"`。 資源限定詞`"ldrtl"`可以包含在 direcory 名稱以包含專為從右至左顯示的自訂版面配置。

如需有關資源目錄命名和後援的詳細資訊，請參閱 Android 文件[提供替代資源](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)。


### <a name="app-name"></a>應用程式名稱

應用程式名稱很容易使用當地語系化`@string/id`中針對`MainLauncher`活動：

```csharp
[Activity (Label = "@string/app_name", MainLauncher = true, Icon="@drawable/launcher",
    ConfigurationChanges =  ConfigChanges.Orientation | ConfigChanges.Locale)]
```

### <a name="right-to-left-rtl-languages"></a>由右至左 (RTL) 語言

Android 4.2 和更新版本提供完整支援 RTL 配置中有詳細, 說明[原生從右至左支援部落格](http://android-developers.blogspot.dk/2013/03/native-rtl-support-in-android-42.html)。

使用 Android 4.2 (API level 17) 時，可以使用指定值的更新版本中，aligment`start`和`end`而不是`left`和`right`(例如`android:paddingStart`)。 也有新的 Api 類似`LayoutDirection`， `TextDirection`，和`TextAlignment`可協助您建立從右至左讀取器的螢幕，以調整。

下列螢幕擷取畫面顯示[當地語系化**Tasky**範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)以阿拉伯數字：

[![阿拉伯 Tasky 應用程式的螢幕擷取畫面](localization-images/rtl-ar-sml.png)](localization-images/rtl-ar.png#lightbox) 

下一步 的螢幕擷取畫面顯示[當地語系化**Tasky**範例](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)希伯來文：

[![希伯來文 Tasky 應用程式的螢幕擷取畫面](localization-images/rtl-he-sml.png)](localization-images/rtl-he.png#lightbox)

使用當地語系化從右至左文字**Strings.xml**方式與從左至右文字相同的檔案。

## <a name="testing"></a>測試

請務必徹底測試預設的地區設定。 如果基於某些原因 （也就是它們是遺漏） 無法載入預設資源，您的應用程式會損毀。

### <a name="emulator-testing"></a>模擬器測試

Google 是指[Android 模擬器上測試](https://developer.android.com/guide/topics/resources/localization.html#testing)> 一節，如需如何將模擬器設定為特定地區設定使用 ADB 殼層的指示。

```shell
adb shell setprop persist.sys.locale fr-CA;stop;sleep 5;start
```

### <a name="device-testing"></a>裝置測試

若要測試的裝置上，變更語言**設定**應用程式。
**提示：**設圖示的附註和功能表項目的位置，好讓您可以還原成原來的設定的語言。


## <a name="summary"></a>總結

本文涵蓋當地語系化使用內建資源處理的 Android 應用程式基本的概念。 您可以為 iOS、 Android 和跨平台 （包括 Xamarin.Forms) 應用程式中的了解 i18n 與 L10n[本跨平台指南](~/cross-platform/app-fundamentals/localization.md)。



## <a name="related-links"></a>相關連結

- [Tasky （當地語系化程式碼中） （範例）](https://github.com/conceptdev/xamarin-samples/tree/master/TaskyL10n)
- [Android 當地語系化資源](http://developer.android.com/guide/topics/resources/localization.html)
- [跨平台當地語系化概觀](~/cross-platform/app-fundamentals/localization.md)
- [Xamarin.Forms 當地語系化](~/xamarin-forms/app-fundamentals/localization/index.md)
- [iOS 當地語系化](~/ios/app-fundamentals/localization/index.md)
