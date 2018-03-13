---
title: "繫結。AAR"
description: "本逐步解說提供建立從 Android Xamarin.Android Java 繫結程式庫的逐步指示。AAR 檔案。"
ms.topic: article
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: ae209f8099925cc160e16cb5365625e48e6c384d
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="binding-an-aar"></a>繫結。AAR

_本逐步解說提供建立從 Android Xamarin.Android Java 繫結程式庫的逐步指示。AAR 檔案。_


## <a name="overview"></a>總覽

*Android 封存 (。AAR)*檔案是 Android 程式庫的檔案格式。
。AAR 檔案。ZIP 封存包含下列：

-   已編譯的 Java 程式碼
-   資源識別碼
-   資源
-   中繼資料 （例如，活動宣告、 權限）

本指南中，我們將逐步完成建立單一繫結文件庫的基本概念。AAR 檔案。 如需 Java 文件庫繫結的一般情況下 （使用基本的程式碼範例） 的概觀，請參閱[繫結 Java 文件庫](~/android/platform/binding-java-library/index.md)。


> [!IMPORTANT]
> 繫結專案只能包含一個。AAR 檔案。 如果。其他 AAR 相依性。應該包含在自己的繫結專案並再參考 AAR，則這些相依性。 請參閱[Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)。


## <a name="walkthrough"></a>逐步解說

我們將建立繫結的程式庫，針對 Android 的封存檔案的範例是在 Android Studio、 [textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true)。 這。AAR 包含`TextCounter`類別計數的母音和子音字串中的靜態方法。 此外， **textanalyzer.aar**包含影像資源，可顯示計算的結果。

我們將使用下列步驟來建立從繫結程式庫。AAR 檔案：

1. 建立新的 Java 繫結的程式庫專案。

2. 加入一個。AAR 檔案加入專案中。 繫結專案只能包含單一。AAR。

3. 設定適當的建置動作。AAR 檔案。

4. 選擇目標架構。AAR 支援。

5. 建立繫結的程式庫。

一旦我們建立了繫結的程式庫，我們將開發小型的 Android 應用程式，以提示使用者輸入的文字字串，呼叫。AAR 方法分析的文字、 擷取的映像。AAR，並顯示結果以及映像。

範例應用程式會存取`TextCounter`類別**textanalyzer.aar**:

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

此外，此範例應用程式會擷取並顯示的影像資源，封裝在**textanalyzer.aar**:

[![Xamarin 猴子映像](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

此映像資源位於**res/drawable/monkey.png**中**textanalyzer.aar**。



### <a name="creating-the-bindings-library"></a>建立繫結的程式庫

然後再開始執行下列步驟，請下載此範例[textanalyzer.aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) Android 封存檔案：

1.  建立新的繫結的程式庫專案，從 Android 繫結的程式庫範本。 您可以使用 Visual Studio for Mac 或 Visual Studio （下面的螢幕擷取畫面顯示 Visual Studio 中，但 Visual Studio for Mac 是非常類似）。 將方案命名**AarBinding**:

    [![建立 AarBindings 專案](binding-an-aar-images/01-new-bindings-library-vs-sml.png)](binding-an-aar-images/01-new-bindings-library-vs.png#lightbox)

2.  此範本包括**Jar**資料夾，您新增程式。AAR(s) 繫結庫專案。 以滑鼠右鍵按一下**Jar**資料夾，然後選取**新增 > 現有項目**:

    [![加入現有項目](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)


3.  瀏覽至**textanalyzer.aar**先前下載的檔案並加以選取，按一下 **新增**:

    [![新增 textanalayzer.aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)


4.  確認**textanalyzer.aar**檔案已成功加入至專案：

    [![已加入 textanalyzer.aar 檔案](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5.  設定的建置動作**textanalyzer.aar**至`LibraryProjectZip`。 在 Visual Studio for Mac，以滑鼠右鍵按一下**textanalyzer.aar**設定建置動作。 在 Visual Studio 中，可以在中設定建置動作**屬性**窗格):

    [![將 textanalyzer.aar 建置動作設定為 LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6.  開啟專案屬性，以設定*目標 Framework*。 如果。AAR 會使用任何的 Android 應用程式開發介面，將目標 Framework 設定為應用程式開發介面層級。AAR 預期。 (有關的目標架構設定和 Android API 層級的一般詳細資訊，請參閱[了解 Android API 層級](~/android/app-fundamentals/android-api-levels.md)。)

    設定繫結庫目標 API 層級。 在此範例中，我們可以隨意使用最新的平台應用程式開發介面層級 （API 層級 23），因為我們**textanalyzer** Android Api 上沒有相依性：

    [![目標層級設定為 API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7.  建立繫結的程式庫。 繫結的程式庫專案應該會順利建置和產生的輸出。在下列位置的 DLL: **AarBinding/bin/Debug/AarBinding.dll**



### <a name="using-the-bindings-library"></a>使用繫結的程式庫

若要使用這種情況。Xamarin.Android 應用程式中的 DLL，您必須先新增繫結的程式庫的參考。 使用下列步驟：

1.  正在建立此應用程式和繫結程式庫，以簡化此逐步解說的相同方案中。 （使用繫結的程式庫應用程式也可能位於不同的解決方案。）建立新的 Xamarin.Android 應用程式： 以滑鼠右鍵按一下方案，然後選取**加入新的專案**。 將新專案**BindingTest**:

    [![建立新的 BindingTest 專案](binding-an-aar-images/07-add-new-project-vs-sml.png)](binding-an-aar-images/07-add-new-project-vs.png#lightbox)

2.  以滑鼠右鍵按一下**參考**節點**BindingTest**專案，然後選取**加入參考...**:

    [![按一下 加入參考](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3.  選取**AarBinding**稍早建立的專案，然後按一下**確定**:

    [![AAR 繫結專案簽入](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4.  開啟**參考**節點**BindingTest**專案可讓您確認**AarBinding**參考存在於：

    [![AarBinding 會列在底下的參考](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)


如果您想要檢視繫結的程式庫專案的內容，您可以按兩下以開啟在參考**物件瀏覽器**。 您所見的對應的內容`Com.Xamarin.Textcounter`命名空間 (從 Java 對應`com.xamarin.textanalyzezr`封裝)，您可以檢視的成員`TextCounter`類別：

[![檢視物件瀏覽器](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

上面的螢幕擷取畫面會反白顯示兩個`TextAnalyzer`範例應用程式會呼叫的方法： `NumConsonants` (包裝基礎 Java`numConsonants`方法)，和`NumVowels`(包裝基礎 Java`numVowels`方法)。



### <a name="accessing-aar-types"></a>存取。AAR 類型

您將參考加入您的應用程式指向繫結的程式庫之後，您可以存取中的 Java 類型。AAR，就會存取 C# 類型 （這點受惠 C# 包裝函式）。 C# 應用程式程式碼可以呼叫`TextAnalyzer`方法，如本範例所示：

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

在上述範例中，我們正在撥打的靜態方法，`TextCounter`類別。 不過，您也可以具現化類別，並呼叫執行個體方法。 例如，如果您。AAR 包裝類別，稱為`Employee`具有執行個體方法`buildFullName`，您可以具現化`MyClass`並使用它所見這裡：

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

下列步驟將程式碼加入應用程式，讓它會提示使用者輸入文字，使用`TextCounter`来分析的文字，然後顯示結果。

取代**BindingTest**配置 (**Main.axml**) 以下列 XML。 此配置沒有`EditText`文字輸入和兩個按鈕來起始母音和子音計數：

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

取代內容**Weatherapp**為下列程式碼。 此範例所示，按鈕事件處理常式呼叫包裝`TextCounter`位於的方法。AAR 及使用快顯通知來顯示結果。 請注意`using`繫結的程式庫的命名空間陳述式 (在此情況下， `Com.Xamarin.Textcounter`):

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

編譯及執行**BindingTest**專案。 應用程式將會啟動，並在左側呈現螢幕擷取畫面 (`EditText`初始化與一些文字，但您可以點選它加以變更)。 當您點選**計數母音**，快顯會顯示母音數目，在右側所示：

[![從執行 Shoppingcart 螢幕擷取畫面](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

再試一次點選**計數子音** 按鈕。 此外，您可以修改的文字行，並點選一次，以測試不同母音這些按鈕並子音計數。



### <a name="accessing-aar-resources"></a>存取。AAR 資源

Tooling 合併 Xamarin **R**的資料。您的應用程式到 AAR**資源**類別。 如此一來，您可以存取。AAR 資源從您的配置 （以及程式碼後置） 在相同的方式就如同存取資源所**資源**您專案的路徑。

若要存取影像資源，您使用**Resource.Drawable**映像封裝的內部名稱。AAR。 例如，您可以參考**image.png**中。AAR 檔案，使用`@drawable/image`:

```xml
<ImageView android:src="@drawable/image" ... />
```

您也可以存取位於的資源配置。AAR。 若要這樣做，您使用**Resource.Layout**內封裝的版面配置的名稱。AAR。 例如: 

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

**Textanalyzer.aar**範例包含位於的映像檔**res/drawable/monkey.png**。 讓我們來存取此影像資源，並在我們的範例應用程式中使用它：

編輯**BindingTest**配置 (**Main.axml**) 並加入`ImageView`結尾`LinearLayout`容器。 這`ImageView`顯示影像，請參閱 **@drawable/monkey** ; 此映像將載入的資源區段從**textanalyzer.aar**:

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

編譯及執行**BindingTest**專案。 應用程式將會啟動並顯示螢幕擷取畫面左邊&ndash;當您點選**計數子音**，結果會顯示在右側所示：

[![BindingTest 顯示 consonant 計數](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)


恭喜您！ 您已成功地繫結 Java 文件庫。AAR ！



## <a name="summary"></a>總結

在本逐步解說中，我們建立一個繫結文件庫。AAR 檔案，加入繫結的程式庫的最小的測試應用程式，並執行應用程式，以確認我們 C# 程式碼可以呼叫位於的 Java 程式碼。AAR 檔案。
此外，我們擴充應用程式存取及顯示位於映像資源。AAR 檔案。


## <a name="related-links"></a>相關連結

- [建置 Java 繫結程式庫 （影片）](https://university.xamarin.com/classes#10090)
- [繫結 .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
- [AarBinding （範例）](https://developer.xamarin.com/samples/monodroid/JavaIntegration/AarBinding)
- [Bug 44573 一個專案無法繫結多個.aar 檔案](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
