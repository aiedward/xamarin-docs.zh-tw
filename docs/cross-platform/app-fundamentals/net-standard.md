---
title: 使用.NET 標準程式庫來共用程式碼
description: 本文件說明如何使用.NET 標準程式庫共用的程式碼。 它討論建立.NET Standard 程式庫、 編輯其設定，和在應用程式中使用它。
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: 65ba1915a2a968a14f0ce21bcada76e1b83531b0
ms.sourcegitcommit: 46bb04016d3c35d91ff434b38474e0cb8197961b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2018
ms.locfileid: "39270651"
---
# <a name="net-standard-library-code-sharing"></a>.NET 標準程式庫共用程式碼

.NET standard 程式庫有統一的 API，包括 Xamarin 和.NET Core 的所有.NET 平台。 建立單一的.NET Standard 程式庫，並從任何支援.NET Standard 平台的執行階段使用它。 請參閱[此圖表](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support)支援的平台的詳細資料。

雖然.NET Standard 版本 1.0 到 1.6 版提供以累加方式較大的.NET framework 的子集，.NET Standard 2.0 Xamarin 應用程式以及移植現有的可攜式類別庫，就會提供最佳的層級的支援。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

本節逐步解說如何建立及使用 Visual Studio for mac 中使用.NET Standard 程式庫

### <a name="creating-a-net-standard-library"></a>建立.NET Standard 程式庫

將.NET Standard 程式庫新增至您的解決方案是相當直接。

1. 在 **加入新的專案**對話方塊中，選取 **.NET Core**分類，然後選取 **.NET 標準程式庫**:

    ![建立.NET Standard 程式庫](net-standard-images/vsm01-m157.png "建立.NET Standard 程式庫")

2. 在下一個畫面上，選擇 目標 framework- **.NET Standard 2.0**建議：

    [![選擇.NET Standard 2.0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. 最後一個畫面中，輸入專案名稱，然後按一下**建立**。

4. .NET 標準程式庫專案會出現在 [方案總管] 中所示。 相依性節點會指出程式庫會使用[NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/)。

    ![在方案中的相依性節點表示.NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>編輯.NET 標準程式庫設定

可以檢視和變更專案上按一下滑鼠右鍵並選取.NET 標準程式庫設定`Options`這個螢幕擷取畫面所示：

![編輯.NET Standard 專案選項中的目標 framework](net-standard-images/vsm03-m157.png "編輯.NET Standard 目標 Framework 專案選項中的版本")

您可以變更您的版本的內部`netstandard`藉由變更`Target Framework`下拉式清單中的值。

**此外：** 您可以編輯`.csproj`直接來變更此值。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

本節逐步解說如何建立及使用.NET 的標準程式庫，使用 Visual Studio。

### <a name="creating-a-net-standard-library"></a>建立.NET Standard 程式庫

將.NET Standard 程式庫新增至您的解決方案是相當直接。

1. 在 **新的專案**對話方塊中，選取 **.NET Standard**分類，然後選取**類別庫 (.NET Standard)**。

    ![建立新的.NET Standard 類別庫](net-standard-images/vs01-w157.png "建立新的.NET Standard 類別庫")

2. .NET 標準程式庫專案會出現在 [方案總管] 中所示。 相依性節點會指出程式庫會使用[NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/)。

    ![在專案資料夾中的 NETStandard.Library](net-standard-images/vs02-w157.png "方案中的.NET Standard 專案")

### <a name="editing-net-standard-library-settings"></a>編輯.NET Standard 程式庫設定

可以檢視和變更專案上按一下滑鼠右鍵並選取.NET 標準程式庫設定**屬性**這個螢幕擷取畫面所示：

![編輯專案屬性中的.NET standard 的目標架構](net-standard-images/vs03-w157.png "方式與其他專案參考.NET Standard 程式庫")

**此外：** 您可以編輯`.csproj`直接以編輯`TargetFramework`項目，而且是哪個版本的變更 （例如目標。 `<TargetFramework>netstandard2.0</TargetFramework>`)。

### <a name="using-a-net-standard-library-project"></a>使用.NET Standard 程式庫專案

一旦建立.NET Standard 程式庫，您可以新增任何相容的應用程式庫專案的參考，您通常會將參考的方式相同。 在 Visual Studio 中，以滑鼠右鍵按一下 參考 節點，然後選擇 **加入參考...** 然後切換到**專案 > 方案**索引標籤所示：

![參考.NET Standard 程式庫](net-standard-images/vs04.png "在 Visual Studio 中，以滑鼠右鍵按一下 [參考] 節點並選擇 [加入參考...然後切換至方案的專案] 索引標籤所示")

-----

## <a name="related-links"></a>相關連結

* [.NET standard](https://docs.microsoft.com/dotnet/standard/net-standard) -詳細資訊和 PCL 的比較。
