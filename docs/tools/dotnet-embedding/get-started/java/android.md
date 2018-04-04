---
title: Android 使用者入門
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 03/28/2018
ms.openlocfilehash: ed3d6ae3f8537bfae456c6919743e8c9fbfb2009
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="getting-started-with-android"></a>Android 使用者入門


除了從需求我們[入門 Java](~/tools/dotnet-embedding/get-started/java/index.md)指南也需要：

* [Xamarin.Android 7.5](https://www.visualstudio.com/xamarin/)或更新版本
* [Android Studio 3.x](https://developer.android.com/studio/index.html) java 1.8

為概觀，我們將：

* 建立 C# Android 程式庫專案
* 安裝.NET 內嵌透過 NuGet
* Android 程式庫組件上執行 Embeddinator
* 使用產生的 AAR 檔案，在 Android Studio 中的 Java 專案中

## <a name="create-an-android-library-project"></a>建立 Android 程式庫專案

開啟 Visual Studio for Windows 或 Mac 中建立新的 Android 類別庫專案，其命名`hello-from-csharp`，並將它儲存`~/Projects/hello-from-csharp`或`%USERPROFILE%\Projects\hello-from-csharp`。

新增名為 Android 活動`HelloActivity.cs`，後面接著在 Android 配置`Resource/layout/hello.axml`。

加入新`TextView`版面配置，並變更為有趣的項目文字。

版面配置來源看起來應該像這樣：
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minWidth="25px"
    android:minHeight="25px">
    <TextView
        android:text="Hello from C#!"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center" />
</LinearLayout>
```

在您的活動，請確定您要呼叫`SetContentView`與您的新配置：
```csharp
[Activity(Label = "HelloActivity"),
    Register("hello_from_csharp.HelloActivity")]
public class HelloActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        SetContentView(Resource.Layout.hello);
    }
}
```
*注意： 不要忘記`[Register]`屬性，請參閱下限制的詳細資料*

建置專案，產生的組件將會儲存在`bin/Debug/hello-from-csharp.dll`。

## <a name="installing-net-embedding-from-nuget"></a>安裝.NET NuGet 內嵌

選擇**新增 > 新增 NuGet 封裝...**並安裝`Embeddinator-4000`從 NuGet 封裝管理員： ![NuGet 封裝管理員](android-images/visualstudionuget.png)

這將會安裝`Embeddinator-4000.exe`到`packages/Embeddinator-4000/tools`目錄。

## <a name="run-embeddinator-4000"></a>執行 Embeddinator 4000

我們會加入建置後步驟執行 Embeddinator 並建立為 Android 程式庫專案的組件的原生 AAR 檔案。

在 Visual Studio for Mac，移至_專案選項 > 建置 > 自訂命令_並加入_後建置_步驟。

安裝下列 commnd:
```
mono '${SolutionDir}/packages/Embeddinator-4000.0.2.0.80/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

> [!NOTE]
> 注意： 請務必使用您從 NuGet 所安裝的版本號碼

如果您要做的動作進行中的開發，C# 專案，您也可以加入自訂命令以清除`output`目錄執行 Embeddinator 之前：
```
rm -Rf '${SolutionDir}/output/'
```

![自訂的建置動作](android-images/visualstudiocustombuild.png)

Android AAR 檔案將會放置於`~/Projects/hello-from-csharp/output/hello_from_csharp.aar`。 _注意： 因為 Java 不支援封裝名稱中，會取代連字號。_

### <a name="running-net-embedding-on-windows"></a>內嵌在 Windows 上執行的.NET

基本上，我們將會安裝相同的作業，但在 Windows 上的 Visual Studio 中的功能表會有些許不同。 Shell 命令也有些許不同。

移至**專案選項 > 建置事件**並輸入下列內容**建置後事件命令列**方塊：
```
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

例如，下列螢幕擷取畫面：

![在 Windows 上 Embeddinator](android-images/visualstudiowindows.png)

## <a name="use-the-generated-output-in-an-android-studio-project"></a>在 Android Studio 專案中使用產生的輸出

1. 開啟 Android Studio 並建立新的專案與**空的活動**。
2. 以滑鼠右鍵按一下您`app`模組，然後選擇 **新增 > 模組**。
3. 選取**匯入。JAR /。AAR 封裝**。
4. 使用目錄瀏覽來尋找`~/Projects/hello-from-csharp/output/hello_from_csharp.aar`按一下**完成**。

![匯入 AAR Android Studio](android-images/androidstudioimport.png)

這會將 AAR 檔案複製到新的模組名為`hello_from_csharp`。

![Android Studio 專案](android-images/androidstudioproject.png)

若要使用新的模組，從您`app`，以滑鼠右鍵按一下，然後選擇 **開啟模組設定**。 在**相依性**索引標籤上，加入新**模組相依性**選擇`:hello_from_csharp`。

![Android Studio 相依性](android-images/androidstudiodependencies.png)

在活動中，加入新`onResume`方法，並使用下列程式碼，以啟動我們的 C# 活動：

```java
import hello_from_csharp.*;

public class MainActivity extends AppCompatActivity {
    //... Other stuff here ...
    @Override
    protected void onResume() {
        super.onResume();

        Intent intent = new Intent(this, HelloActivity.class);
        startActivity(intent);
    }
}
```

### <a name="assembly-compression-important"></a>組件壓縮*重要事項*

Android Studio 專案中的.NET 內嵌需要進一步的一項變更。

開啟您的應用程式`build.gradle`檔案，然後加入下列變更：
```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```
直接從 APK Xamarin.Android 目前載入的.NET 組件，但需要未壓縮的組件。

如果您沒有此安裝程式，應用程式會啟動當機，並列印到主控台的結果類似這樣：

```csharp
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>執行應用程式

在啟動您的應用程式：

![在模擬器中執行的 C# 範例中的 hello](android-images/hello-from-csharp-android.png)

請注意怎麼了：

* 我們有 C# 類別， `HelloActivity`，該子類別 Java
* 我們有 Android 資源檔
* 我們在 Android Studio 中從 Java 使用

因此對於此範例運作，下列所有條件都在最終的 APK 的安裝程式：

* Xamarin.Android 設定在應用程式啟動
* 所包含的.NET 組件 `assets/assemblies`
* `AndroidManifest.xml` 修改您的 C# 活動、 等等。
* Android 的資源和.NET 程式庫從資產
* [Android 的可呼叫包裝函式](https://developer.xamarin.com/guides/android/advanced_topics/java_integration_overview/android_callable_wrappers/)任何`Java.Lang.Object`子類別

如果您要尋找詳細的逐步解說，請查看這段影片中內嵌 Charles Petzold [FingerPaint 示範](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint/)Android Studio 專案中這裡：

[![適用於 Android Embeddinator-4000。](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>使用 Java 1.8

從開始撰寫這種情況，最好的選擇是使用 Android Studio 3.0 ([此處下載](https://developer.android.com/studio/index.html))。

若要啟用您的應用程式模組中的 Java 1.8`build.gradle`檔案：
```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```
您也可以查看我們的 Android Studio 測試專案的[這裡](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle)如需詳細資訊。

如果您會想要使用 Android Studio 2.3.x 穩定，您必須啟用已被取代的端子工具鏈：
```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>在 Android 上目前的限制

現在如果您子類別化`Java.Lang.Object`，Xamarin.Android 產生 Java 虛設常式 （Android 可呼叫包裝函式），而不是 Embeddinator。

因此，您必須遵循相同的匯出 C# 為 Xamarin.Android java 規則。

比方說在 C# 中：
```csharp
    [Register("mono.embeddinator.android.ViewSubclass")]
    public class ViewSubclass : TextView
    {
        public ViewSubclass(Context context) : base(context) { }

        [Export("apply")]
        public void Apply(string text)
        {
            Text = text;
        }
    }
```

* `[Register]` 需要對應至所要的 Java 封裝名稱
* `[Export]` 才能顯示 Java 方法

我們可以使用`ViewSubclass`java 就像這樣：
```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

深入了解[Xamarin.Android Java 整合](~/android/platform/java-integration/index.md)。

## <a name="multiple-assemblies"></a>多個組件

內嵌單一組件是直接;不過，它是更有可能會有一個 C# 組件以上。 許多次您將具有相依性 NuGet 封裝，例如 Android 支援程式庫或 Google Play 服務的進一步讓事情更加複雜。

這會導致難題，因為 Embeddinator 需要將許多類型的檔案包含最後 AAR 例如：
* Android 的資產
* Android 的資源
* Android 原生程式庫
* Android 的 java 來源

您最有可能不想要將這些檔案從 Google Play 服務的 Android 支援程式庫包含您 AAR，但想要在 Android Studio 中使用來自 Google 的官方版本。

以下是建議的方法：
* 傳遞 Embeddinator 您所擁有的任何組件 （有來源），而且要從 Java 呼叫
* 傳遞 Embeddinator Android 資產、 原生程式庫或從資源，您需要的任何組件
* 將 Java 相依性，例如 Android 支援 Android Studio 中的文件庫或 Google Play 服務

因此，可能是您的命令：
```
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```
您應該從 NuGet 排除任何項目，除非您找出它包含 Android 資產、 資源等，您必須在 Android Studio 專案中。 您也可以省略您不需要呼叫從 Java 和連結的相依性_應該_包含所需的程式庫組件。

將需要在 Android Studio 中，任何 Java 相依性加入您`build.gradle`檔案看起來如下：
```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>進一步閱讀

* [在 Android 上回呼](~/tools/dotnet-embedding/android/callbacks.md)
* [Android 的初步研究](~/tools/dotnet-embedding/android/index.md)
* [Embeddinator 限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/docs/Contributing.md)
* [錯誤碼與描述](~/tools/dotnet-embedding/errors.md)


## <a name="related-links"></a>相關連結

- [天氣範例 (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
