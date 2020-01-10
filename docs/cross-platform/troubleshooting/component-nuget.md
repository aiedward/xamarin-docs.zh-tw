---
title: 正在更新 NuGet 的元件參考
description: 本檔說明如何將您的元件參考取代為 NuGet 套件，以供日後證明應用程式，因為 Xamarin 元件存放區已停止。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: davidortinau
ms.author: daortin
ms.date: 04/18/2018
ms.openlocfilehash: 4a5aa13a197e885b074b07eae3594abd4992ee71
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728248"
---
# <a name="updating-component-references-to-nuget"></a>正在更新 NuGet 的元件參考

> [!IMPORTANT]
> 元件存放區已于2018年5月15日停止（此結束原本是在2017年11月）[推出](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/)。
>
> Visual Studio 不再支援 Xamarin 元件，應由 NuGet 套件取代。 請依照下列指示，手動移除專案中的元件參考。

請參閱這些指示，以在[Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package)或[Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)上新增 NuGet 套件。

您可以使用常用的 Xamarin[外掛程式和程式庫](https://github.com/xamarin/XamarinComponents/blob/master/README.md)清單，以協助找出 NuGet 套件無法使用的元件替代專案。

## <a name="manually-removing-component-references"></a>手動移除元件參考

15.6 版的 Visual Studio 和7.4 版的 Visual Studio for Mac 不再支援您專案中的元件。 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

如果您將專案載入 Visual Studio，就會顯示下列對話方塊，說明您必須手動從專案中移除任何元件：

![警示對話方塊，其中會說明在您的專案中已找到元件，必須將其移除](component-nuget-images/component-alert-vs.png)

若要從專案中移除元件：

1. 開啟 .csproj 檔案。 若要這麼做，請在專案名稱上按一下滑鼠右鍵，然後選取 **[卸載專案**]。 

2. 在卸載的專案上再次以滑鼠右鍵按一下，然後選取 [**編輯 {您的專案名稱} .csproj**]。

3. 尋找要 `XamarinComponentReference`之檔案中的任何參考。 看起來應該類似下列範例：

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

4. 移除 `XamarinComponentReference` 的參考，並儲存檔案。 在上述範例中，移除整個 `ItemGroup`是安全的。

5. 儲存檔案之後，以滑鼠右鍵按一下專案名稱，然後選取 [**重載專案**]。

6. 針對方案中的每個專案重複上述步驟。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

如果您將專案載入 Visual Studio for Mac，就會顯示下列對話方塊，說明您必須手動從專案中移除任何元件：

![警示對話方塊，其中會說明在您的專案中已找到元件，必須將其移除](component-nuget-images/component-alert.png)

若要從專案中移除元件：

1. 開啟 .csproj 檔案。 若要這麼做，請在專案名稱上按一下滑鼠右鍵，然後選取 [**工具] > [編輯**檔案]。

2. 尋找要 `XamarinComponentReference`之檔案中的任何參考。 看起來應該類似下列範例：

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

3. 移除 `XamarinComponentReference` 的參考，並儲存檔案。 在上述範例中，您可以放心地移除整個 `ItemGroup`

4. 針對方案中的每個專案重複上述步驟。

-----

> [!WARNING]
> 下列指示僅適用于較舊版本的 Visual Studio。
> 目前版本的 Visual Studio 2017 或 Visual Studio for Mac 已不再提供 [**元件**] 節點。

下列各節說明如何更新現有的 Xamarin 解決方案，以變更 NuGet 套件的元件參考。

- [包含 NuGet 套件的元件](#contain)
- [包含 NuGet 更換的元件](#replace)

大部分的元件都屬於上述類別的其中一個。
如果您使用的元件似乎沒有對等的 NuGet 套件，請閱讀下面的「[沒有 NuGet 遷移路徑的元件](#require-update)」一節。

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>包含 NuGet 套件的元件

許多元件已經包含 NuGet 套件，而遷移路徑只是用來刪除元件參考。

您可以按兩下解決方案中的元件，判斷元件是否已包含 NuGet 套件：

![展開的元件節點](component-nuget-images/solution-sml.png)

[**封裝**] 索引標籤會列出元件中包含的任何 NuGet 套件：

![[套件] 索引標籤包含 NuGet](component-nuget-images/packages-tab-sml.png)

請注意，[**元件**] 索引標籤會是空的：

![[元件] 索引標籤是空的](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>更新方案

若要更新您的解決方案，請從解決方案中刪除**元件**專案：

![刪除元件](component-nuget-images/delete-component-sml.png)

NuGet 套件會繼續列在 [**套件**] 節點中，且您的應用程式會如往常般編譯並執行。 未來，將會透過**NuGet**更新功能來執行此套件的更新：

![更新 NuGet 套件](component-nuget-images/nuget-update-sml.png)

<a name="replace" />

## <a name="components-with-nuget-replacements"></a>包含 NuGet 更換的元件

如果 [元件**資訊] 頁面 [元件]** 索引標籤包含如下所示的專案，您將需要手動尋找對等的 NuGet 套件。

![包含元件](component-nuget-images/assemblies-tab-sml.png)

請注意，[**封裝**] 索引標籤可能會是空的：

![](component-nuget-images/packages-tab-empty-sml.png)

_它可能包含 NuGet 相依性，但可以忽略這些相依性。_

若要確認取代 NuGet 套件存在，請使用元件名稱或作者來搜尋[NuGet.org](https://www.nuget.org/packages)。

例如，您可以藉由搜尋下列內容來尋找熱門的**sqlite 網路-pcl**套件：

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) –產品名稱。
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) –作者的設定檔。

### <a name="updating-the-solution"></a>更新方案

一旦您確認 NuGet 中有該元件，請遵循下列步驟：

#### <a name="delete-the-component"></a>刪除元件

以滑鼠右鍵按一下方案中的元件，然後選擇 [**移除**]：

![移除元件](component-nuget-images/remove-component-sml.png)

這會刪除元件和任何參考。 這會中斷您的組建，直到您新增對等的 NuGet 套件以取代它為止。

#### <a name="add-the-nuget-package"></a>新增 NuGet 套件

1. 以滑鼠右鍵按一下 [**套件**] 節點，然後選擇 [**新增套件**]。
2. 依名稱或作者搜尋 NuGet 取代：

    ![](component-nuget-images/nuget-search-sml.png)

3. 按 [**新增套件**]。

NuGet 套件會連同任何相依性新增至您的專案。
這應該會修正組建。 如果組建持續失敗，請調查每個錯誤，以查看元件和 NuGet 套件之間是否有 API 差異。

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>沒有 NuGet 遷移路徑的元件

如果您不想立即找到應用程式中所用元件的取代，請不要擔心。 現有元件會繼續在 Visual Studio 15.5 中工作，而 [**元件**] 節點會如往常般出現在您的方案中。

不過，未來 Visual Studio 版本將_不_會還原或更新元件。
這表示如果您在新電腦上開啟方案，將不會下載並安裝該元件;作者將無法提供更新。 您應該規劃：

- 從元件解壓縮元件，並直接在您的專案中參考它們。
- 請洽詢元件作者，並詢問要遷移至 NuGet 的計畫。
- 調查替代的 NuGet 套件，或在元件為開放原始碼的情況下搜尋原始程式碼。

許多元件廠商仍在致力於遷移至 NuGet，而其他專案（包括商用產品）可能會調查替代的傳遞選項。

## <a name="related-links"></a>相關連結

- [熱門 Xamarin 外掛程式和程式庫的清單](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [安裝和使用 NuGet 套件（Windows）](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [包含 NuGet 套件（Mac）](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
