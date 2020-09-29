---
title: 使用 .NET Standard 程式庫來共用程式碼
description: 本檔說明如何使用 .NET Standard 程式庫來共用程式碼。 它會討論如何建立 .NET Standard 程式庫、編輯其設定，以及在應用程式中使用它。
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 07/18/2018
ms.openlocfilehash: 6c2f0ef1d34b60e712dd713d4fff3fe5f9856926
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91458029"
---
# <a name="net-standard-library-code-sharing"></a>.NET Standard 程式庫程式碼共用

.NET Standard 程式庫都具有適用于所有 .NET 平臺（包括 Xamarin 和 .NET Core）的統一 API。 建立單一 .NET Standard 程式庫，並從任何支援 .NET Standard 平臺的執行時間使用它。 如需支援平臺的詳細資料，請參閱 [此圖表](/dotnet/standard/net-standard#net-implementation-support) 。

雖然 .NET Standard 1.0 至1.6 版提供累加的 .NET Framework 子集，.NET Standard 2.0 提供 Xamarin 應用程式的最佳支援層級，以及移植現有的便攜類別庫。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio for Mac

本節將逐步解說如何使用 Visual Studio for Mac 建立和使用 .NET Standard 程式庫。

### <a name="creating-a-net-standard-library"></a>建立 .NET Standard 程式庫

您可以使用下列步驟，將 .NET Standard 程式庫新增至您的解決方案：

1. 在 [ **加入新專案** ] 對話方塊中，選取 [ **.net Core** ] 類別，然後選取 [ **.NET Standard 程式庫**]：

    ![建立 .NET Standard 程式庫](net-standard-images/vsm01-m157.png "建立新的 .NET Standard 程式庫")

2. 在下一個畫面上，選擇目標 framework-建議使用 **.NET Standard 2.0** ：

    [![選擇 .NET Standard 2。0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. 在最後一個畫面上，輸入專案名稱，然後按一下 [ **建立**]。

4. .NET Standard 程式庫專案會出現，如方案總管所示。 [相依性] 節點會指出程式庫使用 [NETStandard](https://www.nuget.org/packages/NETStandard.Library/)程式庫。

    ![解決方案中的 [相依性] 節點指出 .NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>編輯 .NET Standard 程式庫設定

您可以用滑鼠右鍵按一下專案，然後選取， `Options` 如下列螢幕擷取畫面所示，來查看並變更 .NET Standard 程式庫設定：

![在專案選項中編輯 .NET Standard 目標 framework](net-standard-images/vsm03-m157.png "在專案選項中編輯 .NET Standard 目標 Framework 的版本")

您可以在中變更 `netstandard` 下拉式清單值，以變更您的版本 `Target Framework` 。

**此外：** 您可以直接編輯 `.csproj` 來變更這個值。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows) 

本節將逐步解說如何使用 Visual Studio 建立和使用 .NET Standard 程式庫。

### <a name="creating-a-net-standard-library"></a>建立 .NET Standard 程式庫

將 .NET Standard 程式庫新增至您的解決方案相當簡單。

1. 在 [ **新增專案** ] 對話方塊中，選取 [ **.NET Standard** ] 分類，然後選取 [ **類別庫] ( .NET Standard) **。

    ![建立新的 .NET Standard 類別庫](net-standard-images/vs01-w157.png "建立新的 .NET Standard 類別庫")

2. .NET Standard 程式庫專案會出現，如方案總管所示。 [相依性] 節點會指出程式庫使用 [NETStandard](https://www.nuget.org/packages/NETStandard.Library/)程式庫。

    ![專案資料夾中的 NETStandard 程式庫](net-standard-images/vs02-w157.png "方案中的 .NET Standard 專案")

### <a name="editing-net-standard-library-settings"></a>編輯 .NET Standard 程式庫設定

您可以用滑鼠右鍵按一下專案，然後選取 [ **屬性** ]，如下列螢幕擷取畫面所示，以查看並變更 .NET Standard 程式庫設定：

![在專案屬性中編輯 .NET standard 目標 framework](net-standard-images/vs03-w157.png "以與其他專案相同的方式參考 .NET Standard 程式庫")

**此外：** 您可以直接編輯 `.csproj` 以編輯 `TargetFramework` 元素，並變更目標的版本 (例如。 `<TargetFramework>netstandard2.0</TargetFramework>`).

### <a name="using-a-net-standard-library-project"></a>使用 .NET Standard 程式庫專案

一旦建立 .NET Standard 程式庫之後，您就可以在任何相容的應用程式或程式庫專案中加入該程式庫的參考，就像平常新增參考一樣。 在 Visual Studio 中，以滑鼠右鍵按一下 [參考] 節點，然後選擇 [ **加入參考** ]，然後切換至 [ **專案] > [方案** ] 索引標籤，如下所示：

![參考 .NET Standard 程式庫](net-standard-images/vs04.png "在 Visual Studio 中，以滑鼠右鍵按一下 [參考] 節點，然後選擇 [加入參考]。然後切換至 [方案專案] 索引標籤，如下所示")

-----

## <a name="net-standard-and-xamarinforms-for-the-net-developer-video"></a>適用于 .NET 開發人員的 .NET Standard 和 Xamarin (影片) 

> [!Video https://channel9.msdn.com/Shows/XamarinShow/NET-Standard-and-XamarinForms-for-the-NET-Developer/player]

## <a name="related-links"></a>相關連結

* [.NET Standard](/dotnet/standard/net-standard) 詳細資訊以及 PCL 的比較。