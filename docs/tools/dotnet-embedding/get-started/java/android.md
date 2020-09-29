---
title: 開始使用 Android
description: 本檔說明如何開始搭配使用 .NET 內嵌與 Android。 它會討論如何安裝 .NET 內嵌、建立 Android 程式庫專案、在 Android Studio 專案中使用產生的輸出，以及其他考慮。
ms.prod: xamarin
ms.assetid: 870F0C18-A794-4C5D-881B-64CC78759E30
author: davidortinau
ms.author: daortin
ms.date: 03/28/2018
ms.openlocfilehash: 813fb08835f30d0fc24f0c04e1f26b1f4e7f83e0
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456545"
---
# <a name="getting-started-with-android"></a>開始使用 Android

除了開始 [使用 JAVA](~/tools/dotnet-embedding/get-started/java/index.md) 指南的需求之外，您還需要：

- [Xamarin. Android 7.5](https://visualstudio.microsoft.com/xamarin/) 或更新版本
- 使用 JAVA 1.8 [Android Studio](https://developer.android.com/studio/index.html) 3。x

大致上，我們將：

- 建立 c # Android 程式庫專案
- 透過 NuGet 安裝 .NET 內嵌
- 在 Android 程式庫元件上執行 .NET 內嵌
- 在 Android Studio 的 JAVA 專案中使用產生的 AAR 檔案

## <a name="create-an-android-library-project"></a>建立 Android 程式庫專案

開啟適用于 Windows 或 Mac 的 Visual Studio、建立新的 Android 類別庫專案、將它命名為 **hello-csharp**，然後將其儲存至 **~/Projects/hello-from-csharp** 或 **%USERPROFILE%\Projects\hello-from-csharp**。

加入名為 **HelloActivity.cs**的新 android 活動，後面接著在 **資源/配置/.Axml**上的 android 版面配置。

將新的加入 `TextView` 至您的版面配置，並將文字變更為有意義的內容。

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

在您的活動中，確定您是 `SetContentView` 使用新的版面配置來呼叫：

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
> 別忘了 `[Register]` 屬性。 如需詳細資訊，請參閱 [限制](#current-limitations-on-android)。

建置專案。 產生的元件將會儲存在中 `bin/Debug/hello-from-csharp.dll` 。

## <a name="installing-net-embedding-from-nuget"></a>從 NuGet 安裝 .NET 內嵌

請遵循下列 [指示](~/tools/dotnet-embedding/get-started/install/install.md) ，為您的專案安裝及設定 .net 內嵌。

您應該設定的命令調用看起來會像這樣：

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

1. 開啟 Android Studio 並建立具有 **空白活動**的新專案。
2. 以滑鼠右鍵按一下您的 **應用程式** 模組，然後選擇 [ **新增 > 模組**]。
3. 選取 [匯 **入]。JAR/。AAR 封裝**。
4. 使用瀏覽目錄器找出 **~/projects/hello-from-csharp/output/hello_from_csharp. aar** ，然後按一下 **[完成]**。

![將 AAR 匯入 Android Studio](android-images/androidstudioimport.png)

這會將 AAR 檔案複製到名為 **hello_from_csharp**的新模組。

![Android Studio 專案](android-images/androidstudioproject.png)

若要從您的 **應用程式**使用新的模組，請按一下滑鼠右鍵並選擇 [ **開啟模組設定**]。 在 [相依性] 索引標籤上加入新的**模組**相依性，然後選擇 [ **： hello_from_csharp** **]** 。

![Android Studio 相依性](android-images/androidstudiodependencies.png)

在您的活動中，加入新的 `onResume` 方法，並使用下列程式碼來啟動 c # 活動：

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

### <a name="assembly-compression-important"></a>元件壓縮 (*重要*) 

您 Android Studio 專案中的 .NET 嵌入需要進行進一步的變更。

開啟您應用程式的 **gradle** 檔案，然後新增下列變更：

```groovy
android {
    // ...
    aaptOptions {
        noCompress 'dll'
    }
}
```

Xamarin 目前會直接從 APK 載入 .NET 元件，但不需要將元件壓縮。

如果您沒有此設定，則應用程式會在啟動時損毀，並在主控台中列印如下內容：

```shell
com.xamarin.hellocsharp A/monodroid: No assemblies found in '(null)' or '<unavailable>'. Assuming this is part of Fast Deployment. Exiting...
```

## <a name="run-the-app"></a>執行應用程式

啟動您的應用程式時：

![在模擬器中執行的 Hello c # 範例](android-images/hello-from-csharp-android.png)

請注意這裡發生的情況：

- 我們有一個 c # 類別，子 `HelloActivity` 類別為 JAVA
- 我們有 Android 資源檔
- 在 Android Studio 中，我們從 JAVA 使用這些功能

若要讓此範例能夠運作，請在最後的 APK 中設定下列各項：

- 在應用程式啟動時設定了 Xamarin. Android
- 包含在**資產/元件**中的 .net 元件
- **AndroidManifest.xml** c # 活動的修改等等。
- .NET 程式庫中的 Android 資源和資產
- 適用于任何子類別的 Android 可呼叫[包裝](~/android/platform/java-integration/android-callable-wrappers.md)函式 `Java.Lang.Object`

如果您要尋找其他逐步解說，請參閱下列影片，其中示範如何在 Android Studio 專案中內嵌 Charles Petzold 的 [FingerPaint 示範](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) ：

[![適用于 Android 的 Embeddinator-4000](https://img.youtube.com/vi/ZVcrXUpCNpI/0.jpg)](https://www.youtube.com/watch?v=ZVcrXUpCNpI)

## <a name="using-java-18"></a>使用 JAVA 1。8

在撰寫本文時，最好的選擇是使用 Android Studio 3.0 ([在這裡下載](https://developer.android.com/studio/index.html)) 。

若要在您的應用程式模組的 **gradle** 檔案中啟用 JAVA 1.8：

```groovy
android {
    // ...
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}
```

您也可以查看 [Android Studio 的測試專案](https://github.com/mono/Embeddinator-4000/blob/master/tests/android/app/build.gradle) ，以取得更多詳細資料。 

如果您想要使用 Android Studio 2.3. x 穩定版，就必須啟用已淘汰的工具鏈：

```groovy
android {
    // ..
    defaultConfig {
        // ...
        jackOptions.enabled true
    }
}
```

## <a name="current-limitations-on-android"></a>Android 目前的限制

現在，如果您的子類別 `Java.Lang.Object` ，則 Xamarin 會產生 JAVA 存根 (Android 可呼叫包裝函式) ，而不是 .net 內嵌。 因此，您必須遵循相同的規則，將 c # 以 Xamarin 的形式匯出至 JAVA。 例如：

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

- `[Register]` 需要對應至所需的 JAVA 套件名稱
- `[Export]` 需要有此方法，才能讓 JAVA 看見方法

我們可以 `ViewSubclass` 在 JAVA 中使用，如下所示：

```java
import mono.embeddinator.android.ViewSubclass;
//...
ViewSubclass v = new ViewSubclass(this);
v.apply("Hello");
```

深入瞭解 [JAVA 與 Xamarin 的整合](~/android/platform/java-integration/index.md)。

## <a name="multiple-assemblies"></a>多個元件

內嵌單一元件很簡單;不過，您可能會有一個以上的 c # 元件。 許多時候，您會有 NuGet 套件的相依性，例如 Android 支援程式庫或 Google Play Services，這會進一步使事情變得複雜。

這會造成一個難題，因為 .NET 內嵌必須將許多類型的檔案包含在最終的 AAR 中，例如：

- Android 資產
- Android 資源
- Android 原生程式庫
- Android java 來源

您很可能不會想要將這些檔案從 Android 支援程式庫或 Google Play Services 併入您的 AAR 中，而是在 Android Studio 中使用 Google 的官方版本。

以下是建議的方法：

- 傳遞 .NET 內嵌任何您擁有的元件 (有) 的來源，而且想要從 JAVA 呼叫
- 將任何您需要 Android 資產、原生程式庫或資源的任何元件傳遞至 .NET
- 在 Android Studio 中新增 JAVA 相依性，例如 Android 支援程式庫或 Google Play Services

因此您的命令可能是：

```shell
mono Embeddinator-4000.exe --gen=Java --platform=Android -c -o output YourMainAssembly.dll YourDependencyA.dll YourDependencyB.dll
```

除非您找出包含您 Android Studio 專案中所需的 Android 資產、資源等專案，否則您應該從 NuGet 排除任何專案。 您也可以省略不需要從 JAVA 呼叫的相依性，而且連結器 _應該_ 包含需要的程式庫部分。

若要新增 Android Studio 所需的任何 JAVA 相依性，您的 **gradle** 檔案看起來可能像這樣：

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
- [初步的 Android 研究](~/tools/dotnet-embedding/android/index.md)
- [.NET 嵌入限制](~/tools/dotnet-embedding/limitations.md)
- [參與開放原始碼專案](https://github.com/mono/Embeddinator-4000/blob/master/Contributing.md)
- [錯誤碼與說明](~/tools/dotnet-embedding/errors.md)

## <a name="related-links"></a>相關連結

- [ (Android) 的氣象範例 ](https://github.com/jamesmontemagno/embeddinator-weather)