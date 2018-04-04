---
title: 如何自動執行的 Android NUnit 測試專案？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/29/2018
ms.openlocfilehash: f63a25f36682038b7fcd85d711d980b9e3ec869d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>如何自動執行的 Android NUnit 測試專案？

> [!NOTE]
> 本指南說明如何將自動化 Android NUnit 測試專案中，非 Xamarin.UITest 專案。 您可以找到 Xamarin.UITest 指南[這裡](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)。

當您建立**單元測試應用程式 (Android)** Visual Studio 中的專案 (或**Android 的單元測試**適用於 Mac 的 Visual Studio 專案中)，不會自動執行測試，根據預設將此專案。
若要在目標裝置上執行 NUnit 測試，您可以建立[Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/)使用下列命令來啟動的子類別： 

```shell
adb shell am instrument 
```

下列步驟說明此程序：

1.  建立新的檔案稱為**TestInstrumentation.cs**: 

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
    在此檔案中， [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (從**Xamarin.Android.NUnitLite.dll**) 要建立子類別化`TestInstrumentation`。

2.  實作[TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)建構函式和[AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29)方法。 `AddTests`方法控制項實際上執行哪些測試。

3.  修改`.csproj`檔案以加入**TestInstrumentation.cs**。 例如: 

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

3.  您可以使用下列命令來執行單元測試。 取代`PACKAGE_NAME`同名的應用程式封裝 (封裝名稱可以找到的應用程式中`/manifest/@package`屬性位於**AndroidManifest.xml**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

4.  或者，您可以修改`.csproj`檔案以加入`RunTests`MSBuild 目標。 這可讓要叫用單元測試使用類似下列的命令：

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (請注意，使用這個新的目標不需要，舊版`adb`而不是您可以使用命令`msbuild`。)

如需有關使用`adb shell am instrument`命令來執行單元測試，請參閱 Android 開發人員[使用 ADB 執行測試](https://developer.android.com/studio/test/command-line.html#RunTestsDevice)主題。


> [!NOTE]
> 與[Xamarin.Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming)版本，預設封裝名稱 Android 可呼叫包裝函式會根據正在匯出之型別的組件限定名稱的 MD5SUM。 這可讓相同完整名稱提供從兩個不同的組件，因此無法封裝時發生錯誤。 因此請確定您使用`Name`屬性`Instrumentation`屬性，以產生可讀取的 ACW/類別名稱。

_必須使用 ACW 名稱`adb`上述命令_。
重新命名/重構 C# 類別將因此需要修改`RunTests`命令，以使用正確的 ACW 名稱。

