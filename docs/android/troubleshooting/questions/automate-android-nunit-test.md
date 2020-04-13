---
title: 如何將 Android NUnit 測試專案自動化？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/29/2018
ms.openlocfilehash: 1246eeac63a0ae232396d4c2fd69d8bf516f5e3e
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026994"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>如何將 Android NUnit 測試專案自動化？

> [!NOTE]
> 本指南說明如何自動化 Android NUnit 測試專案,而不是 Xamarin.UITest 專案。 Xamarin.UITest 指南[可在此處](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest)找到。

當您在 Visual Studio 中建立**單元測試應用 (Android)** 專案(或 Mac Visual Studio 中的**Android 單元測試**專案)時,默認情況下,此專案不會自動運行您的測試。
要在目標裝置上運行 NUnit 測試,可以建立一個[Android.App.儀器](xref:Android.App.Instrumentation)子類,該子類使用以下命令啟動: 

```shell
adb shell am instrument 
```

以下步驟解釋此過程:

1. 建立名為**TestInstrumentation.cs**的新檔案: 

    ```cs 
    using System;
    using System.Reflection;
    using Android.App;
    using Android.Content;
    using Android.Runtime;
    using Xamarin.Android.NUnitLite;

    namespace App.Tests {

        [Instrumentation(Name="app.tests.TestInstrumentation")]
        public class TestInstrumentation : TestSuiteInstrumentation {

            public TestInstrumentation (IntPtr handle, JniHandleOwnership transfer) : base (handle, transfer)
            {
            }

            protected override void AddTests ()
            {
                AddTest (Assembly.GetExecutingAssembly ());
            }
        }
    }
    ```

    此檔案中,(`Xamarin.Android.NUnitLite.TestSuiteInstrumentation`來自**Xamarin.Android.NUnitLite.dll)**`TestInstrumentation`被子類別來建立 。

2. 實`TestInstrumentation`作函數與方法`AddTests`。 方法`AddTests`控制實際執行的測試。

3. 變更檔案`.csproj`以新增**TestInstrumentation.cs**。 例如：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        ...
        <ItemGroup>
            <Compile Include="TestInstrumentation.cs" />
        </ItemGroup>
        <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
            <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
        </Target>
        ...
    </Project>
    ```

4. 使用以下命令運行單元測試。 取代為`PACKAGE_NAME`應用程式的套件名稱(程式套件名稱可在`/manifest/@package`在**AndroidManifest.xml**中的應用程式屬性中找到):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5. 或者,您可以修改`.csproj`檔案以`RunTests`添加 MSBuild 目標。 這樣,可以使用如下所示的命令調用單元測試:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```

    (請注意,不需要使用此新目標;可以使用較早的命令`adb``msbuild`而不是 .)

有關使用`adb shell am instrument`指令 執行單元測試的詳細資訊,請參閱使用 ADB 執行測試的 Android 開發人員[執行測試](https://developer.android.com/studio/test/command-line.html#RunTestsDevice)。

> [!NOTE]
> 使用[Xamarin.Android 5.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming)版本時,Android 可調用包裝器的預設套件名稱將基於要匯出類型的程式集限定名稱的 MD5SUM。 這允許從兩個不同的程式集提供相同的完全限定的名稱,並且不會收到打包錯誤。 因此,請確保使用`Name``Instrumentation`屬性上的屬性生成可讀的 ACW/類名稱。

_ACW 名稱必須在上述指令中`adb`使用_。
因此,重新命名/重構 C# 類別將`RunTests`需要修改命令以使用正確的 ACW 名稱。
