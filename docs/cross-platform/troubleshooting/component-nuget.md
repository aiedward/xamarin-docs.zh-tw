---
title: 將元件參考更新為 NuGet
description: 本檔說明如何使用 NuGet 套件來取代您的元件參考，以便在您的應用程式中進行未來的驗證，因為 Xamarin 元件存放區已停止。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: 08713afa4e781f0712ffccbbcf80533dda8e4bfc
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457220"
---
# <a name="updating-component-references-to-nuget"></a>將元件參考更新為 NuGet

> [!IMPORTANT]
> 從2018年5月15日起，元件存放區已停用 (此關閉最初是在 2017) 中 [宣佈](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/) 。
>
> Visual Studio 中不再支援 Xamarin 元件，並應由 NuGet 套件取代。 請遵循下列指示，手動移除專案中的元件參考。

請參閱這些指示，以在 [Windows](/nuget/quickstart/use-a-package) 或 [Mac](/visualstudio/mac/nuget-walkthrough)上新增 NuGet 套件。

常用 Xamarin [外掛程式和程式庫](https://github.com/xamarin/XamarinComponents/blob/master/README.md) 的清單可協助您尋找無法作為 NuGet 套件的元件替代專案。

## <a name="manually-removing-component-references"></a>手動移除元件參考

15.6 版的 Visual Studio 和7.4 版的 Visual Studio for Mac 不再支援專案中的元件。

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

如果您將專案載入 Visual Studio 中，會顯示下列對話方塊，說明您必須手動移除專案中的所有元件：

![警示對話方塊，說明專案中已找到元件且必須移除](component-nuget-images/component-alert-vs.png)

若要從專案中移除元件：

1. 開啟 .csproj  檔案。 若要這樣做，請以滑鼠右鍵按一下專案名稱，然後選取 **[卸載專案**]。

2. 再以滑鼠右鍵按一下卸載的專案，然後選取 [ **編輯 {您的專案名稱} .csproj**]。

3. 尋找檔案中的任何參考 `XamarinComponentReference` 。 它看起來應該類似下列範例：

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

4. 移除的參考 `XamarinComponentReference` 並儲存檔案。 在上述範例中，您可以放心地移除整個 `ItemGroup` 。

5. 儲存檔案之後，以滑鼠右鍵按一下專案名稱，然後選取 [ **重載專案**]。

6. 針對方案中的每個專案重複上述步驟。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/macos)

如果您將專案載入 Visual Studio for Mac 中，會顯示下列對話方塊，說明您必須手動移除專案中的所有元件：

![警示對話方塊，說明專案中已找到元件且必須移除](component-nuget-images/component-alert.png)

若要從專案中移除元件：

1. 開啟 .csproj  檔案。 若要這樣做，請以滑鼠右鍵按一下專案名稱，然後選取 [ **工具] > 編輯**檔案]。

2. 尋找檔案中的任何參考 `XamarinComponentReference` 。 它看起來應該類似下列範例：

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

3. 移除的參考 `XamarinComponentReference` 並儲存檔案。 在上述範例中，您可以放心地移除整個 `ItemGroup`

4. 針對方案中的每個專案重複上述步驟。

-----

> [!WARNING]
> 下列指示僅適用于舊版 Visual Studio。
> Visual Studio 2017 或 Visual Studio for Mac 的最新版本中已不再提供 [ **元件** ] 節點。

下列各節說明如何更新現有的 Xamarin 解決方案，以變更 NuGet 套件的元件參考。

- [包含 NuGet 套件的元件](#contain)
- [具有 NuGet 取代元件的元件](#replace)

大部分的元件都屬於上述其中一種類別。
如果您使用的元件沒有對等的 NuGet 套件，請閱讀下面的元件，而不使用 [nuget 的 [遷移路徑](#require-update) ] 區段。

<a name="contain"></a>

## <a name="components-that-contain-nuget-packages"></a>包含 NuGet 套件的元件

許多元件已經包含 NuGet 套件，而遷移路徑只是刪除元件參考。

您可以按兩下解決方案中的元件，判斷元件是否已包含 NuGet 套件：

![展開的元件節點](component-nuget-images/solution-sml.png)

[ **套件** ] 索引標籤會列出元件中包含的任何 NuGet 套件：

![套件索引標籤包含 NuGet](component-nuget-images/packages-tab-sml.png)

請注意，[ **元件** ] 索引標籤將會是空的：

![[元件] 索引標籤是空的](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>更新解決方案

若要更新您的解決方案，請從方案中刪除 **元件** 專案：

![刪除元件](component-nuget-images/delete-component-sml.png)

NuGet 套件將會保持列在 [ **套件** ] 節點中，而您的應用程式將會正常編譯及執行。 未來，將會透過 **NuGet** 更新功能來執行此套件的更新：

![更新 NuGet 套件](component-nuget-images/nuget-update-sml.png)

<a name="replace"></a>

## <a name="components-with-nuget-replacements"></a>具有 NuGet 取代元件的元件

如果 [元件 **資訊] 頁面元件索引** 標籤包含如下所示的專案，您將需要手動尋找對等的 NuGet 套件。

![包含元件](component-nuget-images/assemblies-tab-sml.png)

請注意，[ **封裝** ] 索引標籤可能會是空的：

![套件索引標籤](component-nuget-images/packages-tab-empty-sml.png)

_它可能包含 NuGet 相依性，但可以忽略這些相依性。_

若要確認取代 NuGet 封裝存在，請使用元件名稱或作者來搜尋 [NuGet.org](https://www.nuget.org/packages)。

例如，您可以藉由搜尋下列各項來尋找熱門的 **sqlite-net-pcl** 套件：

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) –產品名稱。
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) –作者的設定檔。

### <a name="updating-the-solution"></a>更新解決方案

確認元件可用於 NuGet 之後，請遵循下列步驟：

#### <a name="delete-the-component"></a>刪除元件

以滑鼠右鍵按一下方案中的元件，然後選擇 [ **移除**]：

![移除元件](component-nuget-images/remove-component-sml.png)

這會刪除元件和任何參考。 這會中斷您的組建，直到您新增對等的 NuGet 套件來取代它。

#### <a name="add-the-nuget-package"></a>新增 NuGet 套件

1. 以滑鼠右鍵按一下 [ **封裝** ] 節點，然後選擇 [ **加入封裝 ...**]。
2. 依名稱或作者搜尋 NuGet 取代：

    ![NuGet 搜尋](component-nuget-images/nuget-search-sml.png)

3. 按下 [ **新增套件**]。

NuGet 套件將會連同任何相依性一起新增至您的專案。
這應該會修正組建。 如果組建持續失敗，請調查每個錯誤，以查看元件和 NuGet 套件之間是否有 API 差異。

<a name="require-update"></a>

## <a name="components-without-a-nuget-migration-path"></a>沒有 NuGet 遷移路徑的元件

如果您不想立即找到應用程式中所使用元件的取代，請不要擔心。 現有的元件在 Visual Studio 15.5 中仍將繼續運作，而 [ **元件** ] 節點會如往常般出現在您的解決方案中。

不過，未來的 Visual Studio 版本將 _不_ 會還原或更新元件。
這表示，如果您在新電腦上開啟方案，將不會下載並安裝該元件;作者將無法提供您更新。 您應規劃：

- 從元件中將元件解壓縮，並直接在您的專案中參考它們。
- 洽詢元件作者，並詢問要遷移至 NuGet 的計畫。
- 調查替代的 NuGet 套件，或在元件為開放原始碼時搜尋原始程式碼。

許多元件廠商仍在遷移至 NuGet，其他 (包含商用產品) 可能正在研究替代的交付選項。

## <a name="related-links"></a>相關連結

- [常用 Xamarin 外掛程式和程式庫的清單](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [ (Windows) 安裝和使用 NuGet 套件 ](/nuget/quickstart/use-a-package)
- [ (Mac) 包含 NuGet 套件 ](/visualstudio/mac/nuget-walkthrough)