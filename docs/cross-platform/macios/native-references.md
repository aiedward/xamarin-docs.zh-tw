---
title: 原生參考 iOS、Mac 和系結專案
description: 原生參考可讓您將原生架構內嵌至 Xamarin、Xamarin 或系結專案。
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 1ad7a98b92c34cf956e50ebc7a6cec73580f8f04
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765496"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>IOS、Mac 和系結專案中的原生參考

_原生參考可讓您將原生架構內嵌至 Xamarin 或 Xamarin 專案或系結專案。_

從 iOS 8.0 開始，您可以建立內嵌架構，在應用程式擴充功能與 Xcode 中的主要應用程式之間共用程式碼。 使用原生參考功能，就可以在 Xamarin 中使用這些內嵌架構（以 Xcode 建立）。

> [!IMPORTANT]
> 您無法從任何類型的 Xamarin 或 Xamarin 專案建立內嵌架構，而原生參考只允許使用現有的原生（目標-C）架構。

<a name="Terminology" />

## <a name="terminology"></a>用語

在 iOS 8 （和更新版本）中，**內嵌**的架構可以是內嵌的靜態連結和動態連結的架構。 若要正確散發它們，您必須將它們設為「fat」架構，其中包含您想要使用應用程式_支援的每_個裝置架構的所有配量。

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>靜態與動態架構

**靜態**架構會在編譯時期連結，在此期間，會在執行時間連結**動態**架構，而不需重新連結即可修改因此。 如果您已使用 iOS 8 之前的任何協力廠商架構，則會使用編譯成您應用程式的**靜態架構**。 如需詳細資訊，請參閱 Apple 的[動態連結程式庫程式設計](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1)檔。

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>Embedded 與系統架構

**內嵌**架構會包含在您的應用程式套件組合中，而且只能透過其沙箱存取您的特定應用程式。 **系統**架構會儲存在作業系統層級，並可供裝置上的所有應用程式使用。 目前只有 Apple 能夠建立作業系統層級架構。

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>精簡與Fat Framework

**精簡**架構只包含適用于特定系統架構的已編譯器代碼，其中的**Fat framework**包含多個架構的程式碼。 編譯成架構的每個架構特定程式碼基底稱為「配量」（配量 _）。_ 因此，例如，如果我們有針對兩個 iOS 模擬器架構（i386 和 X86_64）編譯的架構，則會包含兩個配量。

如果您嘗試將此範例架構與您的應用程式一起散發，它會在模擬器上正確執行，但在裝置上會失敗，因為架構不包含 iOS 裝置的任何程式碼特定配量。 為了確保架構在所有實例中都能正常執行，它也需要包含裝置特定的配量，例如 arm64、armv7 和 armv7s。

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>使用內嵌架構

有兩個步驟必須完成，才能在 Xamarin 或 Xamarin. Mac 應用程式中使用內嵌的架構：建立 Fat 架構並內嵌架構。

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>建立 Fat Framework

如上所述，若要能夠在應用程式中使用內嵌的架構，它必須是一個 Fat 架構，其中包含您的應用程式將在其上執行之裝置的所有系統架構配量。

當架構和取用應用程式位於相同的 Xcode 專案時，這不是問題，因為 Xcode 會使用相同的組建設定來建立架構和應用程式。 因為 Xamarin 應用程式無法建立內嵌架構，所以無法使用這項技術。

若要解決此問題， `lipo`命令列工具可以用來將兩個或更多的架構合併成一個包含所有必要配量的 Fat Framework。 如需使用`lipo`命令的詳細資訊，請參閱連結的[原生程式庫](~/ios/platform/native-interop.md)檔。

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>內嵌架構

需要執行下列步驟，才能使用原生參考在 Xamarin 或 Xamarin. Mac 專案中內嵌架構：

1. 建立新的或開啟現有的 Xamarin. iOS、Xamarin 或 Binding 專案。
2. 在 **方案總管**中，以滑鼠右鍵按一下專案名稱，然後選取 **新增** >  **新增原生參考**： 

    [![](native-references-images/ref01.png "在 [方案總管] 中，以滑鼠右鍵按一下專案名稱並選取加入原生參考")](native-references-images/ref01.png#lightbox)
3. 從**開啟**對話方塊方塊中，選取您想要將內嵌，並按一下原生架構名稱**開啟**按鈕： 

    [![](native-references-images/ref02.png "選取要內嵌的原生架構名稱，然後按一下 [開啟] 按鈕")](native-references-images/ref02.png#lightbox)
4. 架構將會新增至專案的樹狀結構： 

    [![](native-references-images/ref03.png "架構將會加入至 [專案] 樹狀結構")](native-references-images/ref03.png#lightbox)

編譯專案時，原生架構會內嵌在應用程式的組合中。

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>應用程式延伸模組和內嵌架構

內部 Xamarin。 iOS 可以利用這項功能，將 Mono 執行時間連結為架構（當部署目標是 > = iOS 8.0 時），因而減少具有延伸模組的應用程式大小（因為 Mono 執行時間只會包含一次）整個應用程式套件組合，而不是一次用於容器應用程式，每個延伸模組一次。

延伸模組會以架構的形式與 Mono 執行時間連結，因為所有的擴充功能都需要 iOS 8.0。

沒有延伸模組和應用程式以 iOS 為目標的應用程式 

<a name="Summary" />

## <a name="summary"></a>總結

本文深入探討如何將原生架構內嵌至 Xamarin 或 Xamarin. Mac 應用程式。
