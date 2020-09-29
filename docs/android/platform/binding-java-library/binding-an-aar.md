---
title: 繫結 .AAR
description: 本逐步解說將提供逐步指示，說明如何從 Android 建立 Xamarin. Android JAVA 系結程式庫。AAR 檔案。
ms.prod: xamarin
ms.assetid: 380413B8-6A99-4BB8-B64C-3EAF9F359C22
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/11/2018
ms.openlocfilehash: 489408400a7a900bf867a4303188cdc927020f7f
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454464"
---
# <a name="binding-an-aar"></a>繫結 .AAR

> [!IMPORTANT]
> 我們目前正在調查 Xamarin 平臺上的自訂系結使用方式。 請填寫 [**這份問卷**](https://www.surveymonkey.com/r/KKBHNLT) ，以通知未來的開發工作。

_本逐步解說將提供逐步指示，說明如何從 Android 建立 Xamarin. Android JAVA 系結程式庫。AAR 檔案。_

## <a name="overview"></a>概觀

*Android 封存 (。AAR) * file 是 Android 程式庫的檔案格式。
以.AAR 檔案是。包含下列各項的 ZIP 封存：

- 已編譯的 JAVA 程式碼
- 資源識別碼
- 資源
- 中繼資料 (例如，活動聲明、許可權) 

在本指南中，我們將逐步解說建立單一系結程式庫的基本概念。AAR 檔案。 如需使用基本程式碼範例) 的一般 JAVA 程式庫系結總覽 (，請參閱系結 [java 程式庫](~/android/platform/binding-java-library/index.md)。

> [!IMPORTANT]
> 系結專案只能包含一個。AAR 檔案。 如果為，則為。AAR 相依于其他。AAR，這些相依性應該包含在其本身的系結專案中，然後加以參考。 請參閱 [Bug 44573](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)。

## <a name="walkthrough"></a>逐步解說

我們會建立一個系結程式庫，以取得在 Android Studio， [textanalyzer. aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true)中建立的範例 Android 封存檔案。 這。AAR 包含的 `TextCounter` 類別具有靜態方法，可計算字串中的母音和子音數目。 此外， **textanalyzer** 會包含影像資源以協助顯示計數結果。

我們會使用下列步驟，從建立系結程式庫。AAR 檔案：

1. 建立新的 JAVA 系結程式庫專案。

2. 新增單一。AAR 檔案至專案。 系結專案可能只會包含單一。Aar。

3. 為設定適當的組建動作。AAR 檔案。

4. 選擇目標 framework。AAR 支援。

5. 建立系結程式庫。

建立系結程式庫之後，我們會開發一個小型 Android 應用程式，以提示使用者輸入文字字串、呼叫。AAR 方法來分析文字，從抓取影像。AAR，並連同影像一起顯示結果。

範例應用程式會存取 `TextCounter` **textanalyzer. aar**的類別：

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

此外，此範例應用程式會取出並顯示封裝于 **textanalyzer. aar**中的影像資源：

[![Xamarin 猴子影像](binding-an-aar-images/00-monkey-sml.png)](binding-an-aar-images/00-monkey.png#lightbox)

此影像資源位於**textanalyzer. aar**中的**res/繪製/monkey.png** 。

### <a name="creating-the-bindings-library"></a>建立系結程式庫

開始進行下列步驟之前，請先下載 [textanalyzer. aar](https://github.com/xamarin/monodroid-samples/blob/master/JavaIntegration/AarBinding/Resources/textanalyzer.aar?raw=true) Android 封存檔案範例：

1. 從 Android 系結程式庫範本開始建立新的系結程式庫專案。 您可以使用 Visual Studio for Mac 或 Visual Studio (以下的螢幕擷取畫面顯示 Visual Studio，但 Visual Studio for Mac 非常類似) 。 將方案命名為 **AarBinding**：

    [![建立 AarBindings 專案](binding-an-aar-images/01-new-bindings-library-vs-sml.w160.png)](binding-an-aar-images/01-new-bindings-library-vs.w160.png#lightbox)

2. 範本包含一個 **jar** 資料夾，您可以在其中加入。AAR (s) 至系結程式庫專案。 在 [ **jar** ] 資料夾上按一下滑鼠右鍵，然後選取 [ **新增 > 現有專案**：

    [![加入現有專案](binding-an-aar-images/02-add-existing-item-vs-sml.png)](binding-an-aar-images/02-add-existing-item-vs.png#lightbox)

3. 流覽至稍早下載的 **textanalyzer aar** 檔案，並選取它，然後按一下 [ **新增**]：

    [![新增 textanalayzer. aar](binding-an-aar-images/03-select-aar-file-vs-sml.png)](binding-an-aar-images/03-select-aar-file-vs.png#lightbox)

4. 確認已成功將 **textanalyzer. aar** 檔案新增至專案：

    [![已新增 textanalyzer. aar 檔案](binding-an-aar-images/04-aar-added-vs-sml.png)](binding-an-aar-images/04-aar-added-vs.png#lightbox)

5. 將 **textanalyzer. aar** 的組建動作設定為 `LibraryProjectZip` 。 在 Visual Studio for Mac 中，以滑鼠右鍵按一下 **textanalyzer aar** ，以設定組建動作。 在 Visual Studio 中，您可以在 [ **屬性** ] 窗格中設定 [組建] 動作) ：

    [![將 textanalyzer aar 組建動作設定為 LibraryProjectZip](binding-an-aar-images/05-embedded-aar-vs-sml.png)](binding-an-aar-images/05-embedded-aar-vs.png#lightbox)

6. 開啟專案屬性以設定 *目標 Framework*。 如果為，則為。AAR 會使用任何 Android Api，將目標 Framework 設定為的 API 層級。AAR 預期。  (如需有關目標 Framework 設定和一般 Android API 層級的詳細資訊，請參閱 [瞭解 ANDROID Api 層級](~/android/app-fundamentals/android-api-levels.md)。 ) 

    設定系結程式庫的目標 API 層級。 在此範例中，我們可以免費使用 (API 層級 23) 的最新平臺 API 層級，因為我們的 **textanalyzer** 沒有 Android api 的相依性：

    [![將目標層級設定為 API 23](binding-an-aar-images/06-set-target-framework-vs-sml.png)](binding-an-aar-images/06-set-target-framework-vs.png#lightbox)

7. 建立系結程式庫。 系結程式庫專案應該會成功建立，並產生輸出。DLL 位於下列位置： **AarBinding/bin/Debug/AarBinding.dll**

### <a name="using-the-bindings-library"></a>使用系結程式庫

以使用此。在您的 Xamarin Android 應用程式中，您必須先新增系結程式庫的參考。 請使用下列步驟：

1. 我們要在與系結程式庫相同的解決方案中建立此應用程式，以簡化此逐步解說。  (使用系結程式庫的應用程式也可以位於不同的解決方案中。 ) 建立新的 Xamarin Android 應用程式：以滑鼠右鍵按一下方案，然後選取 [ **加入新專案**]。 將新專案命名為 **BindingTest**：

    [![建立新的 BindingTest 專案](binding-an-aar-images/07-add-new-project-vs-sml.w157.png)](binding-an-aar-images/07-add-new-project-vs.w157.png#lightbox)

2. 以滑鼠右鍵按一下**BindingTest**專案的 [**參考**] 節點，然後選取 [**加入參考**]：

    [![按一下 [加入參考]](binding-an-aar-images/08-add-reference-vs-sml.png)](binding-an-aar-images/08-add-reference-vs.png#lightbox)

3. 選取稍早建立的 **AarBinding** 專案，然後按一下 **[確定]**：

    [![檢查 AAR 系結專案](binding-an-aar-images/09-choose-aar-binding-vs-sml.png)](binding-an-aar-images/09-choose-aar-binding-vs.png#lightbox)

4. 開啟**BindingTest**專案的 [**參考**] 節點，以確認**AarBinding**參考是否存在：

    [![AarBinding 列在 [參考] 底下](binding-an-aar-images/10-references-shows-aarbinding-vs-sml.png)](binding-an-aar-images/10-references-shows-aarbinding-vs.png#lightbox)

如果您想要查看系結程式庫專案的內容，您可以按兩下參考，在 [ **物件瀏覽器**] 中開啟它。 您可以看到命名空間的對應內容 `Com.Xamarin.Textcounter` (從 JAVA `com.xamarin.textanalyzezr` 封裝對應) ，而且您可以查看類別的成員 `TextCounter` ：

[![查看物件瀏覽器](binding-an-aar-images/11-object-browser-vs-sml.png)](binding-an-aar-images/11-object-browser-vs.png#lightbox)

上述螢幕擷取畫面顯示 `TextAnalyzer` 範例應用程式將會呼叫的兩個方法： (會將 `NumConsonants` 基礎 java `numConsonants` 方法) ，而 (則會將 `NumVowels` 基礎 java `numVowels` 方法) 包裝。

### <a name="accessing-aar-types"></a>訪問。AAR 類型

將參考加入指向系結程式庫的應用程式之後，您就可以存取中的 JAVA 類型。AAR 就像您存取 c # 型別一樣 (感謝 c # 包裝函式) 。 C # 應用程式程式碼可以呼叫 `TextAnalyzer` 方法，如下列範例所示：

```csharp
using Com.Xamarin.Textcounter;
...
int numVowels = TextCounter.NumVowels (myText);
int numConsonants = TextCounter.NumConsonants (myText);
```

在上述範例中，我們要在類別中呼叫靜態方法 `TextCounter` 。 不過，您也可以具現化類別並呼叫實例方法。 例如，如果您的。AAR `Employee` 會包裝具有實例方法且名為的類別 `buildFullName` ，您可以將它具現化 `MyClass` 並使用，如下所示：

```csharp
var employee = new Com.MyCompany.MyProject.Employee();
var name = employee.BuildFullName ();
```

下列步驟會將程式碼新增至應用程式，讓它提示使用者輸入文字、使用 `TextCounter` 來分析文字，然後顯示結果。

以下列 XML 取代 **BindingTest** layout (**.axml**) 。 此配置具有 `EditText` 適用于文字輸入的，以及用來初始化母音和輔音計數的兩個按鈕：

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

以下列程式碼取代 **MainActivity.cs** 的內容。 如這個範例所示，按鈕事件處理常式會呼叫 `TextCounter` 位於中的包裝方法。AAR 並使用快顯通知來顯示結果。 請注意系結連結 `using` 庫命名空間的語句 (在此案例中， `Com.Xamarin.Textcounter`) ：

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

編譯並執行 **BindingTest** 專案。 應用程式將會啟動，並在左側顯示幕幕快照 (`EditText` 使用一些文字來初始化，但您可以按一下它來變更) 。 當您按一下 [ **計數母音**] 時，快顯通知會顯示右邊顯示的母音數目：

[![執行 BindingTest 的螢幕擷取畫面](binding-an-aar-images/12-count-vowels.png)](binding-an-aar-images/12-count-vowels.png#lightbox)

請嘗試點擊 [ **計數子音** ] 按鈕。 此外，您也可以修改文字行，然後再次點擊這些按鈕，以測試不同的母音和輔音計數。

### <a name="accessing-aar-resources"></a>訪問。AAR 資源

Xamarin 工具會合並 **R** 資料。AAR 至您的應用程式 **資源** 類別。 因此，您可以存取。從您的版面配置 (和程式碼後端) AAR 資源，其方式與存取專案 **資源** 路徑中的資源相同。

若要存取影像資源，您可以使用中所封裝影像的 **資源。** Aar。 例如，您可以參考中的 **image.png** 。AAR 檔案，方法是使用 `@drawable/image` ：

```xml
<ImageView android:src="@drawable/image" ... />
```

您也可以存取位於中的資源版面配置。Aar。 若要這樣做，請使用在中封裝之配置的 **資源。版面** 配置名稱。Aar。 例如：

```csharp
var a = new ArrayAdapter<string>(this, Resource.Layout.row_layout, ...);
```

**Textanalyzer. aar**範例包含位於**res/可繪製/monkey.png**的影像檔案。 讓我們存取此映射資源，並在我們的範例應用程式中使用它：

編輯 **BindingTest** 配置 (**.Axml**) ，然後將新增 `ImageView` 至容器的結尾 `LinearLayout` 。 這 `ImageView` 會顯示可** \@ 繪製/猴子**的影像; 此映射將從 textanalyzer 的資源區段載入 **。 aar**：

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

編譯並執行 **BindingTest** 專案。 當您按一下 [計數] 子音時，應用程式會啟動並顯示左邊的螢幕擷取畫面 &ndash; ，顯示的結果會顯示在右邊： **COUNT CONSONANTS**

[![BindingTest 顯示輔音計數](binding-an-aar-images/13-count-consonants.png)](binding-an-aar-images/13-count-consonants.png#lightbox)

恭喜！ 您已成功系結 JAVA 程式庫。Aar！

## <a name="summary"></a>摘要

在這個逐步解說中，我們建立了的系結程式庫。AAR 檔案、將系結程式庫新增至最基本的測試應用程式，然後執行應用程式來確認 c # 程式碼可以呼叫位於的 JAVA 程式碼。AAR 檔案。
此外，我們擴充了應用程式來存取和顯示位於中的影像資源。AAR 檔案。

## <a name="related-links"></a>相關連結

- [建立 JAVA 系結程式庫 (影片) ](https://university.xamarin.com/classes#10090)
- [繫結 .JAR](~/android/platform/binding-java-library/binding-a-jar.md)
- [繫結 Java 程式庫](~/android/platform/binding-java-library/index.md)
- [AarBinding (範例) ](/samples/xamarin/monodroid-samples/javaintegration-aarbinding)
- [Bug 44573-一個專案無法系結多個 aar 檔案](https://bugzilla.xamarin.com/show_bug.cgi?id=44573)