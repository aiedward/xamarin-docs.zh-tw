---
title: "原生參考"
description: "原生參考可讓您嵌入 Xamarin.iOS 或 Xamarin.Mac 專案或專案繫結的原生的架構。"
ms.topic: article
ms.prod: xamarin
ms.assetid: E53185FB-CEF5-4AB5-94F9-CC9B57C52300
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 5a33993bdef16191b66127dcc68c57661636c0f8
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="native-references"></a>原生參考

_原生參考可讓您嵌入 Xamarin.iOS 或 Xamarin.Mac 專案或專案繫結的原生的架構。_


自 iOS 8.0 已建立內嵌到應用程式擴充功能，並在 Xcode 中主要的應用程式之間共用程式碼架構。 使用原生參考功能則可能 Xamarin.iOS 中取用這些內嵌的架構 （使用 Xcode 建立）。
 
> [!IMPORTANT]
> **注意：**無法從任何類型的 Xamarin.iOS 或 Xamarin.Mac 專案中建立內嵌的架構，原生參考，僅允許現有的原生 (OBJECTIVE-C) 架構的耗用量。




<a name="Terminology" />

## <a name="terminology"></a>用語

在 iOS 8 （含） 以後，**內嵌架構**可以同時內嵌靜態連結和動態連結架構。 要正確地將它們發佈，您必須將它們製成"fat 」 架構，包含所有其_配量_為您想要支援您的應用程式與每個裝置架構。

<a name="Static-vs-Dynamic-Frameworks" />

### <a name="static-vs-dynamic-frameworks"></a>靜態與動態架構

**靜態架構**在編譯時期連結其中**動態架構**連結於執行階段，因此可以修改而不重新連結。 如果您已經使用 iOS 8 之前的任何第 3 合作對象架構，您已使用**靜態 Framework**的已編譯成您的應用程式。 請參閱 Apple[動態程式庫程式設計](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/DynamicLibraries/100-Articles/OverviewOfDynamicLibraries.html#//apple_ref/doc/uid/TP40001873-SW1)文件以取得詳細資料。

<a name="Embedded-vs-System-Frameworks" />

### <a name="embedded-vs-system-frameworks"></a>內嵌的 vs。系統架構

**內嵌架構**隨附於您的應用程式套件組合，而且僅透過其沙箱應用程式特定存取。 **系統架構**儲存作業系統層級，而且可在裝置上的所有應用程式。 目前只有 Apple 並建立作業系統層級架構的能力。

<a name="Thin-vs-Fat-Frameworks" />

### <a name="thin-vs-fat-frameworks"></a>精簡 vs。Fat 架構

**精簡架構**只包含已編譯程式碼的特定系統架構其中**Fat 架構**包含多個架構的程式碼。 每個架構專屬程式碼基底編譯成架構，稱為_配量_。 因此，比方說，如果我們在已編譯的兩個 ios 模擬器架構 （i386 和 X86_64） 的架構，它將包含兩個配量。

如果您嘗試將此應用程式的範例架構的發佈時，它會在模擬器中上, 正常執行，但在裝置上會失敗，因為架構不包含 iOS 裝置的任何程式碼專屬配量。 若要確保在所有情況下，架構將正常運作，它也必須包含例如 arm64、 armv7 和 armv7s 裝置的特定配量。

<a name="Working-with-Embedded-Frameworks" />

## <a name="working-with-embedded-frameworks"></a>使用內嵌的架構

有兩個步驟必須完成工作的 Xamarin.iOS 或 Xamarin.Mac 應用程式中使用內嵌的架構： 建立 Fat 架構和內嵌架構。

<a name="Overview" />

### <a name="creating-a-fat-framework"></a>建立 Fat 架構

如前所述，若要能夠使用應用程式中內嵌 Framework 它必須是 Fat 架構，其中包含所有裝置上執行您的應用程式的系統架構配量。

當架構和取用應用程式位於相同的 Xcode 專案時，這不是問題為 Xcode 會建置架構並使用相同的組建設定的應用程式。 因為 Xamarin 應用程式無法建立內嵌的架構，則無法使用這項技術。

若要解決此問題，`lipo`命令列工具可用來在兩個或多個架構合併成一個 Fat 架構，其中包含所有必要的扇區。 如需有關使用`lipo`命令，請參閱我們[連結的原生程式庫](~/ios/platform/native-interop.md)文件。

<a name="Embedding-a-Framework" />

### <a name="embedding-a-framework"></a>內嵌的架構

後續步驟需要使用原生參考 Xamarin.iOS 或 Xamarin.Mac 專案中內嵌的架構：

1. 建立新的或開啟現有的 Xamarin.iOS、 Xamarin.Mac 或繫結專案。
2. 在**方案總管 中**，以滑鼠右鍵按一下專案名稱，然後選取**新增** > **加入原生參考**: 

    [![](native-references-images/ref01.png "在 [方案總管] 中，以滑鼠右鍵按一下專案名稱並選取 加入原生參考")](native-references-images/ref01.png#lightbox)
3. 從**開啟**對話方塊方塊中，選取您想要將內嵌，並按一下 原生架構名稱**開啟**按鈕： 

    [![](native-references-images/ref02.png "選取內嵌，然後按一下 [Open] 按鈕以原生的架構的名稱")](native-references-images/ref02.png#lightbox)
4. 架構會加入至專案的樹狀結構： 

    [![](native-references-images/ref03.png "架構會加入至專案樹狀結構")](native-references-images/ref03.png#lightbox)

編譯專案之後，原生架構會內嵌在應用程式套件組合。

<a name="App-Extensions-and-Embedded-Frameworks" />

## <a name="app-extensions-and-embedded-frameworks"></a>應用程式擴充功能和內嵌的架構

在內部 Xamarin.iOS 可能需要使用這項功能時要連結的架構為單聲道的執行階段 (當部署目標是 > = iOS 8.0 以上版本)，（因為單聲道的執行階段將會一次包含如，進而降低應用程式大小大幅擴充的應用程式整個應用程式套件組合，而不是容器應用程式，然後一次，每個擴充功能的一次）。

延伸模組會使用連結 Mono 執行階段做的架構，因為所有擴充功能需要 iOS 8.0 以上版本。

不具有擴充功能和應用程式的目標 iOS 應用程式 

<a name="Summary" />

## <a name="summary"></a>總結

這篇文章已取得的 Xamarin.iOS 或 Xamarin.Mac 應用程式中嵌入原生架構的詳細的檢視。

