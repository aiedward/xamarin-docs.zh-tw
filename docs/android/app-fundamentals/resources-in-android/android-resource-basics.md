---
title: Android 資源基本概念
ms.prod: xamarin
ms.assetid: ED32E7B5-D552-284B-6385-C3EDDCC30A4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/01/2018
ms.openlocfilehash: c248949024d0e13a24863368e88aa559fa496806
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755250"
---
# <a name="android-resource-basics"></a>Android 資源基本概念

幾乎所有 Android 應用程式都有一些資源，它們通常會以 XML 檔案的形式使用使用者介面版面配置。 第一次建立 Xamarin 應用程式時，會由 [Xamarin. Android] 專案範本設定預設資源：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![資源檔](android-resource-basics-images/01-resource-files-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![資源檔](android-resource-basics-images/01-resource-files-xs.png)

-----

組成預設資源的五個檔案是建立在 Resources 資料夾中：

- **圖示 .png** &ndash;應用程式的預設圖示

- Axml&ndash;應用程式的預設使用者介面配置檔案。 請注意，雖然 Android 使用 **.xml**副檔名，但**axml**副檔名也會使用。

- 字串資料表可協助當地語系化應用程式 &ndash;

- AboutResources 不是必要的，而且可以安全地刪除。 &ndash; 它只提供 Resources 資料夾和其中檔案的高階總覽。

- Resource.designer.cs&ndash;這個檔案是由 Xamarin 自動產生及維護，並保留指派給每個資源的唯一識別碼。 這非常類似，而且與以 JAVA 撰寫的 Android 應用程式所使用的 R. java 檔案相同。 它會由 Xamarin Android 工具自動建立，並會在一段時間後重新產生。

## <a name="creating-and-accessing-resources"></a>建立和存取資源

建立資源就像將檔案新增至有問題的資源類型的目錄一樣簡單。 下列螢幕擷取畫面顯示德文地區設定的字串資源已新增至專案。 將**字串**新增至檔案時，會將**組建動作**自動設定為**AndroidResource** ，由 Xamarin 工具：

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![String .xml 的組建動作設定為 AndroidResource](android-resource-basics-images/02-build-action-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

![String .xml 的組建動作設定為 AndroidResource](android-resource-basics-images/02-build-action-xs.png)

-----

這可讓您在 APK 檔案中正確編譯和內嵌資源。 若因為某些原因而無法將**組建動作**設定為**Android 資源**，則會從 APK 中排除這些檔案，而任何嘗試載入或存取資源的動作都會導致執行階段錯誤，且應用程式會損毀。

此外，請務必注意，雖然 Android 僅支援資源專案的小寫檔案名，但 Xamarin 較容許。它將同時支援大寫和小寫檔案名。 映射名稱的慣例是使用小寫加底線做為分隔符號（例如，**我\_的影像\_名稱 .png**）。 請注意，如果使用破折號或空格做為分隔符號，就無法處理資源名稱。

將資源新增至專案之後，有兩種方式可在應用程式中以程式設計&ndash;方式（在程式碼內）或 XML 檔案中使用它們。

## <a name="referencing-resources-programmatically"></a>以程式設計方式參考資源

若要以程式設計方式存取這些檔案，系統會將唯一的資源識別碼指派給它們。 此資源識別碼是在名`Resource`為的特殊類別中定義的整數，在檔案**Resource.designer.cs**中找到，看起來像這樣：

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

每個資源識別碼都包含在對應至資源類型的嵌套類別內。 例如，當檔案**圖示 .png**已新增至專案時，Xamarin 會更新`Resource`類別，並在名為`Icon`的中建立名`Drawable`為的嵌套類別。
這允許在程式代碼`Resource.Drawable.Icon`中將檔案圖示稱為。 `Resource`類別不應手動編輯，因為對其所做的任何變更將會被 Xamarin 覆寫。

以程式設計方式參考資源時（在程式碼中），可以透過使用下列語法的 Resources 類別階層來存取它們：

```csharp
[<PackageName>.]Resource.<ResourceType>.<ResourceName>
```

- **PackageName**&ndash;提供資源的封裝，只有在使用來自其他封裝的資源時才需要。

- **ResourceType**&ndash;這是上述資源類別內的嵌套資源類型。

- **資源名稱**&ndash;這是資源的檔案名（不含副檔名）或 android： name 屬性的值（適用于 XML 元素中的資源）。

## <a name="referencing-resources-from-xml"></a>從 XML 參考資源

XML 檔案中的資源是由下列特殊語法存取：

```xml
@[<PackageName>:]<ResourceType>/<ResourceName>
```

- **PackageName**&ndash;提供資源的封裝，只有在使用來自其他封裝的資源時才需要。

- **ResourceType**&ndash;這是資源類別內的嵌套資源類型。

- **資源名稱**這是資源的檔案名（*不含*檔案類型副檔名）或 XML 元素中資源的`android:name`屬性值。 &ndash;

例如，版面配置檔案**axml**的內容如下所示：

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

這個範例具有[`ImageView`](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/imageview) ，其需要名為**旗**標的可繪製資源。 將`ImageView` `@drawable/flag`其`src`屬性設定為。 當活動開始時，Android 會在目錄**資源/可繪製**檔案中查看名為 [**旗標 .png** ] 的檔案（副檔名可以是另一種影像格式，例如 [旗標 **.jpg**]），並載入`ImageView`該檔案並將其顯示在中。
執行此應用程式時，它看起來會像下圖：

![當地語系化的 ImageView](android-resource-basics-images/03-localized-screenshot.png)
