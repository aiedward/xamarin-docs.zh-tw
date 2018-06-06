---
title: 更新至 NuGet 元件參考
description: 本文件說明如何以取代您元件的參考至未來的使用期限的 NuGet 封裝您的應用程式，因為已停用 Xamarin 元件存放區。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: 34f2f5149be63af6abe5aaab197472d067122069
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782030"
---
# <a name="updating-component-references-to-nuget"></a>更新至 NuGet 元件參考

> [!IMPORTANT]
> 從 2018 月 15，開始，已停用元件存放區 (此終止原本[宣布](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/)在 2017 年 11 月)。
>
> Xamarin 元件不再支援在 Visual Studio 中，並應取代為 NuGet 封裝。 請遵循下列指示手動從專案中移除元件參考。

新增 NuGet 封裝上的這些指示，請參閱[Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package)或[Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。

一份熱門 Xamarin[增益集和文件庫](https://github.com/xamarin/XamarinComponents/blob/master/README.md)可用以協助找出替代項目，也就是無法使用 NuGet 封裝時元件。

## <a name="manually-removing-component-references"></a>手動移除元件參考

15.6 版本的 Visual Studio 和 7.4 版的 Visual Studio for Mac 不再支援元件，您的專案中。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

如果您將專案載入 Visual Studio 時，會顯示下列對話方塊，以說明，您必須移除任何元件從您的專案以手動方式：

![警示對話方塊說明元件專案中找到，且必須移除](component-nuget-images/component-alert-vs.png)

若要從您的專案中移除元件：

1. 開啟 **.csproj**檔案。 若要這樣做，以滑鼠右鍵按一下專案名稱，然後選取**卸載專案**。 

2. 卸載的專案上按一下滑鼠右鍵，然後選取**編輯 {您的專案名稱}.csproj**。

3. 尋找檔案中的任何參考`XamarinComponentReference`。 它看起來應該類似下列的範例：

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

4. 移除參考`XamarinComponentReference`並儲存檔案。 在上述範例中，可以放心移除整個`ItemGroup`。

5. 已儲存檔案，以滑鼠右鍵按一下專案名稱，並選取**重新載入專案**。

6. 您的方案中每個專案重複上述步驟。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

如果適用於 Mac 的專案載入 Visual Studio 中，會顯示下列對話方塊，以說明，您必須移除任何元件從您的專案以手動方式：

![警示對話方塊說明元件專案中找到，且必須移除](component-nuget-images/component-alert.png)

若要從您的專案中移除元件：

1. 開啟.csproj 檔。 若要這樣做，以滑鼠右鍵按一下專案名稱，然後選取**工具 > 編輯檔案**。

2. 尋找檔案中的任何參考`XamarinComponentReference`。 它看起來應該類似下列的範例：

    ```xml
    <ItemGroup>
      <XamarinComponentReference Include="advancedcolorpicker">
        <Version>2.0.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="gunmetaltheme">
        <Version>1.4.1</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
      <XamarinComponentReference Include="signature-pad">
        <Version>2.2.0</Version>
        <Visible>False</Visible>
      </XamarinComponentReference>
    </ItemGroup>
    ```

3. 移除參考`XamarinComponentReference`並儲存檔案。 在上述範例中，可以放心移除整個 `ItemGroup`

4. 您的方案中每個專案重複上述步驟。

-----

> [!WARNING]
> 下列指示只適用於舊版的 Visual Studio。
> **元件**節點已無法再使用新版本的 Visual Studio 2017 或 Visual Studio for mac。

下列各節說明如何更新現有的 Xamarin 解決方案元件將參考變更到 NuGet 封裝。

- [元件，其中包含 NuGet 封裝](#contain)
- [含有 NuGet 取代項目的元件](#replace)

大部分元件歸類到其中一個以上的類別目錄。
如果您使用的元件，並不具有對等的 NuGet 封裝，請閱讀[元件不包含 NuGet 移轉路徑](#require-update)下一節。

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>元件，其中包含 NuGet 封裝

許多元件已經包含 NuGet 封裝，並移轉路徑只是要刪除的元件參考。

您可以判斷元件是否已包含在方案中的元件上按兩下 NuGet 封裝：

![展開的 [元件] 節點](component-nuget-images/solution-sml.png)

**封裝** 索引標籤會列出任何包含元件中的 NuGet 封裝：

![封裝的索引標籤包含 NuGet](component-nuget-images/packages-tab-sml.png)

請注意，**組件** 索引標籤將會是空白：

![是空的組件 索引標籤](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>更新方案

若要更新您的方案，請刪除**元件**從方案項目：

![刪除元件](component-nuget-images/delete-component-sml.png)

NuGet 封裝仍會列在**封裝**節點和應用程式會將編譯並如往常般執行。 在未來，對此套件的更新會透過執行**Nuget**更新功能：

![更新 NuGet 套件](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>含有 NuGet 取代項目的元件

如果元件資訊頁面**組件** 索引標籤包含的項目，如下所示，您必須以手動方式尋找對等的 NuGet 封裝。

![包含組件](component-nuget-images/assemblies-tab-sml.png)

請注意，**封裝** 索引標籤可能是空白：

![](component-nuget-images/packages-tab-empty-sml.png)

_它可能包含 NuGet 相依性，但是可加以忽略。_

若要確認更換 NuGet 封裝存在，搜尋上[NuGet.org](https://www.nuget.org/packages)，使用該元件名稱，或者依作者。

例如，您可以找到熱門**sqlite net pcl**藉由搜尋套件：

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) – 產品名稱。
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) – 作者設定檔。

### <a name="updating-the-solution"></a>更新方案

一旦您確認元件可用於 NuGet，請遵循下列步驟：

#### <a name="delete-the-component"></a>刪除元件

在方案中的元件上按一下滑鼠右鍵，然後選擇 **移除**:

![移除元件](component-nuget-images/remove-component-sml.png)

這將刪除的元件，任何參考。 這會中斷您的組建，直到您將對等 NuGet 封裝來取代它。

#### <a name="add-the-nuget-package"></a>新增 NuGet 封裝

1. 以滑鼠右鍵按一下**封裝**節點，然後選擇 **新增套件...**.
2. 搜尋 NuGet 取代依名稱或作者：

  ![](component-nuget-images/nuget-search-sml.png)

3. 按**新增封裝**。

NuGet 封裝將會加入至專案，以及任何相依性。
如此應可修正的組建。 如果建置繼續失敗，請調查每個錯誤，以查看發生元件和 NuGet 套件的 API 差異。

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>元件不包含 NuGet 移轉路徑

不會考慮若未立即找到您的應用程式中使用元件的取代項目。 現有的元件將會繼續在 Visual Studio 15.5，而**元件**節點會如往常般出現在您的方案。

未來的 Visual Studio 版本，不過，將_不_還原或更新元件。
這表示如果您開啟新的電腦上的方案，此元件將不下載和安裝;與作者不能更新為您提供。 您應該計劃：

* 從元件擷取組件，並直接在您的專案中參考它們。
* 請連絡元件作者，並詢問有關計劃移轉至 NuGet。
* 調查替代的 NuGet 封裝，或搜尋原始碼，如果元件是開放原始碼。

仍在使用移轉至 NuGet，許多元件廠商和其他人 （包括年年底上市的產品） 在調查替代的傳遞選項。


## <a name="related-links"></a>相關連結
- [熱門 Xamarin 增益集和文件庫的清單](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [安裝和使用 NuGet 封裝 (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [包括 NuGet 封裝 (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
