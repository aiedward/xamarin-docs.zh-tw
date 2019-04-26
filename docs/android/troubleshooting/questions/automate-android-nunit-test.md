---
title: 如何將 Android NUnit 測試專案自動化？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/29/2018
ms.openlocfilehash: b785ef171d2cb00d4f8f5a17f37d49de17fd3da9
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153284"
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>如何將 Android NUnit 測試專案自動化？

> [!NOTE]
> 本指南說明如何將 Android NUnit 測試專案中，不是 Xamarin.UITest 專案自動化。 您可以找到 Xamarin.UITest 輔助線[此處](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)。

當您建立**單元測試應用程式 (Android)** Visual Studio 專案中的 (或**Android 單元測試**Visual Studio for Mac 中的專案)，此專案將會預設不會自動執行您的測試。
若要在目標裝置上執行 NUnit 測試，您可以建立[Android.App.Instrumentation](https://developer.xamarin.com/api/type/Android.App.Instrumentation/)使用下列命令來啟動的子類別： 

```shell
adb shell am instrument 
```

下列步驟說明此程序：

1.  建立新的檔案，稱為**TestInstrumentation.cs**: 

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
    在此檔案中， [Xamarin.Android.NUnitLite.TestSuiteInstrumentation](https://developer.xamarin.com/api/type/Xamarin.Android.NUnitLite.TestSuiteInstrumentation/) (從**Xamarin.Android.NUnitLite.dll**) 會建立子類別化`TestInstrumentation`。

2.  實作[TestInstrumentation](https://developer.xamarin.com/api/constructor/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.TestSuiteInstrumentation/p/System.IntPtr/Android.Runtime.JniHandleOwnership/)建構函式並[AddTests](https://developer.xamarin.com/api/member/Xamarin.Android.NUnitLite.TestSuiteInstrumentation.AddTests%28%29)方法。 `AddTests`方法控制項實際上執行哪些測試。

3.  修改`.csproj`檔案來新增**TestInstrumentation.cs**。 例如：

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

3.  您可以使用下列命令來執行單元測試。 取代`PACKAGE_NAME`應用程式的套件名稱 (封裝名稱可在應用程式的`/manifest/@package`屬性位於**AndroidManifest.xml**):

    ```shell
    adb shell am instrument -w PACKAGE_NAME/app.tests.TestInstrumentation
    ```

4.  或者，您可以在其中修改`.csproj`檔案以加入`RunTests`MSBuild 目標。 這讓您能夠叫用使用單元測試的命令，如下所示：

    ```shell
    msbuild /t:RunTests Project.csproj
    ```
    (請注意，使用這個新的目標不需要; 較早`adb`而不是您可以使用命令`msbuild`。)

如需使用詳細資訊`adb shell am instrument`命令來執行單元測試，請參閱 Android 開發人員[使用 ADB 執行測試](https://developer.android.com/studio/test/command-line.html#RunTestsDevice)主題。


> [!NOTE]
> 具有[Xamarin.Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming) release 的預設封裝名稱 Android 可呼叫包裝函式會根據組件限定名稱的匯出型別 MD5SUM。 這可讓您提供從兩個不同的組件，並取得封裝錯誤相同完整名稱。 因此請確定您使用`Name`屬性上的`Instrumentation`屬性產生的可讀取的 ACW/類別名稱。

_ACW 名稱必須用於`adb`上述命令_。
重新命名/重構C#類別將因此需要修改`RunTests`命令，以使用正確的 ACW 名稱。

