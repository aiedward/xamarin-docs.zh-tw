---
title: UrhoSharp Windows 支援
description: 本文件討論 UrhoSharp 的 Windows 支援。 說明如何建立專案、 設定和啟動 Urho、 整合與 WPF 和 UWP 與整合。
ms.prod: xamarin
ms.assetid: A4F36014-AE4E-4F07-A1AC-F264AAA68ACF
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 471029375d8a61a6c48d94a66d7836807e0da22f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386308"
---
# <a name="urhosharp-windows-support"></a>UrhoSharp Windows 支援

雖然 Urho 是可攜式類別庫，並可讓相同的 API，來使用於各種平台的遊戲邏輯，您仍然需要初始化 Urho，在您的平台特定的驅動程式，以及在某些情況下，您會想要充分利用平台特定功能.

在下列頁面中，假設`MyGame`是子類別的`Application`類別。

**支援的架構：** 只有 64 位元 Windows。

您可以看到完整的範例示範如何使用這我們[範例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples)

## <a name="standalone-project"></a>獨立專案

### <a name="creating-a-project"></a>建立專案

建立主控台專案參考 Urho NuGet，請確定您可以找出資產 （包含的資料目錄的目錄）。

### <a name="configuring-and-launching-urho"></a>設定和啟動 Urho

若啟動您的應用程式，請執行下列動作：

```csharp
DesktopUrhoInitializer.AssetsDirectory = "../Assets";
new MyGame().Run();
```

### <a name="example"></a>範例

[完整的範例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/Desktop)

## <a name="integrated-with-wpf"></a>WPF 與整合

### <a name="creating-a-project"></a>建立專案

建立 WPF 專案參考 Urho NuGet，請確定您可以找出資產 （包含的資料目錄的目錄）。

### <a name="configuring-and-launching-urho-from-wpf"></a>設定和啟動 Urho 從 WPF

建立的子類別`Window`並設定您的資產，像這樣：

```csharp
    public partial class MainWindow : Window
    {
        Application currentApplication;

        public MainWindow()
        {
            InitializeComponent();
            DesktopUrhoInitializer.AssetsDirectory = @"../../Assets";
            Loaded += (s,e) => RunGame (new MyGame ());
        }

        async void RunGame(MyGame game)
        {
            var urhoSurface = new Panel { Dock = DockStyle.Fill };
            WindowsFormsHost.Child = urhoSurface;
            WindowsFormsHost.Focus();
            urhoSurface.Focus();
            await Task.Yield();
            var appOptions = new ApplicationOptions(assetsFolder: "Data")
                {
                    ExternalWindow = RunInSdlWindow.IsChecked.Value ? IntPtr.Zero : urhoSurface.Handle,
                    LimitFps = false, //true means "limit to 200fps"
                };
            currentApplication = Urho.Application.CreateInstance(value.Type, appOptions);
            currentApplication.Run();
        }
    }
```

### <a name="example"></a>範例

[完整的範例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/WPF)

## <a name="integrated-with-uwp"></a>UWP 與整合

### <a name="creating-a-project"></a>建立專案

建立 UWP 專案參考 Urho NuGet，請確定您可以找出資產 （包含的資料目錄的目錄）。

### <a name="configuring-and-launching-urho-from-uwp"></a>設定和啟動 Urho 從 UWP

建立的子類別`Window`並設定您的資產，像這樣：

```csharp
{
            InitializeComponent();
            GameTypes = typeof(Sample).GetTypeInfo().Assembly.GetTypes()
                .Where(t => t.GetTypeInfo().IsSubclassOf(typeof(Application)) && t != typeof(Sample))
                .Select((t, i) => new TypeInfo(t, $"{i + 1}. {t.Name}", ""))
                .ToArray();
            DataContext = this;
            Loaded += (s, e) => RunGame (new MyGame ());
        }

        public void RunGame(TypeInfo value)
        {
            //at this moment, UWP supports assets only in pak files (see PackageTool)
            currentApplication = UrhoSurface.Run(value.Type, "Data.pak");
        }
    }
```

### <a name="example"></a>範例

[完整的範例](https://github.com/xamarin/urho-samples/tree/master/FeatureSamples/UWP)

## <a name="integrated-with-windowsforms"></a>Windows.Forms 與整合

### <a name="creating-a-project"></a>建立專案

建立 Windows.Forms 專案參考 Urho NuGet，請確定您可以找出資產 （包含的資料目錄的目錄）。

### <a name="configuring-and-launching-urho-from-windowsforms"></a>設定和啟動從 Windows.Forms Urho

啟動 Urho 從您的表單，請參閱[完整的範例](https://github.com/xamarin/urho-samples/blob/master/FeatureSamples/WinForms/SamplesForm.cs)
