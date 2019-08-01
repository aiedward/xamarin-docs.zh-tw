---
title: 取代動作列
ms.prod: xamarin
ms.assetid: 5341D28E-B203-478D-8464-6FAFDC3A4110
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/27/2018
ms.openlocfilehash: 19ac5a023b1f97b2e08bbe1821a2b9259280fc98
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645144"
---
# <a name="replacing-the-action-bar"></a>取代動作列

## <a name="overview"></a>總覽

最常見的用途`Toolbar`之一, 就是將預設動作列取代為自訂`Toolbar` (建立新的 Android 專案時, 它會使用預設的動作列)。 `Toolbar`因為能夠將品牌標誌、標題、功能表項目、瀏覽按鈕, 甚至是自訂視圖加入至活動 UI 的 [應用程式行] 區段, 所以它在預設動作列上提供了大量的升級。

若要使用`Toolbar`下列內容取代應用程式的預設動作列: 

1.  建立新的自訂主題, 並修改應用程式的屬性, 讓它使用這個新主題。 

2.  停用`windowActionBar`自訂主題中的屬性, 並`windowNoTitle`啟用屬性。

3.  定義的`Toolbar`版面配置。

4.  將配置包含在活動的 axml 版面配置檔案中。 `Toolbar` 

5.  將程式碼新增至活動`OnCreate`的方法, 找`Toolbar`出`ToolBar`並`SetActionBar`呼叫以安裝做為動作列。

下列各節將詳細說明此程式。 隨即建立簡單的應用程式, 並將其動作列取代為`Toolbar`自訂的。 



## <a name="start-an-app-project"></a>啟動應用程式專案

建立名為**ToolbarFun**的新 Android 專案 (如需有關建立新 Android 專案的詳細資訊, 請參閱[Hello, android](~/android/get-started/hello-android/hello-android-quickstart.md) )。 建立此專案之後, 將目標和最小的 Android API 層級設定為**android 5.0 (API 層級 21-棒糖)** 或更新版本。 如需設定 Android 版本層級的詳細資訊, 請參閱[瞭解 ANDROID API 層級](~/android/app-fundamentals/android-api-levels.md)。 建立並執行應用程式時, 它會顯示預設動作列, 如下列螢幕擷取畫面所示:

[![預設動作列的螢幕擷取畫面](replacing-the-action-bar-images/01-before-sml.png)](replacing-the-action-bar-images/01-before.png#lightbox)



## <a name="create-a-custom-theme"></a>建立自訂主題

開啟**Resources/values**目錄, 然後建立名為**樣式 .xml**的新檔案。 以下列 XML 取代其內容: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<resources>
  <style name="MyTheme" parent="@android:style/Theme.Material.Light.DarkActionBar">
    <item name="android:windowNoTitle">true</item>
    <item name="android:windowActionBar">false</item>
    <item name="android:colorPrimary">#5A8622</item>
  </style>
</resources>
```

此 XML 會定義名為**MyTheme**的新自訂主題, 其以棒糖中的**DarkActionBar**主題為基礎。 屬性會設定為`true`以隱藏標題列: `windowNoTitle` 

```xml
<item name="android:windowNoTitle">true</item>
```

若要顯示自訂工具列, 必須`ActionBar`停用預設值: 

```xml
<item name="android:windowActionBar">false</item>
```

[橄欖綠-綠色`colorPrimary` ] 設定會用於工具列的背景色彩: 
 
```xml
<item name="android:colorPrimary">#5A8622</item>
```

## <a name="apply-the-custom-theme"></a>套用自訂主題

編輯**Properties/androidmanifest.xml** , 並將下列`android:theme`屬性新增至`<application>` `MyTheme`元素, 讓應用程式使用自訂主題: 

```xml
<application android:label="@string/app_name" android:theme="@style/MyTheme"></application>
```

如需將自訂主題套用至應用程式的詳細資訊, 請參閱[使用自訂主題](~/android/user-interface/material-theme.md#customtheme)。 



## <a name="define-a-toolbar-layout"></a>定義工具列版面配置

在**資源/版面**配置目錄中, 建立名為**toolbar**的新檔案。 以下列 XML 取代其內容: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:minHeight="?android:attr/actionBarSize"
    android:background="?android:attr/colorPrimary"
    android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"/>
```

此 XML 會定義取代`Toolbar`預設動作列的自訂。 的最小高度`Toolbar`會設定為它所取代的動作列大小: 

```csharp
android:minHeight="?android:attr/actionBarSize"
```

的背景色彩`Toolbar`會設定為稍早在**樣式 .xml**中定義的橄欖色-綠色色彩:

```csharp
android:background="?android:attr/colorPrimary"
```

從棒糖開始, `android:theme`屬性可以用來為個別視圖的樣式。 在`ThemeOverlay.Material`棒糖中引進的主題可讓您覆迭`Theme.Material`預設主題, 並覆寫相關的屬性, 使其變亮或深色。 在此範例中, `Toolbar`會使用深色主題, 使其內容具有淺色色彩: 

```csharp
android:theme="@android:style/ThemeOverlay.Material.Dark.ActionBar"
```

使用此設定, 讓功能表項目與較暗的背景色彩對比。



## <a name="include-the-toolbar-layout"></a>包含工具列版面配置

編輯配置檔案**Resources/layout/axml** , 並將其內容取代為下列 XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <include
        android:id="@+id/toolbar"
        layout="@layout/toolbar" />
</RelativeLayout>
```

此配置包含在`Toolbar` **toolbar**中定義的, 並使用`RelativeLayout`來指定`Toolbar`要放置在 UI 的最上方 (在按鈕上方)。 



## <a name="find-and-activate-the-toolbar"></a>尋找並啟動工具列

編輯**MainActivity.cs**並新增下列 using 語句:

```csharp
using Android.Views;
```

此外, 將下列幾行程式碼新增至`OnCreate`方法的結尾:

```csharp
var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
SetActionBar(toolbar);
ActionBar.Title = "My Toolbar";
```

此程式碼`Toolbar` `Toolbar`會尋找和`SetActionBar`呼叫, 以便接受預設的動作列特性。 工具列的標題會變更為 [**我的工具列**]。 如這個程式碼範例所`ToolBar`示, 可以直接參考為動作列。 編譯並執行此應用&ndash;程式會`Toolbar`顯示自訂的, 以取代預設動作列: 

[![具有綠色色彩配置的自訂工具列螢幕擷取畫面](replacing-the-action-bar-images/02-after-sml.png)](replacing-the-action-bar-images/02-after.png#lightbox)

請注意, `Toolbar`的樣式與應用於`Theme.Material.Light.DarkActionBar`應用程式其餘部分的主題無關。 

如果在執行應用程式時發生例外狀況, 請參閱下面的[疑難排解](#troubleshooting)一節。

 
## <a name="add-menu-items"></a>新增功能表項目 

在這一節中, 功能表會加入`Toolbar`至。 的右上方區域`ToolBar`會保留供功能表項目&ndash;使用: 每個功能表項目 (也稱為*動作專案*) 都可以在目前的活動內執行動作, 或可以代表整個應用程式執行動作。 

若要將功能表新增`Toolbar`至: 

1.  將功能表圖示 (如有必要) 新增`mipmap-`至應用程式專案的資料夾。 Google 會在 [[材質] 圖示](https://design.google.com/icons/)頁面上提供一組免費功能表圖示。 

2.  在 [**資源]/[功能表**] 下新增功能表資源檔, 以定義功能表項目的內容。 

3.  執行此方法擴大功能表項目&ndash;的活動方法。`OnCreateOptionsMenu` 

4.  執行活動&ndash;的方法。當按下功能表項目時, 這個方法會執行動作。 `OnOptionsItemSelected` 

下列各節會詳細說明此程式, 方法是將 [**編輯**] 和 [**儲存**] 功能表項目新增至自訂`Toolbar`的。 



### <a name="install-menu-icons"></a>安裝功能表圖示

繼續進行`ToolbarFun`範例應用程式, 將功能表圖示新增至應用程式專案。 下載[工具列圖示](https://github.com/xamarin/monodroid-samples/blob/master/Supportv7/AppCompat/Toolbar/Resources/toolbar-icons-plus.zip?raw=true)、解壓縮, 並將解壓縮的*mipmap*資料夾的內容複寫到**ToolbarFun/Resources**底下的專案*mipmap*資料夾, 並在專案中包含每個新增的圖示檔。


### <a name="define-a-menu-resource"></a>定義功能表資源

建立新 **功能表** 下方的子目錄 **資源**。 在**功能表**子目錄中, 建立名為**top_menus**的新功能表資源檔, 並將其內容取代為下列 xml: 

```xml
<?xml version="1.0" encoding="utf-8" ?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item
       android:id="@+id/menu_edit"
       android:icon="@mipmap/ic_action_content_create"
       android:showAsAction="ifRoom"
       android:title="Edit" />
  <item
       android:id="@+id/menu_save"
       android:icon="@mipmap/ic_action_content_save"
       android:showAsAction="ifRoom"
       android:title="Save" />
  <item
       android:id="@+id/menu_preferences"
       android:showAsAction="never"
       android:title="Preferences" />
</menu>
```

此 XML 會建立三個功能表項目:

-   使用`ic_action_content_create.png`圖示的 [**編輯**] 功能表項目 (鉛筆)。 

-   使用`ic_action_content_save.png`圖示 (磁片) 的 [**儲存**] 功能表項目。 

-   沒有圖示的**喜好**設定功能表項目。

`Toolbar` &ndash; `ifRoom` `showAsAction` [**編輯**] 和 [**儲存**] 功能表項目的屬性會設定為此設定, 因此, 如果有足夠的空間可顯示這些功能表項目, 則會在中顯示這些專案。 [**喜好**設定] 功能表`showAsAction`項`never`會將設為&ndash;此, 使 [**喜好**設定] 功能表出現在*溢*位功能表中 (三個垂直點)。 


### <a name="implement-oncreateoptionsmenu"></a>執行 OnCreateOptionsMenu

將下列方法新增至**MainActivity.cs**:

```csharp
public override bool OnCreateOptionsMenu(IMenu menu)
{
    MenuInflater.Inflate(Resource.Menu.top_menus, menu);
    return base.OnCreateOptionsMenu(menu);
}
```

Android 會呼叫`OnCreateOptionsMenu`方法, 讓應用程式可以指定活動的功能表資源。 在此方法中, **top_menus**會擴大到傳遞`menu`的。 這段程式碼會使新的 [**編輯**]、[**儲存**] 和 [ `Toolbar`**喜好**設定] 功能表項目出現在中。 



### <a name="implement-onoptionsitemselected"></a>執行 OnOptionsItemSelected

將下列方法新增至**MainActivity.cs**:

```csharp
public override bool OnOptionsItemSelected(IMenuItem item)
{
    Toast.MakeText(this, "Action selected: " + item.TitleFormatted,
        ToastLength.Short).Show();
    return base.OnOptionsItemSelected(item);
}
```

當使用者按下功能表項目時, Android 會呼叫`OnOptionsItemSelected`方法, 並傳入已選取的功能表項目。 在此範例中, 執行只會顯示一個快顯通知, 指出已點擊哪個功能表項目。 

建立並執行`ToolbarFun` , 以在工具列中看到新的功能表項目。 `Toolbar`現在會顯示三個功能表圖示, 如下列螢幕擷取畫面所示: 

[![說明 [編輯]、[儲存] 和 [溢位] 功能表項目位置的圖表](replacing-the-action-bar-images/04-menu-items-sml.png)](replacing-the-action-bar-images/04-menu-items.png#lightbox)

當使用者按下 [**編輯**] 功能表項目時, 會顯示快顯通知, `OnOptionsItemSelected`指出已呼叫方法: 

[![按 [編輯專案] 時顯示的快顯快顯螢幕擷取畫面](replacing-the-action-bar-images/05-toast-displayed-sml.png)](replacing-the-action-bar-images/05-toast-displayed.png#lightbox)

當使用者按下溢位功能表時, 會顯示 [**喜好**設定] 功能表項目。 通常, 較不常見的動作應該放在溢位功能表&ndash;中, 此範例會使用 [溢位] 功能表作為**喜好**設定, 因為它不常當做 [**編輯**] 和 [**儲存**] 使用: 

[![出現在溢位功能表中的 [喜好設定] 功能表項目的螢幕擷取畫面](replacing-the-action-bar-images/06-preferences-sml.png)](replacing-the-action-bar-images/06-preferences.png#lightbox)

如需 Android 功能表的詳細資訊, 請參閱 Android 開發人員[功能表](https://developer.android.com/guide/topics/ui/menus.html)主題。 
 

## <a name="troubleshooting"></a>疑難排解

下列秘訣可協助您在以工具列取代動作列時, 偵測可能發生的問題。

### <a name="activity-already-has-an-action-bar"></a>活動已經有動作列

如果應用程式未正確設定為使用自訂主題, 如套用[自訂主題](#apply-the-custom-theme)中所述, 執行應用程式時可能會發生下列例外狀況:

![未使用自訂主題時可能發生的錯誤](replacing-the-action-bar-images/03-theme-not-defined.png)

此外, 可能會產生如下的錯誤訊息:_IllegalStateException:此活動已經有視窗 décor 所提供的動作列。_ 

若要更正此錯誤, 請確認`android:theme`已將自訂主題的屬性新增`<application>`至 (在**Properties/androidmanifest.xml**中), 如稍早的套用[自訂主題](#apply-the-custom-theme)中所述。 此外, 如果`Toolbar`未正確設定版面配置或自訂主題, 可能會導致此錯誤。


## <a name="related-links"></a>相關連結

- [棒糖工具列 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [AppCompat 工具列 (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
