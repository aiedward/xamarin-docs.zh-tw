---
title: 繫結 .AAR
description: 本演練提供了從 Android 創建 Xamarin.Android Java 綁定庫的分步說明。AAR 檔。
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 103720c8cb47b1ac4cfe5cfadeb6b18828318ad3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73618541"
---
# <a name="binding-an-aar"></a>繫結 .AAR

_本演練提供了從 Android 創建 Xamarin.Android Java 綁定庫的分步說明。AAR 檔。_

## <a name="overview"></a>概觀

*安卓存檔 (.AAR)* 檔案是 Android 庫的檔案格式。
A .AAR 檔案是 。包含以下內容的 ZIP 檔:

- 編譯的 Java 代碼
- 資源識別碼
- 資源
- 中繼資料(例如,活動聲明、許可權)

在本指南中,我們將逐步介紹為單個 創建綁定庫的基礎知識。AAR 檔。 有關 Java 函式庫綁定的概述(使用基本代碼範例),請參閱綁定[Java 函式庫](~/android/platform/binding-java-library/index.md)。

> [!IMPORTANT]
> 繫結專案只能包含一個 。AAR 檔。 如果 .AAR 相依其他 。AAR,然後這些依賴項應包含在它們自己的綁定專案中,然後引用。 請參閱[Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)。

## <a name="walkthrough"></a>逐步介紹

我們將為在 Android Studio 中建立的 Android 存檔建立器庫,[文字分析器.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true). 這。AAR 包含`TextCounter`一個包含靜態方法的類,用於計算字串中的元音和輔音數。 此外,**文本分析器.aar**包含一個圖像資源,以幫助顯示計數結果。

我們將使用以下步驟從 創建綁定庫。AAR 檔案:

1. 創建新的 JAVA 綁定庫專案。

2. 新增單一個 。AAR 檔案到專案。 繫結專案只能包含單個 。Aar。

3. 為設置相應的生成操作。AAR 檔。

4. 選擇的目標框架。AAR 支援。

5. 生成綁定庫。

建立繫結庫後,我們將開發一個小型 Android 應用程式,提示使用者輸入文字字串,呼叫 。AAR 方法分析文本,從 檢索圖像。AAR,並顯示結果以及圖像。

範例應用程式將存`TextCounter`**取文本分析器**的類。

```java
package com.xamarin.textcounter;

public class TextCounter
{
    ...
    public static int numVowels (String text) { ... };
    ...
    public static int numConsonants (String text) { ... };
    ...
}
```

此外,此示例應用將檢索並顯示在**文本分析器**中打包的圖像資源。

[![夏馬林猴子影像](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

此影像資源駐留在**文字分析器**中的**res/可繪製/monkey.png。**

### <a name="creating-the-bindings-library"></a>建立繫結庫

在開始執行以下步驟之前,請下載範例[文字分析器.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) Android 存檔檔:

1. 從 Android 綁定庫範本開始創建新的綁定庫專案。 您可以使用 Visual Studio 作為 Mac 或 Visual Studio(下面的螢幕截圖顯示了視覺工作室,但 Mac 的可視化工作室非常相似)。 命名解決方案**AarBinding**:

    [![建立 AarBindings 專案](binding-an-aar-images/01-new-bindings-library-vs-sml.w160.png)](binding-an-aar-images/01-new-bindings-library-vs.w160.png#lightbox)

2. 這個樣本包含**Jars**資料夾,您可以在其中新增 。AAR 到綁定庫專案。 右鍵按下**Jars**資料夾並選擇 **「新增>現有專案**:

    [![新增現有項目](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)

3. 瀏覽到較早下載**的文字分析器.aar**檔案,選擇它,然後按下「**新增**:

    [![新增文本阿納萊澤.阿爾](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)

4. 驗證**文字分析器.aar**檔已成功新增到專案中:

    [![新增文字分析器.aar 檔案](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5. 將**文字分析器的**產生操作設定`LibraryProjectZip`為 。 在 Mac 的可視化工作室中,右鍵單擊**文本分析器.aar**以設置生成操作。 在可視化工作室中,可以在 **「屬性**」窗格中設置生成操作:

    [![將文字分析器.aar 產生操作設定為函式庫工程Zip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6. 開啟項目屬性以設定*目標框架*。 如果 .AAR 使用任何 Android API,將目標框架設置為的 API 級別。AAR 期望。 (有關目標框架設定和 Android API 等級的詳細資訊,請參閱瞭解[Android API 級別](~/android/app-fundamentals/android-api-levels.md)。

    設置綁定庫的目標 API 級別。 在此範例中,我們可以自由地使用最新的平臺 API 級別(API 級別 23),因為我們**的文本分析器**不依賴於 Android API:

    [![將目標等級設定為 API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7. 生成綁定庫。 繫結庫專案應成功產生並產生輸出 。DLL 在以下位置 **:Aar 綁定/bin/除錯/Aar 綁定.dll**

### <a name="using-the-bindings-library"></a>使用繫結庫

要使用此 。DLL 在 Xamarin.Android 應用中,必須首先添加對綁定庫的引用。 使用下列步驟：

1. 我們將在同一解決方案中創建此應用程式,以簡化本演練。 (使用綁定庫的應用也可以駐留在不同的解決方案中。創建新的 Xamarin.Android 應用程式:右鍵單擊解決方案並選擇「**添加新專案**」。 具新項目**繫結 :**

    [![建立新的結合測試項目](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2. 右鍵按一下**綁定測試**專案的**參考**節點,然後選擇 **"添加引用..."**

    [![單擊"添加參考"](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3. 選擇較早建立的**AarBinding**專案,然後單擊 **「 確定**」 :

    [![檢查 AAR 繫結項目](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4. 開啟**繫結測試**項目的**參考**節點,以驗證**是否存在 AarBinding**引用:

    [![AarBinding 列在「參考文獻」下](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)

如果要查看綁定庫項目的內容,可以按兩下引用以在**物件瀏覽器**中打開它。 您可以檢視命名空間的`Com.Xamarin.Textcounter`映射內容(從 Java`com.xamarin.textanalyzezr`套件映射),並`TextCounter`可以檢視 類別的成員:

[![檢視物件瀏覽器](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

上述螢幕截圖突出顯示示例應用將調用`TextAnalyzer`的`NumConsonants`兩 種方法:(`numConsonants`包裝基礎 JAVA`NumVowels`方法)和`numVowels`(包裝基礎 JAVA 方法)。

### <a name="accessing-aar-types"></a>訪問。AAR 類型

向指向綁定庫的應用添加引用後,可以在中訪問 JAVA 類型。AAR,因為您將訪問 C# 類型(多虧了 C# 包裝器)。 C# 應用程式代碼`TextAnalyzer`可以 呼叫以下的範例的範例 :

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

在上面的示例中,我們在`TextCounter`類中調用靜態方法。 但是,您還可以實例化類和調用實例方法。 例如,如果的 。AAR 包裝一個`Employee`稱為 具有實`buildFullName`例方法 的類,您可以`MyClass`實例化 並使用它,如下所示:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

以下步驟向應用添加代碼,以便它提示用戶輸入文本,使用分析`TextCounter`文本,然後顯示結果。

將**結合測試**佈局 (**Main.axml**) 取代為以下 XML。 此佈局具有用於`EditText`文字輸入的佈局和用於啟動中音和輔音計數的兩個按鈕:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation             ="vertical"
    android:layout_width            ="fill_parent"
    android:layout_height           ="fill_parent" >
    <TextView
        android:text                ="Text to analyze:"
        android:textSize            ="24dp"
        android:layout_marginTop    ="30dp"
        android:layout_gravity      ="center"
        android:layout_width        ="wrap_content"
        android:layout_height       ="wrap_content" />
    <EditText
        android:id                  ="@+id/input"
        android:text                ="I can use my .AAR file from C#!"
        android:layout_marginTop    ="10dp"
        android:layout_gravity      ="center"
        android:layout_width        ="300dp"
        android:layout_height       ="wrap_content"/>
    <Button
        android:id                  ="@+id/vowels"
        android:layout_marginTop    ="30dp"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Vowels" />
    <Button
        android:id                  ="@+id/consonants"
        android:layout_width        ="240dp"
        android:layout_height       ="wrap_content"
        android:layout_gravity      ="center"
        android:text                ="Count Consonants" />
</LinearLayout>
```

將**MainActivity.cs**的內容替換為以下代碼。 如本示例所示,按鈕事件處理程式調用駐留在`TextCounter`中的包裝方法。AAR 並使用 Toast 來顯示結果。 請注意繫結`using`庫的命名空間的語句(在本例中為`Com.Xamarin.Textcounter`):

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using Android.Views.InputMethods;
using Com.Xamarin.Textcounter;

namespace BindingTest
{
    [Activity(Label = "BindingTest", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        InputMethodManager imm;

        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);

            SetContentView(Resource.Layout.Main);

            imm = (InputMethodManager)GetSystemService(Context.InputMethodService);

            var vowelsBtn = FindViewById<Button>(Resource.Id.vowels);
            var consonBtn = FindViewById<Button>(Resource.Id.consonants);
            var edittext = FindViewById<EditText>(Resource.Id.input);
            edittext.InputType = Android.Text.InputTypes.TextVariationPassword;

            edittext.KeyPress += (sender, e) =>
            {
                imm.HideSoftInputFromWindow(edittext.WindowToken, HideSoftInputFlags.NotAlways);
                e.Handled = true;
            };

            vowelsBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumVowels(edittext.Text);
                string msg = count + " vowels found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

            consonBtn.Click += (sender, e) =>
            {
                int count = TextCounter.NumConsonants(edittext.Text);
                string msg = count + " consonants found.";
                Toast.MakeText (this, msg, ToastLength.Short).Show ();
            };

        }
    }
}
```

編譯並運行**綁定測試**專案。 應用程式將開始並在左側顯示螢幕截圖(`EditText`使用一些文字初始化,但您可以點擊它來更改它)。 當您點擊**COUNT VOWELS**時,Toast 將顯示右側顯示的中繼音數:

[![執行繫結測試的螢幕擷取](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

點擊點擊 **「計數」 按鈕**。 此外,您還可以修改文本行,然後再次點按這些按鈕以測試不同的元音和輔音計數。

### <a name="accessing-aar-resources"></a>訪問。AAR 資源

Xamarin 工具合併中的**R**資料。AAR 進入**套用資源類別**。 因此,您可以存取 。佈局(和代碼後面的)中的 AAR 資源與存取項目 **「資源」** 路徑中的資源的方式相同。

要存取影像資源,請使用資源 **.可繪製**名稱,用於打包在中的圖像。Aar。 例如,您可以在中引用**image.png。** AAR 檔案`@drawable/image`使用 :

```xml
<ImageView android:src="@drawable/image" ... />
```

您還可以訪問駐留在 中的資源佈局。Aar。 為此,請使用資源 **.佈局**名稱來處理在 中打包的佈局。Aar。 例如：

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

**文字分析器.aar**範例包含影像檔案,該檔案位於**res/可繪製/monkey.png**。 讓我們存取此映像資源並在範例應用程式中使用它:

編輯**繫結測試**佈局 (**Main.axml**`ImageView`),`LinearLayout`並將新增到容器的末尾。 這`ImageView`顯示在**\@可繪製/猴子**處找到的圖像;此圖像將從**文本分析器**的資源部分載入。

```xml
    ...
    <ImageView
        android:src                 ="@drawable/monkey"
        android:layout_marginTop    ="40dp"
        android:layout_width        ="200dp"
        android:layout_height       ="200dp"
        android:layout_gravity      ="center" />

</LinearLayout>
```

編譯並運行**綁定測試**專案。 當您點擊**COUNT CONSONANTS**時,&ndash;應用程式將啟動並呈現左側的螢幕截圖,結果將顯示為右側所示:

[![繫結測試顯示輔音計數](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)

恭喜！ 您已成功綁定 Java 庫。Aar!

## <a name="summary"></a>總結

在本演練中,我們為創建了一個綁定庫。AAR 檔,將綁定庫添加到最小測試應用,並運行該應用以驗證我們的 C# 代碼是否可以調用駐留在 中的 Java 代碼。AAR 檔。
此外,我們擴展了應用程式以訪問並顯示駐留在中的映像資源。AAR 檔。

## <a name="related-links"></a>相關連結

- [建構 Java 結合函式庫(視訊)](https://university.xamarin.com/classes#10090)
- [繫結 .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
- [Aar 繫結(範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Bug 44573 - 一個項目無法結合多個 .aar 檔案](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
