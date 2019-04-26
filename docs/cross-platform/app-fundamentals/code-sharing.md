---
title: 共用程式碼概觀
description: 這份文件比較跨平台專案間共用程式碼的不同方法：共用的專案、 可攜式類別庫和.NET Standard，包括優點和缺點。
ms.prod: xamarin
ms.assetid: B73675D2-09A3-14C1-E41E-20352B819B53
author: conceptdev
ms.author: crdun
ms.date: 08/06/2018
ms.openlocfilehash: 98b5786ae4f071b4d8e8f854561db97aee037fdc
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61227976"
---
# <a name="sharing-code-overview"></a>共用程式碼概觀

_這份文件比較跨平台專案間共用程式碼的不同方法：.NET Standard、 共用專案和可攜式類別程式庫，包括優點和缺點。_

有三種方法來跨平台應用程式間共用程式碼：

- [**.NET 標準程式庫**](#Net_Standard) –.NET Standard 專案可實作跨多個平台上共用的程式碼，而且可以存取大量的.NET Api （視版本而定）。 .NET standard 1.0-1.6 實作 Api，以漸進方式較大的集，而.NET Standard 2.0 提供 （包括適用於 Xamarin 應用程式的.NET Api） 的.NET BCL 的最佳涵蓋範圍。
- [**共用專案**](#Shared_Projects) – 使用共用資產專案類型來組織您的原始程式碼，並使用`#if`編譯器指示詞為必要項目來管理特定平台需求。
- [**可攜式類別庫**](#Portable_Class_Libraries) （已過時）-可攜式類別庫 (Pcl) 可以使用常見的 API 介面、 多重平台為目標並使用介面來提供平台特有的功能。 Visual Studio 的最新版本中已被取代的 Pcl&ndash;改為使用.NET Standard。

程式碼共用策略的目標是要支援此圖中，顯示的架構，其中可以利用單一的程式碼基底的多個平台。

 ![共用程式碼應用程式架構](code-sharing-images/conceptualarchitecture.png "共用程式碼應用程式架構")

本文會比較可幫助您選擇您的應用程式的正確的專案類型的方法。

<a name="Net_Standard" />

## <a name="net-standard-libraries"></a>.NET 標準程式庫

[.NET standard](~/cross-platform/app-fundamentals/net-standard.md)程式庫可提供一組妥善定義可以在不同專案類型，包括 Xamarin.Android 和 Xamarin.iOS 類似的跨平台專案中參考的基底類別程式庫。 .NET standard 2.0 建議的最大的相容性與現有的.NET Framework 程式碼。

![.NET standard 的圖表](code-sharing-images/netstandard.png ".NET Standard 的圖表")

### <a name="benefits"></a>優點

- 可讓您跨多個專案共用程式碼。
- 重構作業一律會更新所有受影響的參考。
- 較大的表面區域的.NET 基底類別庫 (BCL) 可比 PCL 設定檔。 特別是，.NET Standard 2.0 已與.NET Framework 幾乎相同的 API 介面，並建議用於新的應用程式及移植現有的 Pcl。

### <a name="disadvantages"></a>缺點

- 無法使用編譯器指示詞，例如`#if __IOS__`。

### <a name="remarks"></a>備註

.NET standard[類似於 PCL](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries)，但更簡單的模型，可支援平台和較多的 BCL 中的類別。

<a name="Shared_Projects" />

## <a name="shared-projects"></a>共用的專案

[共用專案](~/cross-platform/app-fundamentals/shared-projects.md)包含程式碼檔案和參考它們的任何專案中包含的資產。 共用專案不會產生自己的已編譯的輸出。

此螢幕擷取畫面顯示方案檔 （適用於 Android、 iOS 和 Windows），包含三個應用程式專案**共用**包含一般 C# 原始程式碼檔的專案：

![共用專案的方案](code-sharing-images/sharedsolution.png "共用專案的方案")

下圖，其中每個專案都包含所有的共用原始程式檔中顯示概念性的架構：

![共用專案圖表](code-sharing-images/sharedassetproject.png "共用專案的圖表")

### <a name="example"></a>範例

支援 iOS、 Android 和 Windows 的跨平台應用程式需要每個平台的應用程式專案。 常見的程式碼，位於共用專案中。

範例解決方案會包含下列資料夾和專案 （專案名稱已選擇表達，為您的專案不需要遵循下列命名方針）：

- **共用**– 包含通用於所有專案的程式碼的共用專案。
- **AppAndroid** – Xamarin.Android 應用程式專案。
- **AppiOS** – Xamarin.iOS 應用程式專案。
- **AppWindows** – Windows 應用程式專案。

在這種方式中的三個應用程式專案共用相同的原始程式碼 （C# 中的檔案共用）。 將這三個專案之間共用的共用程式碼的任何編輯。

### <a name="benefits"></a>優點

- 可讓您跨多個專案共用程式碼。
- 共用程式碼可以根據平台使用編譯器指示詞 （例如分支。 使用`#if __ANDROID__`，如下所述[建置跨平台應用程式](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)文件)。
- 應用程式專案可以包含共用程式碼可以利用的平台特定參考 (例如使用`Community.CsharpSqlite.WP7`Tasky 範例適用於 Windows Phone 中)。

### <a name="disadvantages"></a>缺點

- 重構功能，會影響 'inactive' 編譯器指示詞內的程式碼不會更新這些指示詞內的程式碼。
- 不同於大部分其他專案類型，共用的專案會有任何 'output' 組件。 在編譯期間，會視為參考專案的一部分的檔案，並將其編譯至該組件中。 如果您想要共用您的程式碼，做為組件.NET Standard 或可攜式類別程式庫則更好的解決方案。

<a name="Shared_Remarks" />

### <a name="remarks"></a>備註

很好的解決方案，適用於應用程式開發人員撰寫程式碼僅適用於其應用程式中共用 （和不散發給其他開發人員）。

<a name="Portable_Class_Libraries" />

## <a name="portable-class-libraries"></a>可攜式類別庫

> [!TIP]
> .NET standard 2.0 程式庫建議您針對可攜式類別庫。

可攜式類別庫會[以下將詳細討論](~/cross-platform/app-fundamentals/pcl.md)。

![可攜式類別庫圖表](code-sharing-images/portableclasslibrary.png "可攜式類別程式庫的圖表")

### <a name="benefits"></a>優點

- 可讓您跨多個專案共用程式碼。
- 重構作業一律會更新所有受影響的參考。

### <a name="disadvantages"></a>缺點

- 最新版的 Visual Studio 中已被取代，而被建議.NET Standard 程式庫。 這是指[之差異的說明](https://docs.microsoft.com/dotnet/standard/net-standard#comparison-to-portable-class-libraries)PCL 和.NET Standard 之間。
- 無法使用編譯器指示詞。
- 只有.NET framework 的子集可供使用，取決於選取的設定檔 (請參閱[PCL 簡介](~/cross-platform/app-fundamentals/pcl.md)如需詳細資訊)。

### <a name="remarks"></a>備註

PCL 範本會被視為最新版本的 Visual Studio 中已被取代。

## <a name="summary"></a>總結

程式碼共用您所選擇的策略會驅動您的目標平台。 選擇最適合您專案的方法。

.NET standard 是建置可共用的程式碼程式庫 （特別在 NuGet 上發行） 的最佳選擇。 共用的專案，適用於應用程式開發人員計劃使用跨平台應用程式中的許多平台特有的功能。

雖然 PCL 專案仍會繼續支援 Visual Studio 中，.NET Standard 建議針對新的專案。

## <a name="related-links"></a>相關連結

- [建置跨平台應用程式 （主要的文件）](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md)
- [可攜式類別庫](~/cross-platform/app-fundamentals/pcl.md)
- [共用的專案](~/cross-platform/app-fundamentals/shared-projects.md)
- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [案例研究：Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Tasky 範例 (github)](https://github.com/xamarin/mobile-samples/tree/master/Tasky)
- [Tasky 範例使用 PCL (github)](https://github.com/xamarin/mobile-samples/tree/master/TaskyPortable)
