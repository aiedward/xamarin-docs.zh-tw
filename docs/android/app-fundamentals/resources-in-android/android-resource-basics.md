---
title: "Android 資源基本概念"
ms.topic: article
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/01/2018
ms.openlocfilehash: fba8412c53597260744bdce443a7e993a6990672
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="android-resource-basics"></a>Android 資源基本概念

幾乎所有的 Android 應用程式中，會有某種資源最少它們通常具有使用者介面版面配置，在 XML 檔案的形式。 Xamarin.Android 應用程式最初建立時，預設資源是由 Xamarin.Android 專案範本的安裝程式：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![資源檔](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![資源檔](android-resource-basics-images/01-resource-files-xs.png)
 
-----

在 [資源] 資料夾中建立五個檔案組成的預設資源：

-  **Icon.png** &ndash;應用程式的預設圖示

-  **Main.axml** &ndash;應用程式的預設使用者介面版面配置的檔案。 請注意，雖然 Android 使用**.xml**檔案延伸模組，會使用 Xamarin.Android **.axml**檔案副檔名。

-  **Strings.xml** &ndash;協助進行應用程式的當地語系化字串資料表

-  **AboutResources.txt** &ndash;這不需要，並且可以安全地刪除。 它只會提供 [資源] 資料夾和檔案的高層級概觀。

-  **Resource.designer.cs** &ndash;這個檔案自動產生及維護的 Xamarin.Android 及保存之唯一識別碼指派給每個資源。 這是非常類似且 R.java 檔案在 Java 中撰寫的 Android 應用程式會套用相同的用途。 它會自動建立 Xamarin.Android 工具，並將不時重新產生。


## <a name="creating-and-accessing-resources"></a>建立及存取資源

建立資源很簡單，只將檔案加入至有問題的資源類型的目錄。 下列螢幕擷取畫面顯示德文地區設定的字串資源已加入至專案。 當**Strings.xml**加入至檔案，**建置動作**自動設定為**AndroidResource** Xamarin.Android 工具：

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![建置動作設定為 AndroidResource Strings.xml](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

![建置動作設定為 AndroidResource Strings.xml](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

這可讓 Xamarin.Android 工具正確編譯與內嵌 APK 檔案中的資源。 如果基於某些原因**建置動作**未設定為**Android 資源**檔案之後將會排除 APK，任何嘗試載入或存取的資源將會導致執行階段錯誤，應用程式將會損毀。

此外，請務必注意，即使 Android 只支援小寫的檔名為資源項目，但是 Xamarin.Android 位元比較能夠容許;它會支援大寫和小寫的檔名。 映像名稱慣例是使用小寫底線做為分隔符號 (例如，**我\_映像\_name.png**)。 請注意，是否連字號或空格作為分隔符號不能處理的資源名稱。

一旦資源已加入至專案中，有兩種方式可在應用程式中使用它們&ndash;以程式設計方式 （在程式碼） 或從 XML 檔案。


## <a name="referencing-resources-programmatically"></a>以程式設計方式參考的資源

若要以程式設計方式存取這些檔案，它們指派一個唯一的資源 id。 此資源 ID 是特殊類別中定義的整數`Resource`，檔案中找到**Resource.designer.cs**，並且看起來像這樣：

```csharp
public partial class Resource
{
    public partial class Attribute
    {
    }
    public partial class Drawable {
        public const int Icon=0x7f020000;
    }
    public partial class Id
    {
        public const int Textview=0x7f050000;
    }
    public partial class Layout
    {
        public const int Main=0x7f030000;
    }
    public partial class String
    {
        public const int App_Name=0x7f040001;
        public const int Hello=0x7f040000;
    }
}
```

每個資源識別碼被包含在巢狀類別對應到資源類型。 例如，當檔案**Icon.png**已加入至專案中，更新 Xamarin.Android`Resource`類別，建立巢狀的類別稱為 「`Drawable`與常數的內部名稱為`Icon`。
這可讓檔案**Icon.png**中程式碼做為參考`Resource.Drawable.Icon`。 `Resource`類別不應手動編輯，因為它所做的任何變更將會覆寫 Xamarin.Android。

在參考時以程式設計方式 （在程式碼） 的資源，它們可以透過在資源類別階層會使用下列語法：

```xml
@[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **PackageName** &ndash;提供資源，而且僅在封裝時，需要從其他封裝資源的使用。

-  **ResourceType** &ndash;這是上面所述的資源類別內的巢狀的資源類型。

-  **資源名稱**&ndash;這是檔名 （不含副檔名） 的資源或資源的 XML 項目中的 android: name 屬性的值。


## <a name="referencing-resources-from-xml"></a>從 XML 參考的資源

下列存取 XML 檔案中的資源特殊的語法：

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>.
```

-  **PackageName** &ndash;提供資源，而且僅在封裝時，需要從其他封裝資源的使用。

-  **ResourceType** &ndash;這是在資源類別內的巢狀的資源類型。

-  **資源名稱**&ndash;這是資源的檔名 (*不含*檔案類型副檔名) 的值或`android:name`屬性的 XML 項目中的資源。

如需範例檔案的內容配置， **Main.axml**，如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">
    <ImageView android:id="@+id/myImage"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/flag" />
</LinearLayout>
```

這個範例有[ `ImageView` ](https://developer.xamarin.com/recipes/android/controls/imageview)需要名為的 drawable 資源**旗標**。 `ImageView`具有其`src`屬性設為 **@drawable/flag** 。 當活動開始時，Android 看起來目錄內**資源/Drawable**名為的檔案**flag.png** (副檔名可能是另一個映像格式，例如**flag.jpg**)和載入該檔案，並顯示在`ImageView`。
執行此應用程式時，它會看起來類似下列的影像：

![當地語系化的 ImageView](android-resource-basics-images/03-localized-screenshot.png)

