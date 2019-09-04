---
title: 繫結 .AAR
description: 本逐步解說提供逐步指示, 說明如何從 Android 建立 Xamarin. Android JAVA 系結程式庫。AAR 檔案。
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/11/2018
ms.openlocfilehash: ee29f54ac68f370cb9499d315116a030247f6044
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2019
ms.locfileid: "70225728"
---
# <a name="binding-an-aar"></a>繫結 .AAR

_本逐步解說提供逐步指示, 說明如何從 Android 建立 Xamarin. Android JAVA 系結程式庫。AAR 檔案。_


## <a name="overview"></a>總覽

*Android 封存 (。AAR)* file 是 Android 程式庫的檔案格式。
,.AAR 檔案是。ZIP 封存, 其中包含下列各項:

- 已編譯的 JAVA 程式碼
- 資源識別碼
- 資源
- 中繼資料 (例如活動宣告、許可權)

在本指南中, 我們將逐步解說建立單一系結程式庫的基本概念。AAR 檔案。 如需 JAVA 程式庫系結的一般總覽 (包含基本程式碼範例), 請參閱系結[java 程式庫](~/android/platform/binding-java-library/index.md)。


> [!IMPORTANT]
> 系結專案只能包含一個。AAR 檔案。 如果為。AAR 其他的相依性。AAR, 這些相依性應該包含在自己的系結專案中, 然後加以參考。 請參閱[Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)。


## <a name="walkthrough"></a>逐步解說

我們將建立在 Android Studio, [textanalyzer. aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true)中建立的範例 Android 封存檔案的系結程式庫。 該表.AAR 包含`TextCounter`具有靜態方法的類別, 其會計算字串中的母音和子音數目。 此外, **textanalyzer aar**包含影像資源, 可協助顯示計數結果。

我們將使用下列步驟, 從建立系結程式庫。AAR 檔案:

1. 建立新的 JAVA 系結程式庫專案。

2. 新增單一。AAR 檔案加入至專案。 系結專案只能包含單一。AAR.

3. 為設定適當的組建動作。AAR 檔案。

4. 選擇的目標 framework。AAR 支援。

5. 建立系結程式庫。

建立系結程式庫之後, 我們會開發一個小型 Android 應用程式, 以提示使用者輸入文字字串, 並呼叫。AAR 方法來分析文字, 並從抓取影像。AAR, 並連同影像一起顯示結果。

範例應用程式會存取 textanalyzer `TextCounter`的類別 **。 aar**:

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

此外, 此範例應用程式將會抓取並顯示封裝在 textanalyzer 中的影像資源 **。 aar**:

[![Xamarin 猴子映射](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

此映射資源位於**textanalyzer. aar**中的**res/繪製/猴子。**



### <a name="creating-the-bindings-library"></a>建立系結程式庫

開始進行下列步驟之前, 請先下載範例[textanalyzer. aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) Android 封存檔案:

1. 從 Android 系結程式庫範本開始, 建立新的系結程式庫專案。 您可以使用 Visual Studio for Mac 或 Visual Studio (下列螢幕擷取畫面顯示 Visual Studio, 但 Visual Studio for Mac 很類似)。 將方案命名為**AarBinding**:

    [![建立 AarBindings 專案](binding-an-aar-images/01-new-bindings-library-vs-sml.w160.png)](binding-an-aar-images/01-new-bindings-library-vs.w160.png#lightbox)

2. 此範本包含一個**jar**資料夾, 您可以在其中新增您的。AAR 至系結程式庫專案。 以滑鼠右鍵按一下 [ **jar** ] 資料夾, 然後選取 [**新增 > 現有專案**]:

    [![加入現有專案](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)


3. 流覽至稍早下載的**textanalyzer aar**檔案, 加以選取, 然後按一下 [**新增**]:

    [![新增 textanalayzer. aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)


4. 確認**textanalyzer aar**檔案已成功新增至專案:

    [![已新增 textanalyzer aar 檔案](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5. 將**textanalyzer aar**的組建動作設定為`LibraryProjectZip`。 在 Visual Studio for Mac 中, 以滑鼠右鍵按一下**textanalyzer aar**以設定 [建立] 動作。 在 Visual Studio 中, 您可以在 [**屬性**] 窗格中設定 [建立] 動作:

    [![將 textanalyzer aar 組建動作設定為 LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6. 開啟 [專案屬性] 來設定*目標 Framework*。 如果為。AAR 會使用任何 Android Api, 將目標 Framework 設定為的 API 層級。AAR 預期。 (如需有關目標 Framework 設定和一般 Android API 層級的詳細資訊, 請參閱[瞭解 ANDROID Api 層級](~/android/app-fundamentals/android-api-levels.md))。

    設定系結程式庫的目標 API 層級。 在此範例中, 我們可以免費使用最新的平臺 API 層級 (API 層級 23), 因為我們的**textanalyzer**沒有對 Android api 的相依性:

    [![將目標層級設定為 API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7. 建立系結程式庫。 系結程式庫專案應該成功建立並產生輸出。DLL 位於下列位置:**AarBinding/bin/Debug/AarBinding.dll**



### <a name="using-the-bindings-library"></a>使用系結程式庫

以使用此。DLL 在您的 Xamarin Android 應用程式中, 必須先加入系結程式庫的參考。 請使用下列步驟:

1. 我們會在與系結程式庫相同的解決方案中建立此應用程式, 以簡化此逐步解說。 (使用系結程式庫的應用程式也可以位於不同的解決方案中)。建立新的 Xamarin Android 應用程式: 以滑鼠右鍵按一下方案, 然後選取 [**新增專案**]。 將新專案命名為**bindingtest shoppingcart**:

    [![建立新的 Bindingtest shoppingcart 專案](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2. 以滑鼠右鍵按一下**bindingtest shoppingcart**專案的 [**參考**] 節點, 然後選取 [**新增參考 ...** ]:

    [![按一下 [新增參考]](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3. 選取稍早建立的**AarBinding**專案, 然後按一下 **[確定]** :

    [![檢查 AAR 系結專案](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4. 開啟**bindingtest shoppingcart**專案的 [**參考**] 節點, 以確認**AarBinding**參考是否存在:

    [![AarBinding 列在 [參考] 底下](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)


如果您想要查看系結程式庫專案的內容, 您可以按兩下該參考, 在 **物件瀏覽器**中開啟它。 您可以看到`Com.Xamarin.Textcounter`命名空間的對應內容 (從 JAVA `com.xamarin.textanalyzezr`封裝對應), 而且您可以`TextCounter`查看類別的成員:

[![查看物件瀏覽器](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

上述螢幕擷取畫面會反白`TextAnalyzer`顯示範例應用程式將會呼叫的`NumConsonants`兩個方法: (包裝`numConsonants`基礎 java 方法) `NumVowels`和 (包裝基礎 java `numVowels`方法)。



### <a name="accessing-aar-types"></a>正在.AAR 類型

新增指向系結程式庫之應用程式的參考之後, 您可以存取中的 JAVA 類型。AAR, 如同您存取C#類型一樣 (感謝C#包裝函式)。 C#應用程式程式碼`TextAnalyzer`可以呼叫方法, 如下列範例所示:

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

在上述範例中, 我們要在`TextCounter`類別中呼叫靜態方法。 不過, 您也可以具現化類別和呼叫實例方法。 例如, 如果您的。AAR 會包裝名`Employee`為且具有實例方法`buildFullName`的類別, 您可以`MyClass`具現化並使用它, 如下所示:

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

下列步驟會將程式碼新增至應用程式, 讓它提示使用者輸入文字、 `TextCounter`使用來分析文字, 然後顯示結果。

以下列 XML 取代**bindingtest shoppingcart** layout (**axml**)。 此版面配置具有`EditText`文字輸入的, 以及用於起始母音和輔音計數的兩個按鈕:

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

將**MainActivity.cs**的內容取代為下列程式碼。 如本範例所示, 按鈕事件處理常式會呼叫`TextCounter`位於的已包裝方法。AAR 並使用快顯通知來顯示結果。 請注意系結程式庫命名空間的`Com.Xamarin.Textcounter`語句(在此案例中為):`using`

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

編譯並執行**bindingtest shoppingcart**專案。 應用程式將會啟動, 並在左側顯示幕幕快照 ( `EditText`已使用一些文字進行初始化, 但您可以按一下它來加以變更)。 當您按一下 [**計數母音**] 時, 快顯會顯示正確的母音數目, 如下所示:

[![執行 Bindingtest shoppingcart 的螢幕擷取畫面](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

嘗試點擊 [**計數子音**] 按鈕。 此外, 您也可以修改文字行, 然後再點一下這些按鈕, 以測試不同的母音和輔音計數。



### <a name="accessing-aar-resources"></a>正在.AAR 資源

Xamarin 工具會將**R**資料與合併。AAR 至您應用程式的**資源**類別。 因此, 您可以存取。從您的配置 (和程式碼後置) AAR 資源, 其方式與存取專案**資源**路徑中的資源相同。

若要存取影像資源, 請使用**資源。** 在內封裝之影像的可繪製名稱。AAR. 例如, 您可以參考中的**圖像 .png** 。使用`@drawable/image`下列方法 AAR 檔案:

```xml
<ImageView android:src="@drawable/image" ... />
```

您也可以存取位於中的資源版面配置。AAR. 若要這樣做, 您可以針對內封裝的版面配置使用配置名稱。AAR. 例如：

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

**Textanalyzer. aar**範例包含位於**res/可繪製/猴子 .png**的影像檔案。 讓我們存取此映射資源, 並在我們的範例應用程式中使用它:

編輯**bindingtest shoppingcart**版面配置 (**axml**) `ImageView` , 並將新增至`LinearLayout`容器結尾。 這`ImageView`會顯示 **@drawable/monkey** 找到的影像; 此映射將會從**textanalyzer. aar**的 resource 區段載入:

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

編譯並執行**bindingtest shoppingcart**專案。 當您按下 [**計數子音**] 時, 應用&ndash;程式就會啟動並顯示左側的螢幕擷取畫面, 結果會如右側所示顯示:

[![顯示輔音計數的 Bindingtest shoppingcart](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)


恭喜您！ 您已成功地系結 JAVA 程式庫。AAR!



## <a name="summary"></a>總結

在本逐步解說中, 我們已建立的系結程式庫。AAR 檔案, 將系結程式庫新增至最小測試應用程式, 並執行應用程式, C#以確認我們的程式碼可以呼叫位於的 JAVA 程式碼。AAR 檔案。
此外, 我們已擴充應用程式來存取和顯示位於的影像資源。AAR 檔案。


## <a name="related-links"></a>相關連結

- [建立 JAVA 系結程式庫 (影片)](https://university.xamarin.com/classes#10090)
- [繫結 .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
- [AarBinding (範例)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Bug 44573-一個專案無法系結多個 aar 檔案](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)
