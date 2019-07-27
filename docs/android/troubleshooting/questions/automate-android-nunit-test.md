---
title: 如何將 Android NUnit 測試專案自動化？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: 94a0bddcb3a9a1e7236bed4b4c95fc38e1f9f0dd
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510430"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>如何將 Android NUnit 測試專案自動化？

> [!NOTE]
> 本指南說明如何將 Android NUnit 測試專案自動化, 而不是 UITest 專案。 您可以在[這裡](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)找到 UITest 指南。

當您在 Visual Studio (或 Visual Studio for Mac 中的**Android 單元測試**專案) 中建立**單元測試應用程式 (android)** 專案時, 此專案預設不會自動執行您的測試。
若要在目標裝置上執行 NUnit 測試, 您可以建立使用下列命令啟動的[Android. App. 檢測](xref:Android.App.Instrumentation)子類別: 

```shell
adb shell am instrument 
```

下列步驟說明此程式:

1.  建立名為**TestInstrumentation.cs**的新檔案: 

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
    在此檔案中`Xamarin.Android.NUnitLite.TestSuiteInstrumentation` , (來自**NUnitLite**) 會被子類別化來建立`TestInstrumentation`。

2.  執行此`TestInstrumentation`函式`AddTests`和方法。 `AddTests`方法會控制實際執行的測試。

3.  修改檔案以新增**TestInstrumentation.cs。** `.csproj` 例如：

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

4.  使用下列命令來執行單元測試。 將`PACKAGE_NAME`取代為應用程式的套件名稱 (套件名稱可在位於**androidmanifest.xml**的`/manifest/@package`應用程式屬性中找到):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

5.  (選擇性) 您可以修改`.csproj`檔案以`RunTests`新增 MSBuild 目標。 這讓您可以使用如下的命令來叫用單元測試:

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (請注意, 不需要使用這個新的目標, `adb` `msbuild`而可以使用先前的命令, 而不是)。

如需使用`adb shell am instrument`命令執行單元測試的詳細資訊, 請參閱 < 使用[ADB 執行測試](https://developer.android.com/studio/test/command-line.html#RunTestsDevice)的 Android 開發人員主題。


> [!NOTE]
> 在[5.0](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming)版中, android 可呼叫包裝函式的預設封裝名稱會以所匯出類型之元件限定名稱的 check md5sum 為基礎。 這可讓您從兩個不同的元件中提供相同的完整名稱, 而不會收到封裝錯誤。 因此, 請確定您使用屬性`Name` ( `Instrumentation` attribute) 上的屬性 (property), 以產生可讀取的 ACW/類別名稱。

在 _`adb`上述命令中, 必須使用 ACW 名稱_。
重新命名/重構C#類別, 因此需要修改`RunTests`命令以使用正確的 ACW 名稱。

