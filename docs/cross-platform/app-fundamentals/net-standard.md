---
title: 使用 .NET Standard 程式庫來共用程式碼
description: 本檔說明如何使用 .NET Standard 程式庫來共用程式碼。 它討論如何建立 .NET Standard 程式庫、編輯其設定，並在應用程式中使用它。
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 07/18/2018
ms.openlocfilehash: cae59053374f673a56d02e86cd59fb85f313c41b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016805"
---
# <a name="net-standard-library-code-sharing"></a>.NET Standard 程式庫程式碼共用

.NET Standard 程式庫具有適用于所有 .NET 平臺的統一 API，包括 Xamarin 和 .NET Core。 建立單一 .NET Standard 程式庫，並從支援 .NET Standard 平臺的任何執行時間使用它。 如需所支援平臺的詳細資訊，請參閱[此圖表](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support)。

雖然 .NET Standard 版本1.0 到1.6 提供累加的 .NET Framework 子集，.NET Standard 2.0 提供 Xamarin 應用程式的最佳支援層級，以及移植現有的可移植類別庫。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

本節逐步解說如何使用 Visual Studio for Mac 建立和使用 .NET Standard 程式庫。

### <a name="creating-a-net-standard-library"></a>建立 .NET Standard 程式庫

您可以使用下列步驟，將 .NET Standard 程式庫新增至您的解決方案：

1. 在 [**新增專案**] 對話方塊中，選取 [ **.net Core** ] 類別，然後選取 [ **.NET Standard 程式庫**]：

    ![建立 .NET Standard 程式庫](net-standard-images/vsm01-m157.png "建立新的 .NET Standard 程式庫")

2. 在下一個畫面上，選擇 [目標 framework-建議使用 **.NET Standard 2.0** ]：

    [![選擇 .NET Standard 2。0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. 在最後一個畫面上，輸入專案名稱，然後按一下 [**建立**]。

4. [.NET Standard 程式庫] 專案隨即出現，如方案總管所示。 [相依性] 節點會指出程式庫使用的是[NETStandard](https://www.nuget.org/packages/NETStandard.Library/)。

    ![解決方案中的 [相依性] 節點表示 .NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>編輯 .NET Standard 程式庫設定

以滑鼠右鍵按一下專案並選取 [`Options`]，即可查看和變更 .NET Standard 程式庫設定，如下列螢幕擷取畫面所示：

![在專案選項中編輯 .NET Standard 目標 framework](net-standard-images/vsm03-m157.png "在 [專案選項] 中編輯 .NET Standard 目標 Framework 的版本")

在中，您可以藉由變更 `Target Framework` 下拉式值來變更您的 `netstandard` 版本。

**此外：** 您可以直接編輯 `.csproj` 來變更此值。

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 （Windows）

本節逐步解說如何使用 Visual Studio 建立和使用 .NET Standard 程式庫。

### <a name="creating-a-net-standard-library"></a>建立 .NET Standard 程式庫

將 .NET Standard 程式庫新增至您的方案相當簡單。

1. 在 [**新增專案**] 對話方塊中，選取 [ **.NET Standard** ] 類別目錄，然後選取 [**類別庫（.NET Standard）** ]。

    ![建立新的 .NET Standard 類別庫](net-standard-images/vs01-w157.png "建立新的 .NET Standard 類別庫")

2. [.NET Standard 程式庫] 專案隨即出現，如方案總管所示。 [相依性] 節點會指出程式庫使用的是[NETStandard](https://www.nuget.org/packages/NETStandard.Library/)。

    ![專案資料夾中的 NETStandard](net-standard-images/vs02-w157.png "方案中的 .NET Standard 專案")

### <a name="editing-net-standard-library-settings"></a>編輯 .NET Standard 程式庫設定

以滑鼠右鍵按一下專案，然後選取 [**屬性**]，如下列螢幕擷取畫面所示，可以查看和變更 .NET Standard 程式庫設定：

![編輯專案屬性中的 .NET standard 目標 framework](net-standard-images/vs03-w157.png "參考 .NET Standard 程式庫的方式與其他專案相同")

**此外：** 您可以直接編輯 `.csproj` 以編輯 `TargetFramework` 元素，並變更目標版本（例如 `<TargetFramework>netstandard2.0</TargetFramework>`)。

### <a name="using-a-net-standard-library-project"></a>使用 .NET Standard 程式庫專案

一旦建立 .NET Standard 程式庫之後，您就可以從任何相容的應用程式或程式庫專案，以通常加入參考的方式來新增其參考。 在 Visual Studio 中，以滑鼠右鍵按一下 [參考] 節點，然後選擇 [**加入參考 ...** ]，然後切換至 [**專案 > 方案**] 索引標籤，如下所示：

![參考 .NET Standard 程式庫](net-standard-images/vs04.png "在 Visual Studio 中，以滑鼠右鍵按一下 [參考] 節點，然後選擇 [加入參考 ...]。然後切換至 [方案專案] 索引標籤，如下所示")

-----

## <a name="net-standard-and-xamarinforms-for-the-net-developer-video"></a>適用于 .NET 開發人員的 .NET Standard 和 Xamarin （影片）

> [!Video https://channel9.msdn.com/Shows/XamarinShow/NET-Standard-and-XamarinForms-for-the-NET-Developer/player]

## <a name="related-links"></a>相關連結

* [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) -與 PCL 的詳細資訊和比較。
