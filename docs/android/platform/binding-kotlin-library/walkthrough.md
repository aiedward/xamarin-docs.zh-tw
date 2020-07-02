---
title: 逐步解說：系結 Android Kotlin 程式庫
description: 本文提供針對現有 Kotlin 程式庫（BubblePicker）建立 Xamarin. Android 系結的實際操作逐步解說。 其中涵蓋的主題包括編譯 Kotlin 程式庫、將其系結，以及在 Xamarin Android 應用程式中使用系結。
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: af926b518c55bd0d6c73180e512dd669e93778f7
ms.sourcegitcommit: a3f13a216fab4fc20a9adf343895b9d6a54634a5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85853056"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>逐步解說：系結 Android Kotlin 程式庫

> [!IMPORTANT]
> 我們目前正在調查 Xamarin 平臺上的自訂系結使用方式。 請接受[**這份問卷調查**](https://www.surveymonkey.com/r/KKBHNLT)，以通知未來的開發工作。

Xamarin 可讓行動開發人員使用 Visual Studio 和 c # 建立跨平臺原生行動應用程式。 您可以使用現成可用的 Android 平臺 SDK 元件，但在許多情況下，您也會想要使用針對該平臺撰寫的協力廠商 Sdk，而 Xamarin 可讓您透過系結來執行此動作。 若要將協力廠商 Android 架構併入您的 Xamarin Android 應用程式，您必須先為其建立 Xamarin. Android 系結，才能在應用程式中使用它。

Android 平臺以及其原生語言和工具會不斷演進，包括最近推出的 Kotlin 語言，其最終設定是取代 JAVA。 有一些已從 JAVA 遷移至 Kotlin 的3d 合作物件 Sdk，並為我們帶來新的挑戰。 雖然 Kotlin 系結程式類似于 JAVA，但它需要額外的步驟和設定設定，才能成功建立並執行為 Xamarin. Android 應用程式的一部分。  

本檔的目的是要說明解決此案例的高階方法，並提供使用簡單範例的詳細逐步指南。

## <a name="background"></a>背景

Kotlin 已于2016年2月發行，並以2017的方式 Android Studio 標準 JAVA 編譯器的替代方案。 稍後在2019中，Google 宣佈了 Kotlin 程式設計語言已成為 Android 應用程式開發人員的慣用語言。 高階系結方法類似于[一般 JAVA 程式庫](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)的系結程式，其中包含幾個重要的 Kotlin 特定步驟。

## <a name="prerequisites"></a>必要條件

為了完成這個逐步解說，您需要：

- [Android Studio](https://developer.android.com/studio)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [JAVA 解編程式](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>建立原生程式庫

第一個步驟是使用 Android Studio 建立原生 Kotlin 程式庫。 程式庫通常是由協力廠商開發人員提供，或可在[Google 的 Maven 存放庫](https://maven.google.com/web/index.html)和其他遠端存放庫中取得。 例如，在本教學課程中，會建立 [氣泡選擇器 Kotlin 程式庫] 的系結：

![GitHub BubblePicker 示範](walkthrough-images/github-bubblepicker-demo.png)

1. 從 GitHub 下載程式庫[的原始程式碼](https://github.com/igalata/Bubble-Picker/archive/develop.zip)，並將其解壓縮至本機資料夾**反升選擇器**。

1. 啟動 Android Studio，然後選取 [**開啟現有的 Android Studio 專案**] 功能表選項選擇 [反升選擇器本機] 資料夾：

    ![Android Studio 開啟專案](walkthrough-images/android-studio-open-project.png)

1. 確認 Android Studio 是最新的，包括 Gradle。 原始程式碼可以成功建立在 Android Studio v 3.5.3，Gradle v 5.4.1 版上。 如需如何將 Gradle 更新為最新 Gradle 版本的指示，[請參閱這裡](https://gradle.org/install/)。

1. 確認已安裝必要的 Android SDK。 原始程式碼需要 Android SDK v25。 開啟 [**工具] > [Sdk 管理員**] 功能表選項以安裝 sdk 元件。

1. 更新並同步處理位於專案資料夾根目錄的**gradle**設定檔：

    - 將 Kotlin 版本設定為1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - 註冊預設 Google 的 Maven 存放庫，以解決支援程式庫相依性：

        ```gradle
        allprojects {
            repositories {
                jcenter()
                maven {
                    url "https://maven.google.com"
                }
            }
        }
        ```

    - 更新設定檔之後，它就不會同步，而且 Gradle 會顯示 [**立即同步**處理] 按鈕，按下並等候同步處理常式完成：

        ![立即 Android Studio Gradle 同步](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > Gradle 的相依性快取可能已損毀，這有時候是在網路連接超時之後發生。 重新下載相依性和同步處理專案（需要網路）。

        > [!TIP]
        > Gradle 組建進程（daemon）的狀態可能已損毀。 停止所有 Gradle 的守護程式可能會解決此問題。 停止 Gradle 組建進程（需要重新開機）。 在 Gradle 進程損毀的情況下，您也可以嘗試關閉 IDE，然後終止所有 JAVA 進程。

        > [!TIP]
        > 您的專案可能使用協力廠商外掛程式，與專案中的其他外掛程式或專案所要求的 Gradle 版本不相容。

1. 開啟右側的 [Gradle] 功能表，流覽至 [ **bubblepicker >** 工作] 功能表，然後按兩下來執行 [**組建**] 工作，並等候組建程式完成：

    ![Android Studio Gradle 執行工作](walkthrough-images/android-studio-gradle-execute-task.png)

1. 開啟 [根資料夾檔案瀏覽器] 並流覽至 [組建] 資料夾： [**反升-選擇器-> bubblepicker-> 組建-> 輸出-> aar**，將**bubblepicker-release aar**檔案儲存為**bubblepicker-v 1.0. aar**，稍後在系結程式中將會用到此檔案：

    ![Android Studio AAR 輸出](walkthrough-images/android-studio-aar-output.png)

AAR 檔案是 Android 封存，其中包含使用此 SDK 執行應用程式所需的已編譯 Kotlin 原始碼和資產。  

## <a name="prepare-metadata"></a>準備中繼資料

第二個步驟是準備中繼資料轉換檔案，這個檔案是由 Xamarin 用來產生個別的 c # 類別。 「Xamarin」系結專案會探索給定 Android 封存中的所有原生類別和成員，隨後產生具有適當中繼資料的 XML 檔案。 手動建立的中繼資料轉換檔案接著會套用至先前產生的基準，以建立用來產生 c # 程式碼的最終 XML 定義檔。

中繼資料會使用 [XPath](https://www.w3.org/TR/xpath/)   語法，並由系結產生器用來影響系結元件的建立。 JAVA 系結[中繼資料](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)一文提供轉換的詳細資訊，可以套用：

1. 建立空的**Metadata.xml**檔案：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. 定義 xml 轉換：

- Native Kotlin 程式庫有兩個相依性，您不想要對 c # world 公開，而是定義兩項轉換來完全忽略它們。 重要的是，不會從產生的二進位檔中移除原生成員，只會產生 c # 類別。 [JAVA 解編程式](http://java-decompiler.github.io/)可以用來識別相依性。 執行工具並開啟稍早建立的 AAR 檔案，因此會顯示 Android 封存的結構，反映所有相依性、值、資源、資訊清單和類別：  

    ![JAVA 解編程式相依性](walkthrough-images/java-decompiler-dependencies.png)

    略過處理這些封裝的轉換是使用 XPath 指示來定義：

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- 原生 `BubblePicker` 類別有兩個方法 `getBackgroundColor` ，而 `setBackgroundColor` 下列轉換會將它變更為 c # `BackgroundColor` 屬性：

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- 不帶正負號 `UInt, UShort, ULong, UByte` 的類型需要特殊處理。 針對這些類型，Kotlin 會自動變更方法名稱和參數類型，這會反映在產生的程式碼中：

    ```Kotlin
    public open fun fooUIntMethod(value: UInt) : String {
        return "fooUIntMethod${value}"
    }
    ```

    此程式碼會編譯成下列 JAVA 位元組程式碼：

    ```Java byte code
    @NotNull
    public String fooUIntMethod-WZ4Q5Ns(int value) {
    return "fooUIntMethod" + UInt.toString-impl(value);
    }
    ```

    此外，相關的類型（例如） `UIntArray, UShortArray, ULongArray, UByteArray` 也會受到 Kotlin 的影響。 方法名稱會變更為包含額外的尾碼，而參數會變更為相同類型之已簽署版本的元素陣列。 在下列範例中，類型的參數 `UIntArray` 會自動轉換成 `int[]` ，而方法名稱會從變更 `fooUIntArrayMethod` 為 `fooUIntArrayMethod--ajY-9A` 。 第二個是由 Xamarin 工具探索，並產生為有效的方法名稱：

    ```Kotlin
    public open fun fooUIntArrayMethod(value: UIntArray) : String {
        return "fooUIntArrayMethod${value.size}"
    }
    ```

    此程式碼會編譯成下列 JAVA 位元組程式碼：

    ```Java byte code
    @NotNull
    public String fooUIntArrayMethod--ajY-9A(@NotNull int[] value) {
        Intrinsics.checkParameterIsNotNull(value, "value");
        return "fooUIntArrayMethod" + UIntArray.getSize-impl(value);
    }
    ```

    為了提供有意義的名稱，可以將下列中繼資料新增至**Metadata.xml**，這會將名稱更新回 Kotlin 程式碼中原本定義的：

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    在 BubblePicker 範例中，沒有任何成員使用不帶正負號的類型，因此不需要進行任何額外的變更。

- Kotlin 具有泛型參數的成員，預設會轉換成 JAVA 的參數。`Lang.Object` 型. 例如，Kotlin 方法具有泛型參數 \<T> ：

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    產生 Xamarin. Android 系結之後，方法會向 c # 公開，如下所示：

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    Xamarin. Android 系結不支援 JAVA 和 Kotlin 泛型，因此會建立通用的 c # 方法來存取一般 API。 做為因應工作，您可以建立包裝函式 Kotlin 程式庫，並以強型別方式而不使用泛型來公開必要的 Api。 或者，您可以在 c # 端建立協助程式，以透過強型別 Api 以相同的方式來解決問題。

    > [!TIP]
    > 藉由轉換中繼資料，任何變更都可以套用至產生的系結。 系結[JAVA 程式庫](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)一文詳細說明如何產生和處理中繼資料。

## <a name="build-a-binding-library"></a>建立系結程式庫

下一個步驟是使用 Visual Studio 系結範本來建立 Xamarin. Android 系結專案、新增必要的中繼資料、原生參考，然後建立專案以產生可耗用的程式庫：

1. 開啟 Visual Studio for Mac，並建立新的 [Xamarin] 系結程式庫專案，並為其命名，在此案例中為**testBubblePicker** ，並完成 wizard。 [Xamarin] 系結範本位於下列路徑： **Android > 程式庫 >** 系結程式庫：

    ![Visual Studio 建立系結](walkthrough-images/visual-studio-create-binding.png)

    在 [轉換] 資料夾中有三個主要的轉換檔案：

    - **Metadata.xml** –允許對最終 API 進行變更，例如變更所產生系結的命名空間。
    - **EnumFields.xml** –包含 JAVA int 常數與 c # 列舉之間的對應。
    - **EnumMethods.xml** –允許將方法參數和傳回類型從 JAVA int 常數變更為 c # 列舉。

    保留空白**EnumFields.xml**並**EnumMethods.xml**檔案，並更新**Metadata.xml**以定義您的轉換。

1. 以在上一個步驟中建立的**Metadata.xml**檔案取代現有的**轉換/Metadata.xml**檔案。 在 [屬性] 視窗中，確認 [檔案**組建] 動作**已設定為 [ **TransformationFile**]：

    ![Visual Studio 中繼資料](walkthrough-images/visual-studio-metadata.png)

1. 將您在步驟1中建立的**bubblepicker-v 1.0. aar**檔案新增至系結專案做為原生參考。 若要新增原生程式庫參考，請開啟搜尋工具並流覽至具有 Android 封存的資料夾。 將封存拖放到方案總管中的 [Jar] 資料夾。 或者，您可以使用 [Jar] 資料夾上的 [**新增**] 內容功能表選項，然後選擇 [**現有**檔案 ...]。 針對本逐步解說的目的，選擇將檔案複製到目錄。 請務必確認 [組建]**動作**已設定為 [ **LibraryProjectZip**]：

    ![Visual Studio 原生參考](walkthrough-images/visual-studio-native-reference.png)

1. 新增[Kotlin. Stdlib.h> NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)套件的參考。 此套件是 Kotlin 標準程式庫的系結。 若沒有這個套件，只有在 Kotlin 程式庫未使用任何 Kotlin 特定類型時，系結才有效，否則所有這些成員都不會公開給 c #，而任何嘗試使用系結的應用程式將會在執行時間損毀。

    > [!TIP]
    > 由於 Xamarin 的限制，系結工具只能為每個系結專案新增一個 Android 封存（AAR）。 如果需要包含多個 AAR 檔案，則需要多個 Xamarin. Android 專案，每個 AAR 各一個。 如果這是本逐步解說的情況，則必須針對每個封存重複執行此步驟的前四個動作。 另一個選項是，您可以手動合併多個 Android 封存做為單一封存，因此您可以使用單一的 Xamarin. Android 系結專案。

1. 最後的動作是建立程式庫，並不會發生任何編譯錯誤。 發生編譯錯誤時，可以使用您稍早建立的 Metadata.xml 檔案來定址和處理，這會新增、移除或重新命名程式庫成員。

## <a name="consume-the-binding-library"></a>使用系結程式庫

最後一個步驟是在 Xamarin Android 應用程式中使用 Xamarin. Android 系結程式庫。 建立新的 Xamarin Android 專案、將參考加入至系結程式庫和轉譯反升選擇器 UI：

1. 建立 Xamarin Android 專案。 使用**android > 應用程式 > Android 應用**程式作為起點，並選取 [**最新] 和 [最大**的目標平臺] 選項，以避免相容性問題。 下列所有步驟都會以這個專案為目標：

    ![Visual Studio 建立應用程式](walkthrough-images/visual-studio-create-app.png)

1. 將專案參考加入至系結專案，或加入先前建立之 DLL 的參考：

    ![Visual Studio 新增系結 Reference.png](walkthrough-images/visual-studio-add-binding-reference.png)

1. 新增 Stdlib.h> NuGet 套件的參考，這是您稍早新增至 Xamarin. Android 系結專案的[Kotlin。](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/) 它會將支援新增至任何需要在執行時間中進行處理的 Kotlin 特定類型。  若沒有這個套件，就可以編譯應用程式，但會在執行時間損毀：

    ![Visual Studio 新增 Stdlib.h> NuGet](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. 將 `BubblePicker` 控制項新增至的 Android 版面配置 `MainActivity` 。 開啟**testBubblePicker/Resources/layout/content_main.xml**檔案，然後附加 BubblePicker 控制項節點做為根 RelativeLayout 控制項的最後一個元素：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <RelativeLayout …>
        …
        <com.igalata.bubblepicker.rendering.BubblePicker
            android:id="@+id/picker"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:backgroundColor="@android:color/white" />
    </RelativeLayout>
    ```

1. 更新應用程式的原始程式碼，並將初始化邏輯新增至，以啟動 `MainActivity` 氣泡選擇器 SDK：

    ```csharp
    protected override void OnCreate(Bundle savedInstanceState)
    {
        ...
        var picker = FindViewById<BubblePicker>(Resource.Id.picker);
        picker.BubbleSize = 20;
        picker.Adapter = new BubblePickerAdapter();
        picker.Listener = new BubblePickerListener(picker);
        ...
    }
    ```

    `BubblePickerAdapter`和 `BubblePickerListener` 是從頭建立的兩個類別，可處理氣泡資料和控制互動：

    ```csharp
    public class BubblePickerAdapter : Java.Lang.Object, IBubblePickerAdapter
    {
        private List<string> _bubbles = new List<string>();
        public int TotalCount => _bubbles.Count;
        public BubblePickerAdapter()
        {
            for (int i = 0; i < 10; i++)
            {
                _bubbles.Add($"Item {i}");
            }
        }

        public PickerItem GetItem(int itemIndex)
        {
            if (itemIndex < 0 || itemIndex >= _bubbles.Count)
                return null;

            var result = _bubbles[itemIndex];
            var item = new PickerItem(result);
            return item;
        }
    }

    public class BubblePickerListener : Java.Lang.Object, IBubblePickerListener
    {
        public View Picker { get; }
        public BubblePickerListener(View picker)
        {
            Picker = picker;
        }

        public void OnBubbleDeselected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Deselected: {item.Title}", Snackbar.LengthLong)
                .SetAction("Action", (Android.Views.View.IOnClickListener)null)
                .Show();
        }

        public void OnBubbleSelected(PickerItem item)
        {
            Snackbar.Make(Picker, $"Selected: {item.Title}", Snackbar.LengthLong)
            .SetAction("Action", (Android.Views.View.IOnClickListener)null)
            .Show();
        }
    }
    ```

1. 執行應用程式，這應該會呈現反升顯示選擇器 UI：

    ![BubblePicker 示範](walkthrough-images/bubble-picker-demo.png)

    此範例需要額外的程式碼來轉譯元素樣式和處理互動，但 `BubblePicker` 控制項已成功建立和啟動。

恭喜！ 您已成功建立 Xamarin Android 應用程式和系結程式庫，其會使用 Kotlin 程式庫。  

您現在應該有一個使用原生 Kotlin 程式庫的基本 Xamarin. Android 應用程式。 這個逐步解說會刻意使用基本範例，以進一步強調所引進的重要概念。 在真實世界的案例中，您可能需要公開較多的 Api，並對它們套用中繼資料轉換。

## <a name="related-links"></a>相關連結

- [Android Studio](https://developer.android.com/studio)
- [Gradle 安裝](https://gradle.org/install/)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [JAVA 解編程式](http://java-decompiler.github.io/)
- [BubblePicker Kotlin 程式庫](https://github.com/igalata/Bubble-Picker)
- [系結 JAVA 程式庫](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [JAVA 系結中繼資料](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [Kotlin. Stdlib.h> NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [範例專案存放庫](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)
