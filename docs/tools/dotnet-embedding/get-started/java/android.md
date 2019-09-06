---
title: 開始使用 Android
description: 本檔說明如何開始搭配使用 .NET 內嵌與 Android。 討論如何安裝 .NET 內嵌、建立 Android 程式庫專案、使用 Android Studio 專案中產生的輸出，以及其他考慮。
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: conceptdev
ms.author: crdun
ms.date: 03/28/2018
ms.openlocfilehash: 9b0da6f5b195ecef5fd4e5e2b4585b660573a5be
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278566"
---
# <a name="getting-started-with-android"></a>開始使用 Android

除了[開始使用 JAVA](~/tools/dotnet-embedding/get-started/java/index.md)指南中的需求之外，您還需要：

- [Xamarin. Android 7.5](https://visualstudio.microsoft.com/xamarin/)或更新版本
- 使用 JAVA 1.8 [Android Studio](https://developer.android.com/studio/index.html) 3。x

概括而言，我們將：

- 建立C# Android 程式庫專案
- 透過 NuGet 安裝 .NET 嵌入
- 在 Android 程式庫元件上執行 .NET 內嵌
- 在 Android Studio 的 JAVA 專案中使用產生的 AAR 檔案

## <a name="create-an-android-library-project"></a>建立 Android 程式庫專案

開啟 Windows 或 Mac Visual Studio，建立新的 Android 類別庫專案，並將其命名為**hello-csharp**，並將其儲存至 **~/Projects/hello-from-csharp**或 **%USERPROFILE%\Projects\hello-from-csharp**。

新增名為**HelloActivity.cs**的新 android 活動，後面接著 [**資源/版面配置/Axml**] 的 android 版面配置。

將新的`TextView`新增至您的版面配置，並將文字變更為愉快的東西。

您的版面配置來源看起來應該像這樣：

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

在您的活動中，確定您是`SetContentView`以新的版面配置來呼叫：

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
> 別忘了`[Register]`屬性。 如需詳細資訊，請參閱[限制](#current-limitations-on-android)。

建置專案。 產生的元件將會儲存在`bin/Debug/hello-from-csharp.dll`中。

## <a name="installing-net-embedding-from-nuget"></a>從 NuGet 安裝 .NET 內嵌

請遵循這些[指示](~/tools/dotnet-embedding/get-started/install/install.md)，為您的專案安裝和設定 .net 內嵌。

您應該設定的命令調用如下所示：

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

1. 開啟 Android Studio，並建立具有**空白活動**的新專案。
2. 以滑鼠右鍵按一下您的**應用程式**模組，然後選擇 [**新增 > 模組**]。
3. 選取 [匯**入]。JAR/。AAR 封裝**。
4. 使用瀏覽目錄器來尋找 **~/Projects/hello-from-csharp/output/hello_from_csharp.aar** ，然後按一下 **[完成]** 。

![將 AAR 匯入 Android Studio](android-images/androidstudioimport.png)

這會將 AAR 檔案複製到名為**hello_from_csharp**的新模組。

![Android Studio 專案](android-images/androidstudioproject.png)

若要從您的**應用程式**使用新的模組，請以滑鼠右鍵按一下，然後選擇 [**開啟模組設定**]。 在 [相依性] 索引標籤上，加入新的**模組**相依性，然後選擇 [ **hello_from_csharp** **]** 。

![Android Studio 相依性](android-images/androidstudiodependencies.png)

在您的活動中，加入`onResume`新的方法，並使用下列程式碼來C#啟動活動：

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

### <a name="assembly-compression-important"></a>元件壓縮（*重要*）

Android Studio 專案中的 .NET 內嵌需要進行進一步變更。

開啟您應用程式的**gradle**檔案，並新增下列變更：

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin 目前會直接從 APK 載入 .NET 元件，但不需要壓縮元件。

如果您沒有這項設定，應用程式會在啟動時損毀，並將類似下列的內容列印到主控台：

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>執行應用程式

啟動您的應用程式時：

![在模擬器C#中執行的範例 Hello](android-images/hello-from-csharp-android.png)

請注意這裡發生的情況：

- 我們有一個C#類別， `HelloActivity`也就是子類別的 JAVA
- 我們有 Android 資源檔
- 我們在 Android Studio 中使用了這些功能

若要讓此範例能夠運作，請在最後一個 APK 中設定下列各項：

- 已在應用程式啟動時設定 Xamarin. Android
- 包含在**資產/元件**中的 .net 元件
- **Androidmanifest.xml**您C#的活動的 xml 修改等等。
- .NET 程式庫中的 Android 資源和資產
- 適用于任何`Java.Lang.Object`子類別的 [Android 可呼叫包裝函式](~/android/platform/java-integration/android-callable-wrappers.md)

如果您要尋找額外的逐步解說，請查看下列影片，其中示範如何在 Android Studio 專案中內嵌 Charles Petzold 的[FingerPaint 示範](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)：

[![適用于 Android 的 Embeddinator-4000](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>使用 JAVA 1。8

在撰寫本文時，最好的選擇是使用 Android Studio 3.0 （[這裡的下載](https://developer.android.com/studio/index.html)）。

若要在您的應用程式模組的**gradle**檔案中啟用 JAVA 1.8：

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

您也可以查看[Android Studio 的測試專案](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle)，以取得詳細資料。 

如果您想要使用 Android Studio 2.3. x 穩定的，您必須啟用已淘汰的工具鏈：

```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>Android 上目前的限制

現在，如果您的子`Java.Lang.Object`類別化，則 Xamarin 會產生 JAVA stub （android 可呼叫包裝函式），而不是 .net 內嵌。 因此，您必須遵循相同的規則，將匯出C#至 JAVA 做為 Xamarin。 例如：

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

- `[Register]`需要對應到所需的 JAVA 套件名稱
- `[Export]`需要讓 JAVA 能夠看見方法

我們可以在`ViewSubclass` JAVA 中使用，如下所示：

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

深入瞭解[JAVA 與 Xamarin 的整合](~/android/platform/java-integration/index.md)。

## <a name="multiple-assemblies"></a>多個元件

內嵌單一元件相當簡單;不過，您可能會有一個C#以上的元件。 許多時候，您會有 NuGet 套件的相依性，例如 Android 支援程式庫或 Google Play Services，這樣做會進一步使事情複雜化。

這會造成難題，因為 .NET 內嵌需要在最終的 AAR 中包含許多類型的檔案，例如：

- Android 資產
- Android 資源
- Android 原生程式庫
- Android java 來源

您很可能不想要將這些檔案從 Android 支援程式庫或 Google Play Services 納入您的 AAR，但會在 Android Studio 中使用 Google 的官方版本。

以下是建議的方法：

- 傳遞 .NET 內嵌您擁有的任何元件（具有的來源），並想要從 JAVA 呼叫
- 傳遞 .NET 內嵌您需要 Android 資產、原生程式庫或資源的任何元件
- 新增 JAVA 相依性，例如 Android 支援程式庫或 Google Play Services Android Studio

因此您的命令可能是：

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

除非您發現它包含您在 Android Studio 專案中需要的 Android 資產、資源等等，否則您應該從 NuGet 排除任何專案。 您也可以省略不需要從 JAVA 呼叫的相依性，而且連結器_應該_包含所需的程式庫元件。

若要新增 Android Studio 所需的任何 JAVA 相依性，您的**gradle**檔案可能如下所示：

```groovy
dependencies {
    // ...
    compile 'com.android.support:appcompat-v7:25.3.1'
    compile 'com.google.android.gms:play-services-games:11.0.4'
    // ...
}
```

## <a name="further-reading"></a>進一步閱讀

- [Android 上的回呼](~/tools/dotnet-embedding/android/callbacks.md)
- [初稿 Android Research](~/tools/dotnet-embedding/android/index.md)
- [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
- [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [錯誤碼和描述](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>相關連結

- [氣象範例（Android）](https://github.com/jamesmontemagno/embeddinator-weather)
