---
title: 如何自動執行的 Android NUnit 測試專案？
ms.topic: article
ms.prod: xamarin
ms.assetid: EA3CFCC4-2D2E-49D6-A26C-8C0706ACA045
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: acb213e8c73013bc9b2482afb45296c4e1f61ab5
ms.sourcegitcommit: 20ca85ff638dbe3a85e601b5eb09b2f95bda2807
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2018
---
# <a name="how-do-i-automate-an-android-nunit-test-project"></a>如何自動執行的 Android NUnit 測試專案？

> [!NOTE]
> 本指南涵蓋 Android NUnit 測試專案中，非 Xamarin.UITest 專案所設定的步驟。 您可以找到 Xamarin.UITest 指南[這裡](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest)。

當您建立 Android 的單元測試專案在 Visual Studio 中的 Mac 或在 Visual Studio 中單元測試應用程式 (Android) 時，依預設它將不會自動執行測試。
若要在目標裝置上執行 NUnit 測試，我們使用`Android.App.Instrumentation`子類別，可建立和執行使用`adb shell am instrument`命令。

首先，我們建立**TestInstrumentation.cs**檔案，建立一個子類別的`Xamarin.Android.NUnitLite.TestSuiteInstrumentation`(宣告於`Xamarin.Android.NUnitLite.dll`)。 `TestInstrumentation(IntPtr, JniHandleOwnership)`建構函式_必須_提供，並將虛擬`AddTests()`必須覆寫方法。
`AddTests()` 控制項實際上執行哪些測試。 這個檔案是主要是重複使用。

下一步`.csproj`必須經過修改以加入`TestInstrumentation.cs`。

（選擇性）`.csproj`可能經過修改以加入`RunTests`MSBuild 目標，這可讓您叫用中的單元測試：

```shell
msbuild /t:RunTests Project.csproj
```

使用新的目標不是必要的。可能會改為使用對應的 adb 命令：

```shell
adb shell am instrument -w @PACKAGE_NAME@/app.tests.TestInstrumentation
```

取代`@PACKAGE\_NAME@`適當地; 它是出現在值**AndroidManifest.xml** `/manifest/@package`屬性。


> [!NOTE]
> *重要*： 與[Xamarin.Android 5.0](https://developer.xamarin.com/releases/android/xamarin.android_5/xamarin.android_5.1/#Android_Callable_Wrapper_Naming)版本，預設封裝名稱 Android 可呼叫包裝函式會根據正在匯出之型別的組件限定名稱的 MD5SUM。 這可讓相同完整名稱提供從兩個不同的組件，因此無法封裝時發生錯誤。 因此請確定您使用\`名稱\`屬性\`檢測\`relationshipend 產生可讀取的 ACW/類別名稱。

_必須使用 ACW 名稱`adb`命令_。 重新命名/重構 C# 類別將因此需要修改`RunTests`命令，以使用正確的 ACW 名稱。

新增至.csproj 檔：

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project DefaultTargets="Build" ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <Compile Include="TestInstrumentation.cs" />
    </ItemGroup>
    <Target Name="RunTests" DependsOnTargets="_ValidateAndroidPackageProperties">
        <Exec Command="&quot;$(_AndroidPlatformToolsDirectory)adb&quot; $(AdbTarget) $(AdbOptions) shell am instrument -w $(_AndroidPackage)/app.tests.TestInstrumentation" />
    </Target>
</Project>
```

**TestInstruments.cs**:

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

