---
title: Android 資源基本概念
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: 2673021fae2f0a0b45761bf4ed619c92fb826b13
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110130"
---
# <a name="android-resource-basics"></a>Android 資源基本概念

幾乎所有的 Android 應用程式中，會有某種形式的資源至少他們通常會有使用者介面版面配置，在 XML 檔案的形式。 第一次建立 Xamarin.Android 應用程式時，預設資源是由 Xamarin.Android 專案範本的安裝程式：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![資源檔](android-resource-basics-images/01-resource-files-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![資源檔](android-resource-basics-images/01-resource-files-xs.png)
 
-----

在 [資源] 資料夾中建立 5 個檔案的預設資源所組成：

-  **Icon.png** &ndash;應用程式的預設圖示

-  **Main.axml** &ndash;應用程式的預設使用者介面版面配置檔。 請注意，當 Android 會使用 **.xml**副檔名，Xamarin.Android 會使用 **.axml**副檔名。

-  **Strings.xml** &ndash;字串資料表，以確保使用應用程式的當地語系化

-  **AboutResources.txt** &ndash;這並不需要和可能安全地刪除。 它只是提供 [資源] 資料夾和檔案的高階的概觀。

-  **Resource.designer.cs** &ndash;這個檔案自動產生及維護的 Xamarin.Android 和保留的唯一識別碼指派給每個資源。 這是非常類似的用途與 R.java 檔案，以 Java 撰寫的 Android 應用程式必須相同。 它會自動建立由 Xamarin.Android 工具，並將隨時重新產生。


## <a name="creating-and-accessing-resources"></a>建立及存取資源

建立資源是簡單，只要將檔案加入至有問題的資源類型的目錄。 以下螢幕擷取畫面會顯示為德文地區設定的字串資源已新增至專案。 當**Strings.xml**加入至檔案，**建置動作**自動設定為**AndroidResource**由 Xamarin.Android 工具：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![建置動作設為 AndroidResource Strings.xml](android-resource-basics-images/02-build-action-vs.png)
 
# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![建置動作設為 AndroidResource Strings.xml](android-resource-basics-images/02-build-action-xs.png)
 
-----
 

這可讓 Xamarin.Android 工具，才能正確編譯，並內嵌至 APK 檔案中的資源。 如果基於某些原因**建置動作**未設定為**Android 資源**、 從 APK，然後將排除檔案和任何嘗試載入或存取的資源將會導致執行階段錯誤，應用程式將會損毀。

此外，務必請注意，雖然 Android 資源項目僅支援小寫的檔名，Xamarin.Android 有點比較能夠容許;它會支援大寫和小寫的檔名。 映像名稱的慣例是使用有底線的小寫，做為分隔符號 (例如**我\_映像\_name.png**)。 請注意，是否連字號或空格作為分隔符號，無法處理的資源名稱。

一旦資源已新增至專案，有兩種方式可在應用程式中使用它們&ndash;以程式設計方式 （在程式碼） 或從 XML 檔案。


## <a name="referencing-resources-programmatically"></a>以程式設計方式參考的資源

若要以程式設計方式存取這些檔案，其會指派一個唯一的資源識別碼。 此資源識別碼是特殊類別中定義的整數`Resource`，這檔案中找到**Resource.designer.cs**，並且看起來像這樣：

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

每個資源識別碼被包含在巢狀類別對應到資源類型。 例如，當檔案**Icon.png**已加入至專案，Xamarin.Android 更新`Resource`類別，建立巢狀的類別稱為`Drawable`常數內名為`Icon`。
這可讓檔案**Icon.png**做為程式碼中參考`Resource.Drawable.Icon`。 `Resource`類別不應手動編輯，因為 Xamarin.Android 會覆寫對它進行任何變更。

當參考資源，以程式設計方式 （在程式碼），它們可透過存取的資源類別階層架構，它會使用下列語法：

```xml
@[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

-  **PackageName** &ndash;的封裝提供的資源，而且只有在需要時從其他封裝資源的使用。

-  **ResourceType** &ndash;這是上面所述的資源類別內的巢狀的資源類型。

-  **資源名稱**&ndash;這是檔案名稱 （不含副檔名） 的資源或資源中的 XML 項目針對 android: name 屬性的值。


## <a name="referencing-resources-from-xml"></a>從 XML 參考的資源

XML 檔案中的資源依照存取特殊的語法：

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>.
```

-  **PackageName** &ndash;的封裝提供的資源，而且只有在需要時從其他封裝資源的使用。

-  **ResourceType** &ndash;這是巢狀的資源類型內的資源類別。

-  **資源名稱**&ndash;這是資源的檔名 (*而不需要*檔案類型副檔名)，或是值`android:name`屬性的 XML 項目中的資源。

例如版面配置檔案的內容**Main.axml**，如下所示：

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

此範例有[ `ImageView` ](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/imageview)需要名為的可繪製資源**旗標**。 `ImageView`有其`src`屬性設為**@drawable/flag**。 當活動開始時，Android 會尋找目錄內**資源/Drawable**名為的檔案**flag.png** (副檔名可以是另一種影像格式，例如**flag.jpg**)和載入該檔案，並顯示在`ImageView`。
執行此應用程式時，它會看起來如下圖所示：

![當地語系化的 ImageView](android-resource-basics-images/03-localized-screenshot.png)

