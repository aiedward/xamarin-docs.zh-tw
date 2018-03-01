---
title: .NET Standard
ms.topic: article
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/12/2017
ms.openlocfilehash: 294d28c57978218986d62d1ee6579e8d283b8f72
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="net-standard"></a>.NET Standard

## <a name="using-net-standard-library-projects-to-share-code"></a>共用的程式碼使用.NET 標準程式庫專案

.NET 標準程式庫是計劃提供所有 .NET 執行階段使用的 .NET API 正式規格。 標準程式庫背後的動機是在 .NET 生態系統中建立更高的一致性。
[ECMA 335](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md) 會繼續建立 .NET 執行階段行為的一致性，但沒有 .NET 程式庫實作之 .NET 基底類別庫 (BCL) 的類似規格。

您可以將它視為簡化的新一代的[可攜式類別庫](https://msdn.microsoft.com/library/gg597391.aspx)。
它是以統一的 API，用於所有.NET 平台包括.NET Core 單一程式庫。 您剛建立單一.NET 標準程式庫，並使用它從任何支援.NET 標準平台的執行階段。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

## <a name="xamarin-studio"></a>Xamarin Studio

Xamarin Studio 6.2，可以建立標準的.NET 程式庫專案藉由先建立可攜式程式庫專案：

[ ![](net-standard-images/xs01-sml.png "建立新的可攜式程式庫專案")](net-standard-images/xs01.png)

一旦建立專案之後，以滑鼠右鍵按一下，然後開啟**專案選項**視窗。
在**一般**專案可以轉換成.NET 標準和設定為使用中的特定版本的區段**平台**下拉式清單：

[ ![](net-standard-images/xs02-sml.png "將轉換成.NET 標準一般選項")](net-standard-images/xs02.png)

您可以接著[建立 NuGet 套件](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/existing-library.md)與其他開發人員共用文件庫。

## <a name="visual-studio-for-mac-walkthrough"></a>Visual Studio for Mac 逐步解說

本節逐步解說如何建立及使用.NET 的標準程式庫，使用 Visual Studio for mac。 請參閱完整實作.NET 標準程式庫範例 > 一節。

### <a name="creating-a-net-standard-library"></a>建立.NET 標準程式庫

將.NET 標準程式庫加入至您的方案是相當直接。

1. 在 [加入新的專案] 對話方塊中，選取`.NET Core`類別目錄，然後選取`Class Library(.NET Core)`。

  **注意：**此範本將重新命名為`.NET Standard`的未來版本的 Visual Studio for mac。

  ![建立.NET 核心類別庫](net-standard-images/vsm01.png)

2. .NET 標準程式庫專案會出現在 [方案總管] 中所示。 相依性節點會指出程式庫會使用[NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/)。

  ![在方案中的相依性節點表示.NET 標準](net-standard-images/vsm02.png)

#### <a name="editing-net-standard-library-settings"></a>編輯.NET 標準程式庫設定

可以檢視和變更以專案上按一下滑鼠右鍵並選取.NET 標準程式庫設定`Options`這個螢幕擷取畫面所示：

![編輯專案選項中的標準.NET 目標 framework](net-standard-images/vsm03.png)

您可以在內部變更您的版本`netstandard`藉由變更`Target Framework`下拉式清單中的值。

**此外：**可以編輯`.csproj`直接來變更此值。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

## <a name="visual-studio-windows-walkthrough"></a>Visual Studio (Windows) 的逐步解說

本節逐步解說如何建立及使用.NET 的標準程式庫，使用 Visual Studio。 請參閱完整實作.NET 標準程式庫範例 > 一節。

### <a name="creating-a-net-standard-library"></a>建立.NET 標準程式庫

#### <a name="visual-studio-2017"></a>Visual Studio 2017

將.NET 標準程式庫加入至您的方案是相當直接。

1. 在 [加入新的專案] 對話方塊中，選取`.NET Standard`類別目錄，然後選取`Class Library(.NET Standard)`。

  ![](net-standard-images/vs01.png "建立新的標準.NET 類別庫")

2. .NET 標準程式庫專案會出現在 [方案總管] 中所示。 相依性節點會指出程式庫會使用[NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/)。

  ![](net-standard-images/vs02.png "方案中的標準.NET 專案")

#### <a name="editing-net-standard-library-settings"></a>編輯.NET 標準程式庫設定

可以檢視和變更以專案上按一下滑鼠右鍵並選取.NET 標準程式庫設定`Properties`這個螢幕擷取畫面所示：

![](net-standard-images/vs03.png "參考的.NET 標準程式庫和其他專案一樣")

您可以在內部變更您的版本`netstandard`藉由變更`Target Framework`下拉式清單中的值。

**此外：**可以編輯`.csproj`直接來變更此值。

#### <a name="using-net-standard-library"></a>使用.NET 標準程式庫

一旦建立.NET 標準程式庫之後，您可以加入任何相容的應用程式庫專案的參考，您通常會將參考的方式相同。 在 Visual Studio 中，以滑鼠右鍵按一下 參考 節點，然後選擇 `Add Reference...`然後切換到`Solution : Projects`索引標籤上所示：

![](net-standard-images/vs04.png "在 Visual Studio 中，以滑鼠右鍵按一下 [參考] 節點並選擇 [加入參考...然後切換至方案的專案] 索引標籤所示")

-----


## <a name="related-links"></a>相關連結

- [版本資訊](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#.NET_Standard_Support)
