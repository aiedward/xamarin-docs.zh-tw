---
title: Android 使用者入門
description: 本文件說明如何開始使用.NET 內嵌與 Android。 它討論安裝.NET 內嵌，建立 Android 程式庫專案，使用產生的輸出中的 Android Studio 專案和其他考量。
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: lobrien
ms.author: laobri
ms.date: 03/28/2018
ms.openlocfilehash: e60892edfcf73f3e7cada8923e16bcc1be2c203e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121382"
---
# <a name="getting-started-with-android"></a>Android 使用者入門

除了從需求[開始使用 Java](~/tools/dotnet-embedding/get-started/java/index.md)指南，您還需要：

* [Xamarin.Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更新版本
* [Android Studio 3.x](https://developer.android.com/studio/index.html)使用 Java 1.8

概觀中，我們將會：

* 建立C#Android 程式庫專案
* 安裝.NET 內嵌透過 NuGet
* 執行 Android 程式庫組件上的 .NET 內嵌
* 使用產生的 AAR 檔案，在 Android Studio 中的 Java 專案中

## <a name="create-an-android-library-project"></a>建立 Android 程式庫專案

開啟 Visual Studio for Windows 或 Mac，建立新的 Android 類別庫專案，將其命名**hello 從 csharp**，並將它儲存 **~/Projects/hello-from-csharp**或 **%USERPROFILE%\Projects\hello 從 csharp**。

加入名為新的 Android 活動**HelloActivity.cs**，後面接著在 Android 配置**Resource/layout/hello.axml**。

加入新`TextView`版面配置，來變更要愉悅的項目文字。

您的版面配置來源應該看起來像這樣：

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

在您的活動，請確定您呼叫`SetContentView`與您的新配置：

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

> [!NOTE]
> 別忘了`[Register]`屬性。 如需詳細資訊，請參閱 <<c0> [ 限制](#current-limitations-on-android)。

建置專案。 產生的組件會儲存在`bin/Debug/hello-from-csharp.dll`。

## <a name="installing-net-embedding-from-nuget"></a>安裝來自 NuGet 的.NET 內嵌

請遵循這些[指示](~/tools/dotnet-embedding/get-started/install/install.md)安裝及設定.NET 內嵌為您的專案。

您應該設定的命令引動過程會如下所示：

### <a name="visual-studio-for-mac"></a>Visual Studio for Mac

```shell
mono '${SolutionDir}/packages/Embeddinator-4000.0.4.0.0/tools/Embeddinator-4000.exe' '${TargetPath}' --gen=Java --platform=Android --outdir='${SolutionDir}/output' -c
```

#### <a name="visual-studio-2017"></a>Visual Studio 2017

```shell
set E4K_OUTPUT="$(SolutionDir)output"
if exist %E4K_OUTPUT% rmdir /S /Q %E4K_OUTPUT%
"$(SolutionDir)packages\Embeddinator-4000.0.2.0.80\tools\Embeddinator-4000.exe" "$(TargetPath)" --gen=Java --platform=Android --outdir=%E4K_OUTPUT% -c
```

## <a name="use-the-generated-output-in-an-android-studio-project"></a>在 Android Studio 專案中使用產生的輸出

1. 開啟 Android Studio，並建立新專案**空的活動**。
2. 以滑鼠右鍵按一下您**應用程式**模組，然後選擇**新增 > 模組**。
3. 選取**匯入。JAR /。除了 AAR 封裝**。
4. 使用目錄瀏覽來尋找 **~/Projects/hello-from-csharp/output/hello_from_csharp.aar**然後按一下**完成**。

![匯入 AAR Android Studio](android-images/androidstudioimport.png)

這會將 AAR 檔案複製到新的模組，稱為**hello_from_csharp**。

![Android Studio 專案](android-images/androidstudioproject.png)

若要使用新的模組，從您**應用程式**，以滑鼠右鍵按一下，然後選擇**開啟模組設定**。 在 **相依性**索引標籤上，加入新**模組相依性**，然後選擇  **: hello_from_csharp**。

![Android Studio 中的相依性](android-images/androidstudiodependencies.png)

在您的活動中，加入新`onResume`方法，並使用下列程式碼來啟動C#活動：

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

### <a name="assembly-compression-important"></a>組件壓縮 (*重要*)

.NET 內嵌在您的 Android Studio 專案中需要一項其他變更。

開啟您的應用程式**build.gradle**檔案，並新增下列變更：

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin.Android 目前載入.NET 組件，直接從 APK，但它需要未壓縮的組件。

如果您沒有這項設定，應用程式會啟動時當機，並會類似下面的列印到主控台：

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>執行應用程式

一旦啟動您的應用程式：

![從 helloC#在模擬器中執行的範例](android-images/hello-from-csharp-android.png)

請注意，為什麼會這樣：

* 我們有C#類別， `HelloActivity`，該子類別 Java
* 我們有 Android 資源檔
* 我們在 Android Studio 中從 Java 使用

此範例能夠運作，下列所有設定在最終 APK:

* 在應用程式啟動設定 Xamarin.Android
* 所包含的.NET 組件**資產/組件**
* **AndroidManifest.xml**修改您C#等活動。
* Android 資源和資產的.NET 程式庫
* [Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)任何`Java.Lang.Object`子類別

如果您所需的其他逐步解說，請參閱下列影片，示範如何內嵌 Charles Petzold [FingerPaint 示範](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/FingerPaint/)Android Studio 專案中：

[![適用於 Android 的 Embeddinator 4000](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>使用 Java 1.8

截至撰寫這種情況，最佳的選項是使用 Android Studio 3.0 ([這裡下載](https://developer.android.com/studio/index.html))。

若要啟用您的應用程式模組中的 Java 1.8 **build.gradle**檔案：

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

您也可以看看[測試的 Android Studio 專案](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle)如需詳細資訊。 

如果您想要使用 Android Studio 2.3.x 穩定，您必須啟用已被取代的 Jack 工具鏈：

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

現在，如果您的子類別`Java.Lang.Object`，Xamarin.Android 會產生 Java 虛設常式 （Android 可呼叫包裝函式），而不是.NET 內嵌。 基於這個原因，您必須遵循相同的規則，匯出C#為 Xamarin.Android 的 java。 例如: 

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

* `[Register]` 需要對應至所需的 Java 套件名稱
* `[Export]` 才能顯示 Java 方法

我們可以使用`ViewSubclass`在 Java 中就像這樣：

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

深入了解[Xamarin.Android 使用 Java 整合](~/android/platform/java-integration/index.md)。

## <a name="multiple-assemblies"></a>多個組件

內嵌的單一組件很簡單;不過，它是很有可能會有一個以上C#組件。 許多次您將 NuGet 封裝，例如 Android 支援程式庫或 Google Play 服務，進一步使項目上有相依性。

這會造成一個進退兩難的局面，因為.NET 內嵌需要將包含許多類型的檔案最終 AAR，例如：

* Android 資產
* Android 資源
* Android 的原生程式庫
* Android 的 java 來源

您很可能不想將包含您 AAR，這些檔案從 Android 支援程式庫或 Google Play 服務，但想要在 Android Studio 中使用來自 Google 的官方版本。

以下是建議的方法：

* 傳遞.NET 內嵌您擁有的任何組件 （具有來源），而且想要呼叫從 Java
* 傳遞.NET 內嵌 Android 資產、 原生程式庫或從資源，您需要的任何組件
* 新增 Java 相依性，例如 Android 支援 Android Studio 中的文件庫或 Google Play 服務

因此，可能是您的命令：

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

您必須將任何項目從 NuGet 中排除，除非您找出它包含 Android 資產、 資源等，您必須在 Android Studio 專案中。 您也可以省略您不需要從 Java 和連結器呼叫的相依性_應該_包含所需的程式庫組件。

若要加入任何 Java 相依性，需要在 Android Studio 中，您**build.gradle**檔案可能看起來如下：

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>進一步閱讀

* [在 Android 上的回撥](~/tools/dotnet-embedding/android/callbacks.md)
* [Android 的初步研究](~/tools/dotnet-embedding/android/index.md)
* [.NET 內嵌限制](~/tools/dotnet-embedding/limitations.md)
* [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
* [錯誤碼和描述](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>相關連結

- [天氣範例 (Android)](https://github.com/jamesmontemagno/embeddinator-weather)
