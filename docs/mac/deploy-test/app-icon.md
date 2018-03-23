---
title: 應用程式圖示
description: 本文涵蓋建立 Xamarin.Mac 應用程式圖示所需的影像、將影像統合至 .icns 檔案，以及將圖示納入 Xamarin.Mac 專案中。
ms.topic: article
ms.prod: xamarin
ms.assetid: 675b9405-d9a7-49f0-94ad-417f10a71d11
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: ceeb0d33b00288e2a50dc7f758902c1dbc7ab04a
ms.sourcegitcommit: 5fc1c4d17cd9c755604092cf7ff038a6358f8646
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/17/2018
---
# <a name="application-icon"></a>應用程式圖示

_本文涵蓋建立 Xamarin.Mac 應用程式圖示所需的影像、將影像統合至 .icns 檔案，以及將圖示納入 Xamarin.Mac 專案中。_


## <a name="overview"></a>總覽

在 Xamarin.Mac 應用程式中使用 C# 和 .NET 時，開發人員可以存取在 *Objective-C* 和 *Xcode* 中工作的開發人員可以存取的相同影像與圖示工具。

優良的圖示應會傳達 Xamarin.Mac 應用程式的用途，且提示使用者在使用應用程式時預期會有的體驗。 本文涵蓋建立圖示所需的影像資產、將這些資產封裝至 `AppIcons.appiconset` 檔案，以及在 Xamarin.Mac 應用程式中取用該檔案的所有必要步驟。

![AppIcons.appiconset 編輯器](app-icon-images/intro01.png "AppIcons.appiconset 編輯器")


## <a name="application-icon"></a>應用程式圖示

優良的圖示應會傳達 Xamarin.Mac 應用程式的用途，且提示使用者在使用應用程式時預期會有的體驗。 每個 macOS 應用程式都必須要有數個不同大小的圖示以供顯示在 Finder、Dock、Launchpad，以及電腦的各個其他位置。


## <a name="designing-the-icon"></a>設計圖示

設計應用程式的圖示時，Apple 建議下列祕訣：

- 請考慮讓圖示具有寫實又獨特的圖形。
- 如果 macOS 應用程式有 iOS 對應項目，請不要重複使用 iOS 應用程式的圖示。
- 使用人們容易辨識的通用圖像。
- 儘量簡潔。
- 謹慎使用色彩和陰影，協助圖示述說應用程式用途。
- 避免混合實際文字與「難懂的」文字或線條來表達文字。
- 建立圖示主題的理想化版本，而不要使用實際的相片。
- 避免在圖示中使用 macOS UI 元素。
- 不要在圖示中使用 Apple 圖示的複本。

設計 Xamarin.Mac 應用程式的圖示之前，請先閱讀 Apple [OS X 人性化介面指導方針](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) \(英文\) 的[應用程式圖示資源庫](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Gallery.html#//apple_ref/doc/uid/20000957-CH88-SW1) \(英文\) 和[設計應用程式圖示](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Designing.html#//apple_ref/doc/uid/20000957-CH87-SW1) \(英文\) 小節。


## <a name="required-image-sizes-and-filenames"></a>所需的影像大小和檔案名稱

和開發人員即將用於 Xamarin.Mac 應用程式中的任何影像資源一樣，應用程式圖示也必須提供標準與 Retina 解析度版本。 同樣和任何其他影像一樣，命名圖示檔時，請使用 `@2x` 格式：

- **標準解析度**  - _影像名稱_**.**_副檔名_ (範例：**icon_512x512.png**)
- **高解析度**  - _影像名稱_**@2x.**_副檔名_ (範例：**icon_512x512@2x.png**)

例如，若要提供 512 x 512 版本的應用程式圖示，檔案會命名為 **icon_512x512.png** 和 **icon_512x512@2x.png**。

為了確保所有位置的圖示，在使用者眼中看起來都很出色，請提供下面所列的各種資源大小：

|Filename|大小 (像素)|
|---|---|
|icon_512x512@2x.png|1024 x 1024|
|icon_512x512.png|512 x 512|
|icon_256x256@2x.png|512 x 512|
|icon_256x256.png|256 x 256|
|icon_128x128@2x.png|256 x 256|
|icon_128x128.png|128 x 128|
|icon_32x32@2x.png|64 x 64|
|icon_32x32.png|32 x 32|
|icon_16x16@2x.png|32 x 32|
|icon_16x16.png|16 x 16|

如需詳細資訊，請參閱 Apple 的[提供所有應用程式圖形資源的高解析度版本](https://developer.apple.com/library/mac/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Optimizing/Optimizing.html#//apple_ref/doc/uid/TP40012302-CH7-SW3) \(英文\) 文件。


## <a name="packaging-the-icon-resources"></a>封裝圖示資源

設計圖示並儲存為必要的檔案大小和名稱後，Visual Studio for Mac 能輕鬆將它們指派為可用於 Xamarin.Mac 中的影像資產。

請執行下列動作：

1. 在 [Solution Pad] 中，開啟 [Assets.xcassets] > [AppIcons.appiconset]： 

    ![編輯 AppIcons.appiconset](app-icon-images/intro01.png "編輯 AppIcons.appiconset")
2. 對於必要的各種圖示大小，按一下該圖示，然後選取依上述建立的對應影像檔案： 

    [![選取圖示影像](app-icon-images/intro02.png "選取圖示影像")](app-icon-images/intro02-large.png#lightbox)
3. 儲存您的變更。


## <a name="using-the-icon"></a>使用圖示

建置 `AppIcons.appiconset` 檔案之後，您必須在 Visual Studio for Mac 中將它指派給 Xamarin.Mac 專案。

請執行下列動作：

1. 在 [Solution Pad] 中，按兩下 [Info.plist] 以開啟 [專案選項]。
2. 在 [Mac OS X 應用程式目標] 區段中，按一下 [應用程式圖示] 以選取 `AppIcons.appiconset` 檔案： 

    [![設定圖示集](app-icon-images/icon01.png "設定圖示集")](app-icon-images/icon01-large.png#lightbox)
3. 儲存變更。

當應用程式執行時，即會在 Dock 中顯示新圖示：

![macOS Dock 中的應用程式圖示範例](app-icon-images/icon04.png "macOS Dock 中的應用程式圖示範例")


## <a name="summary"></a>總結

本文詳細說明了使用所需影像來建立 macOS 應用程式圖示、封裝圖示，以及將圖示納入 Xamarin.Mac 專案中。


## <a name="related-links"></a>相關連結

- [MacImages (範例)](https://developer.xamarin.com/samples/mac/MacImages/)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [使用影像](~/mac/app-fundamentals/image.md)
- [macOS 人性化介面指導方針 - 圖示和影像](https://developer.apple.com/macos/human-interface-guidelines/icons-and-images/image-size-and-resolution/) \(英文\)
- [關於 OS X 的高解析度](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html) \(英文\)
- [Icns Builder](https://itunes.apple.com/us/app/icns-builder/id554660130?mt=12) \(英文\)
