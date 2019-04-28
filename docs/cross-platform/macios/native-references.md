---
title: 原生參考 iOS、 Mac 和繫結專案
description: 原生參考可讓您嵌入 Xamarin.iOS、 Xamarin.Mac 或繫結專案的原生的架構。
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 3a497d0bb4674014b8063cb1fbc91eec6e7ae5ea
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61201529"
---
# <a name="native-references-in-ios-mac-and-bindings-projects"></a>在 iOS、 Mac 和繫結專案的原生參考

_原生參考可讓您嵌入 Xamarin.iOS 或 Xamarin.Mac 專案或繫結專案的原生的架構。_

IOS 8.0 以上版本之後已經可以建立內嵌的架構，來共用應用程式擴充功能與在 Xcode 中主要的應用程式之間的程式碼。 使用原生參考功能很可能在 Xamarin.iOS 中使用這些內嵌的架構 （使用 Xcode 建立）。
 
> [!IMPORTANT]
> 無法從任何類型的 Xamarin.iOS 或 Xamarin.Mac 專案中建立內嵌的架構，針對現有的原生 (OBJECTIVE-C) 架構的耗用量只允許原生參考。

<a name="Terminology" />

## <a name="terminology"></a>用語

在 iOS 8 （和更新版本），**內嵌的架構**可以同時內嵌靜態連結和動態連結的架構。 若要正確地散發它們，您必須將它們放入"fat 」 架構，包含所有其_配量_針對每個您想要支援您的應用程式的裝置架構。

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>靜態與動態架構

**靜態架構**spojit až za kompilace 所在**動態架構**連結在執行階段，因此可以修改而不重新連結。 如果您已使用任何第 3 方架構，在 iOS 8 之前，您使用**靜態 Framework** ，都會編譯到您的應用程式。 請參閱 Apple[動態程式庫程式設計](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1)文件，如需詳細資訊。

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>內嵌的 vs。系統架構

**內嵌架構**都會納入您的應用程式套件組合，而且僅可透過其沙箱應用程式特定存取。 **系統架構**會儲存在作業系統層級，而且可在裝置上的所有應用程式。 目前，只有 Apple 具有能夠建立作業系統層級的架構。

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>精簡型的 vs。Fat 架構

**精簡架構**只包含已編譯程式碼特定系統架構的地方**Fat 架構**包含多個架構的程式碼。 每個架構特定程式碼基底成架構編譯指_配量_。 因此，比方說，如果我們有一種架構，而編譯的兩個 iOS 模擬器架構 （i386 及 X86_64） 時，它會包含兩個配量。

如果您嘗試將您的應用程式使用此範例架構的發佈時，它會在模擬器中上, 正常執行，但在裝置上會失敗，因為架構不包含 iOS 裝置的任何程式碼特定配量。 若要確保架構能在所有情況下，它也需要包括例如 arm64、 armv7 和 armv7s 的裝置特定配量。

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>使用內嵌的架構

有兩個必須完成才能使用內嵌的架構中的 Xamarin.iOS 或 Xamarin.Mac 應用程式的步驟：如何建立 Fat 架構，以及內嵌架構。

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>如何建立 Fat 架構

如上所述，若要能夠使用內嵌的架構，以您的應用程式，它必須是 Fat 架構，其中包含所有裝置上執行您的應用程式的系統架構配量。

架構和時使用的應用程式相同的 Xcode 專案中，這並非問題因為 Xcode 將建置的架構和應用程式使用相同的組建設定。 因為 Xamarin 應用程式無法建立內嵌的架構，就無法使用這項技術。

若要解決此問題，`lipo`命令列工具可用來合併成一個 Fat 架構，包含所有必要的配量的兩個或多個架構。 如需有關使用`lipo`命令，請參閱我們[連結原生程式庫](~/ios/platform/native-interop.md)文件。

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>內嵌的架構

後續步驟，才能內嵌 Xamarin.iOS 或 Xamarin.Mac 專案中使用原生參考的架構：

1. 建立新的或開啟現有的 Xamarin.iOS、 Xamarin.Mac 或繫結專案。
2. 在 [**方案總管] 中**，以滑鼠右鍵按一下專案名稱，然後選取**新增** > **加入原生參考**: 

    [![](native-references-images/ref01.png "在 [方案總管] 中，以滑鼠右鍵按一下專案名稱並選取加入原生參考")](native-references-images/ref01.png#lightbox)
3. 從**開啟**對話方塊方塊中，選取您想要將內嵌，並按一下原生架構名稱**開啟**按鈕： 

    [![](native-references-images/ref02.png "選取 原生架構，內嵌，然後按一下 [開啟] 按鈕的名稱")](native-references-images/ref02.png#lightbox)
4. 架構會新增至專案的樹狀結構： 

    [![](native-references-images/ref03.png "此架構會新增至專案樹狀結構")](native-references-images/ref03.png#lightbox)

當編譯專案時，原生架構會內嵌在應用程式的套件組合。

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>應用程式擴充功能和內嵌的架構

Xamarin.iOS 可以充份利用此功能，以連結的架構的 Mono 執行階段的內部 (當部署目標是 > = iOS 8.0 以上版本)，因此可減少應用程式大小大幅擴充功能的應用程式 （因為 Mono 執行階段將會包含一次整個應用程式套件組合，而不是容器應用程式和每個擴充功能的一次一次）。

延伸模組會使用連結 Mono 執行階段的架構，因為所有的延伸模組需要 iOS 8.0 以上版本。

沒有擴充功能和應用程式的目標 iOS 的應用程式 

<a name="Summary" />

## <a name="summary"></a>總結

本文所深入了解的 Xamarin.iOS 或 Xamarin.Mac 應用程式中嵌入原生的架構。

