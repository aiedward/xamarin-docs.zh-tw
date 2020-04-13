---
title: 演練:綁定安卓科特林庫
description: 本文提供了為現有的科特林庫"氣泡選取器"創建 Xamarin.Android 綁定的動手演練。 它涵蓋了諸如編譯科特林庫、綁定它以及使用 Xamarin.Android 應用程式中的綁定等主題。
ms.prod: xamarin
ms.assetid: 5E45451F-514F-4F2B-A6E8-599ED2EFDA2C
ms.technology: xamarin-android
author: alexeystrakh
ms.author: alstrakh
ms.date: 02/11/2020
ms.openlocfilehash: cbd7c796cd13aa45dc107bddf06ca44d6adbdf9d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291501"
---
# <a name="walkthrough-bind-an-android-kotlin-library"></a>演練:綁定安卓科特林庫

Xamarin 使行動開發人員能夠使用 Visual Studio 和 C# 創建跨平臺本機行動應用。 您可以開箱即用的 Android 平臺 SDK 元件,但在許多情況下,您還希望使用為該平台編寫的第三方 SDK,Xamarin 允許您透過綁定進行操作。 為了將第三方 Android 框架合併到 Xamarin.Android 應用程式中,您需要為其創建 Xamarin.Android 綁定,然後才能在應用程式中使用它。

Android 平臺及其母語和工具不斷演變,包括最近引入的 Kotlin 語言,該語言最終將取代 JAVA。 有許多 3D 方 SDK 已經從 JAVA 遷移到科特林,它給我們帶來了新的挑戰。 儘管 Kotlin 綁定過程與 Java 類似,但它需要額外的步驟和配置設置才能作為 Xamarin.Android 應用程式的一部分成功構建和運行。  

本文件的目標是概述一種高級方法來解決此方案,並提供詳細的分步指南,並給出一個簡單的示例。

## <a name="background"></a>背景

Kotlin 於 2016 年 2 月發布,並在 2017 年定位為標準 Java 編譯器的替代方案。 2019年晚些時候,谷歌宣佈科特林程式設計語言將成為Android應用開發者的首選語言。 高級綁定方法類似於[常規 JAVA 庫的綁定過程](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/),其中有幾個重要的 Kotlin 特定步驟。

## <a name="prerequisites"></a>Prerequisites

為了完成這個逐步解說，您需要：

- [Android Studio](https://developer.android.com/studio)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Java 解編譯](http://java-decompiler.github.io/)

## <a name="build-a-native-library"></a>編譯本機庫

第一步是使用 Android Studio 構建本地科特林庫。 該庫通常由第三方開發人員提供,或在 Google[的 Maven 儲存庫](https://maven.google.com/web/index.html)和其他遠端儲存庫中提供。 例如,在本教程中,為氣泡選取器科特林庫創建了綁定:

![GitHub 泡泡選取器展示](walkthrough-images/github-bubblepicker-demo.png)

1. 從 GitHub 下載函式庫的[源碼](https://github.com/igalata/Bubble-Picker/archive/develop.zip),並將其解壓縮到本地資料夾**氣泡選取器**。

1. 啟動 Android 工作室並選擇 **「打開現有的 Android Studio 專案**」選單選項,選擇「氣泡選取器」本地資料夾:

    ![安卓工作室開放專案](walkthrough-images/android-studio-open-project.png)

1. 驗證 Android 工作室是否最新,包括 Gradle。 原始程式碼可以成功地構建在Android Studio v3.5.3,Gradle v5.4.1上。 關於如何將 Gradle 更新到最新 Gradle 版本的說明[可在此處找到](https://gradle.org/install/)。

1. 驗證是否安裝了所需的 Android SDK。 原始程式碼需要Android SDK v25。 開啟**工具> SDK 管理器**選單選項來安裝 SDK 元件。

1. 更新並同步位於項目資料夾根目錄的**主 build.gradle**設定檔:

    - 將象牙林版本設定為 1.3.10

        ```gradle
        buildscript {
            ext.kotlin_version = '1.3.10'
        }
        ```

    - 註冊預設 Google 的 Maven 儲存庫,以便可以解決支援庫依賴項:

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

    - 更新設定檔後,它不同步,Gradle 將顯示 **「立即同步」** 按鈕,按它並等待同步過程完成:

        ![安卓工作室格拉德爾同步現在](walkthrough-images/android-studio-gradle-syncnow.png)

        > [!TIP]
        > Gradle 的依賴項緩存可能已損壞,這有時會在網路連接超時後發生。 重新下載依賴項和同步專案(需要網路)。

        > [!TIP]
        > Gradle 生成程序 (守護行程) 的狀態可能已損壞。 停止所有 Gradle 守護進程可能會解決此問題。 停止 Gradle 生成進程(需要重新啟動)。 如果進程損壞,您也可以嘗試關閉 IDE,然後終止所有 Java 進程。

        > [!TIP]
        > 您的專案可能使用第三方外掛程式,該外掛程式與專案中的其他外掛程式或專案請求的 Gradle 版本不相容。

1. 開啟右側的 Gradle 選單,導航到**氣泡選擇>任務**選單,按兩下執行**產生**任務,然後等待生成過程完成:

    ![安卓工作室格拉德爾執行任務](walkthrough-images/android-studio-gradle-execute-task.png)

1. 開啟根資料夾檔案瀏覽器並瀏覽到產生資料夾:**氣泡選取器 ->气泡选取器 ->生成 -> 輸出 -> aar,** 保存**氣泡選擇器-釋放.aar**檔作為**氣泡選取器-v1.0.ar,** 此檔將在稍後的綁定過程中使用:

    ![安卓工作室 AAR 輸出](walkthrough-images/android-studio-aar-output.png)

AAR 檔案是 Android 存檔,其中包含 Android 使用此 SDK 運行應用程式所需的已編譯科特林原始程式碼和資產。  

## <a name="prepare-metadata"></a>準備中繼資料

第二步是準備元數據轉換檔,Xamarin.Android 用於生成相應的 C# 類。 Xamarin.Android 綁定專案將發現給定 Android 存檔中的所有本機類和成員,然後生成具有相應元數據的 XML 檔。 然後,手動創建的元數據轉換檔將應用於以前生成的基線,以創建用於生成 C# 代碼的最終 XML 定義檔。

元數據使用 [XPath](https://www.w3.org/TR/xpath/) 語法,綁定生成器使用該生成器來影響綁定程式集的創建。 [Java 綁定中繼](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)資料文章提供有關轉換的詳細資訊,可以應用這些資訊:

1. 建立空白**的資料.xml**檔案:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <metadata>
    </metadata>
    ```

1. 定義 xml 轉換:

- 本機 Kotlin 庫有兩個依賴項(您不希望向 C# 世界公開)定義兩個轉換以完全忽略它們。 重要提示,本機成員不會從生成的二進位檔中剝離,只有C#類不會生成。 [Java 解編譯](http://java-decompiler.github.io/)可用於標識依賴項。 執行該工具並開啟更早建立的 AAR 檔,因此將顯示 Android 存檔的結構,反映所有依賴項、值、資源、清單和類:  

    ![Java 解編譯相依項](walkthrough-images/java-decompiler-dependencies.png)

    要跳過處理這些套件的轉換是使用 XPath 指令定義的:

    ```xml
    <remove-node path="/api/package[starts-with(@name,'org.jbox2d')]" />
    <remove-node path="/api/package[starts-with(@name,'org.slf4j')]" />
    ```

- 本機`BubblePicker`類別有兩種`getBackgroundColor``setBackgroundColor`方法,以下轉換將將其更改為`BackgroundColor`C# 屬性:

    ```xml
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='getBackground' and count(parameter)=0]" name="propertyName">BackgroundColor</attr>
    <attr path="/api/package[@name='com.igalata.bubblepicker.rendering']/class[@name='BubblePicker']/method[@name='setBackground' and count(parameter)=1 and parameter[1][@type='int']]" name="propertyName">BackgroundColor</attr>
    ```

- 未簽名的類型`UInt, UShort, ULong, UByte`需要特殊處理。 對於這些類型的,Kotlin 會自動更改方法名稱和參數類型,這反映在生成的代碼中:

    ```Kotlin
    public open fun fooUIntMethod(value: UInt) : String {
        return "fooUIntMethod${value}"
    }
    ```

    此代碼編譯為以下 Java 位元組碼:

    ```Java byte code
    @NotNull
    public String fooUIntMethod-WZ4Q5Ns(int value) {
    return "fooUIntMethod" + UInt.toString-impl(value);
    }
    ```

    此外,相關類型,如`UIntArray, UShortArray, ULongArray, UByteArray`也受象牙海岸的影響。 方法名稱將更改為包含其他後綴,參數將更改為相同類型的簽名版本的元素陣列。 `UIntArray`在下面的範例中,類型的參數將自動轉換為`int[]`,方法名稱`fooUIntArrayMethod`從變更為`fooUIntArrayMethod--ajY-9A`。 後者由 Xamarin.Android 工具發現,並作為有效方法名稱生成:

    ```Kotlin
    public open fun fooUIntArrayMethod(value: UIntArray) : String {
        return "fooUIntArrayMethod${value.size}"
    }
    ```

    此代碼編譯為以下 Java 位元組碼:

    ```Java byte code
    @NotNull
    public String fooUIntArrayMethod--ajY-9A(@NotNull int[] value) {
        Intrinsics.checkParameterIsNotNull(value, "value");
        return "fooUIntArrayMethod" + UIntArray.getSize-impl(value);
    }
    ```

    為了給它一個有意義的名稱,可以將以下元資料添加到**Metadata.xml**,這將更新名稱回象碼中最初定義的名稱:

    ```xml
    <attr path="/api/package[@name='com.microsoft.simplekotlinlib']/class[@name='FooClass']/method[@name='fooUIntArrayMethod--ajY-9A']" name="managedName">fooUIntArrayMethod</attr>
    ```

    在氣泡選取器示例中,沒有使用無符號類型的成員,因此不需要其他更改。

- 默認情況下,具有泛型參數的 Kotlin 成員轉換為 Java 的參數。`Lang.Object` 類型。 例如,Kotlin 方法具有泛型\<參數 T>:

    ```Kotlin
    public open fun <T>fooGenericMethod(value: T) : String {
    return "fooGenericMethod${value}"
    }
    ```

    生成 Xamarin.Android 綁定後,該方法將公開給 C#,如下所示:

    ```csharp
    [Register ("fooGenericMethod", "(Ljava/lang/Object;)Ljava/lang/String;", "GetFooGenericMethod_Ljava_lang_Object_Handler")]
    [JavaTypeParameters (new string[] {
        "T"
    })]

    public virtual string FooGenericMethod (Java.Lang.Object value);
    ```

    Xamarin.Android 綁定不支援 JAva 和 Kotlin 泛型,因此創建了訪問泛型 API 的通用 C# 方法。 作為解決方法,您可以創建包裝科特林庫,並在不採用泛型的強類型方式公開所需的 API。 或者,您可以在 C# 端創建幫助器,通過強類型 API 以同樣的方式解決問題。

    > [!TIP]
    > 通過轉換元數據,任何更改都可以應用於生成的綁定。 [綁定 Java 庫](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)一文詳細介紹了如何生成和處理元數據。

## <a name="build-a-binding-library"></a>建構繫結庫

下一步是使用 Visual Studio 綁定範本建立 Xamarin.Android 綁定專案,添加所需的中繼資料、本機引用,然後生成專案以生成消耗性庫:

1. 打開 Mac 的可視化工作室並創建新的 Xamarin.Android 綁定庫專案,為其命名,在此例中**測試 BubblePicker.綁定**並完成精靈。 Xamarin.Android 綁定範本由以下路徑定位 **:Android >库>綁定庫**:

    ![視覺化工作室建立繫結](walkthrough-images/visual-studio-create-binding.png)

    在「轉換」資料夾中有三個主要轉換檔:

    - **元資料.xml** = 允許對最終 API 進行更改,例如更改生成的綁定的命名空間。
    - **EnumFields.xml** = 包含 Java int 常量和 C# 枚舉之間的映射。
    - **EnumMethod.xml** = 允許更改方法參數,並將類型從 Java int 常量返回到 C# 枚舉。

    將**EnumFields.xml**和**Enum 方法.xml**檔保持為空,並更新**元數據.xml**以定義轉換。

1. 將現有的**轉換/中繼資料.xml**檔取代為上一步驟中創建的**Metadata.xml**檔。 在屬性視窗中,驗證檔案**產生操作**是否設定為 **「轉換檔案**」 :

    ![視覺化工作室中繼資料](walkthrough-images/visual-studio-metadata.png)

1. 將步驟 1 中生成的**氣泡選取器-v1.0.aar**檔作為本機引用添加到綁定專案中。 要添加本機庫引用,打開查找器,然後使用 Android 存檔導航到資料夾。 將存檔拖放到解決方案資源管理器中的 Jars 資料夾中。 或者,您可以在 Jars 資料夾上使用 **"添加**上下文"功能表選項,然後選擇 **"現有檔案..."** 選擇將檔案複製到目錄,以便本演練。 請務必驗證**產生操作**是否設定為**函式庫ProjectZip**:

    ![視覺工作室本機參考](walkthrough-images/visual-studio-native-reference.png)

1. 添加對[Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)包的引用。 此包是科特林標準庫的綁定。 如果沒有此包,綁定將僅在 Kotlin 庫不使用任何 Kotlin 特定類型時才起作用,否則所有這些成員將不會接觸到 C#,任何嘗試使用綁定的應用都將在運行時崩潰。

    > [!TIP]
    > 由於 Xamarin.Android 的限制,每個綁定專案只能添加單個 Android 存檔 (AAR) 綁定工具。 如果需要包含多個 AAR 檔,則需要多個 Xamarin.Android 專案,每個 AAR 一個。 如果本演練是這樣,則必須為每個存檔重複此步驟的前四個操作。 作為替代選項,可以將多個 Android 存檔手動合併為單個存檔,因此您可以使用單個 Xamarin.Android 綁定專案。

1. 最後一個操作是生成庫,並且沒有任何編譯錯誤。 在編譯錯誤的情況下,可以使用 Metadata.xml 檔處理和處理它們,您之前透過新增 xml 轉換元資料建立了該檔,該檔將添加、刪除或重命名庫成員。

## <a name="consume-the-binding-library"></a>使用繫結庫

最後一步是在 Xamarin.Android 應用程式中使用 Xamarin.Android 綁定庫。 建立新的 Xamarin.Android 專案,添加對繫結庫的參考並呈現氣泡選取器 UI:

1. 創建 Xamarin.安卓專案。 使用**Android >應用> Android 應用**作為起點,選擇 **「最新和最佳**目標平臺」選項以避免相容性問題。 以下步驟針對此專案:

    ![視覺化工作室建立應用程式](walkthrough-images/visual-studio-create-app.png)

1. 新增對繫結項目的項目參考或新增以前建立的 DLL 的引用:

    ![視覺化工作室加入綁定參考.png](walkthrough-images/visual-studio-add-binding-reference.png)

1. 新增對[Xamarin.Kotlin.StdLib NuGet](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)套件的引用,您之前添加到 Xamarin.Android 綁定專案中。 它增加了對需要提交運行時的任何 Kotlin 特定類型的支援。  如果沒有此包,可以編譯應用程式,但在運行時崩潰:

    ![視覺工作室加入 StdLib NuGet](walkthrough-images/visual-studio-add-stdlib-nuget.png)

1. 將`BubblePicker`控制項添加到`MainActivity`的 Android 佈局中。 開啟**測試氣泡選取器/資源/佈局/content_main.xml**檔案,並將氣泡選取器控制節點追加為根相對佈局控制項的最後一個元素:

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

1. 更新應用程式的源碼並將初始化邏輯到 啟動氣泡取器 SDK`MainActivity`的 。

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

    `BubblePickerAdapter`是`BubblePickerListener`兩個要從頭開始創建的類,它們處理氣泡數據和控制交互:

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

1. 運行應用,應用應呈現氣泡選取器 UI:

    ![泡泡選擇器展示](walkthrough-images/bubble-picker-demo.png)

    該範例需要其他代碼來呈現元素樣式和處理交互,`BubblePicker`但控制項已成功建立和啟動。

恭喜！ 您已成功創建了 Xamarin.Android 應用和綁定庫,它使用象牙海岸庫。  

現在,您應該有一個基本的 Xamarin.Android 應用程式,該應用程式通過 Xamarin.Android 綁定庫使用本機科特林庫。 本演練特意使用基本示例來更好地強調介紹的關鍵概念。 在實際方案中,您可能需要公開更多 API 並將元數據轉換應用於它們。

## <a name="related-links"></a>相關連結

- [Android Studio](https://developer.android.com/studio)
- [格拉德爾安裝](https://gradle.org/install/)
- [Visual Studio for Mac](https://visualstudio.microsoft.com/downloads)
- [Java 解編譯](http://java-decompiler.github.io/)
- [泡泡採摘者科特林圖書館](https://github.com/igalata/Bubble-Picker)
- [繫結 Java 函式庫](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/)
- [XPath](https://www.w3.org/TR/xpath/)
- [Java 繫結中繼資料](https://docs.microsoft.com/xamarin/android/platform/binding-java-library/customizing-bindings/java-bindings-metadata)
- [薩馬林.科特林.斯特利布·努Get](https://www.nuget.org/packages/Xamarin.Kotlin.StdLib/)
- [範例項目儲存庫](https://github.com/alexeystrakh/xamarin-binding-kotlin-framework)
