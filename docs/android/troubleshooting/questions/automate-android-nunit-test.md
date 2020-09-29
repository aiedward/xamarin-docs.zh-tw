---
title: 如何將 Android NUnit 測試專案自動化？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/29/2018
ms.openlocfilehash: c8c9e721bc46d9071bb2af479a5e1d37b93fce27
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458195"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>如何將 Android NUnit 測試專案自動化？

> [!NOTE]
> 本指南說明如何將 Android NUnit 測試專案自動化，而不是 UITest 專案。 您可以在 [這裡](/appcenter/test-cloud/preparing-for-upload/xamarin-android-uitest)找到 UITest 指南。

當您在 Visual Studio for Mac) 的 Visual Studio (或**Android 單元測試**專案中建立**單元測試應用程式 (android) **專案時，此專案預設不會自動執行您的測試。
若要在目標裝置上執行 NUnit 測試，您可以使用下列命令來建立啟動的 [Android. Instrumentation](xref:Android.App.Instrumentation) 子類別： 

```shell
adb shell am instrument 
```

下列步驟說明此程式：

1. 建立名為 **TestInstrumentation.cs**的新檔案： 

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

    在此檔案中， `Xamarin.Android.NUnitLite.TestSuiteInstrumentation` 從 **Xamarin.Android.NUnitLite.dll**)  (會建立子類別 `TestInstrumentation` 。

2. 執行函式 `TestInstrumentation` 和 `AddTests` 方法。 `AddTests`方法會控制實際執行的測試。

3. 修改檔案 `.csproj` 以新增 **TestInstrumentation.cs**。 例如：

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

4. 使用下列命令來執行單元測試。 `PACKAGE_NAME`以應用程式的套件名稱取代 (套件名稱可在 `/manifest/@package` 位於**AndroidManifest.xml**) 的應用程式屬性中找到：

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5. （選擇性）您可以修改檔案 `.csproj` 來加入 `RunTests` MSBuild 目標。 這可讓您使用類似下列的命令叫用單元測試：

    ```shell
    msbuild /t:RunTests Project.csproj
    ```

     (請注意，不需要使用這個新的目標;您 `adb` 可以使用先前的命令，而不是 `msbuild` 。 ) 

如需使用 `adb shell am instrument` 命令來執行單元測試的詳細資訊，請參閱使用 [ADB 執行測試](https://developer.android.com/studio/test/command-line.html#RunTestsDevice) 的 Android 開發人員主題。

> [!NOTE]
> 使用 [Xamarin. android 5.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) 版時，Android 可呼叫包裝函式的預設封裝名稱會根據所匯出之類型的元件限定名稱 MD5SUM。 這可讓您從兩個不同的元件中提供相同的完整名稱，而不會收到封裝錯誤。 因此，請確定您使用屬性（ `Name` attribute）上的屬性（property） `Instrumentation` 來產生可讀取的 ACW/類別名稱。

_ `adb` 上述命令中必須使用 ACW 名稱_。
重新命名/重構 c # 類別會因此需要修改 `RunTests` 命令以使用正確的 ACW 名稱。