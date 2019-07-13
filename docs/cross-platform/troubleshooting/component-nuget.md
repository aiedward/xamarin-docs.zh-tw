---
title: 元件參考更新為 NuGet
description: 本文件說明如何以取代您元件的參考 NuGet 套件，以未來考驗您的應用程式，因為已停用 Xamarin 元件存放區。
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9E6C986F-3FBA-4599-8367-FB0C565C0ADE
author: asb3993
ms.author: amburns
ms.date: 04/18/2018
ms.openlocfilehash: fbef8f60a31511be926ee2adf5e239ad8955b208
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864529"
---
# <a name="updating-component-references-to-nuget"></a>元件參考更新為 NuGet

> [!IMPORTANT]
> 自 2018 5 月 15 日起，已停用元件存放區 (此終止原本[宣布](https://blog.xamarin.com/hello-nuget-new-home-xamarin-components/)在 2017 年 11 月)。
>
> Xamarin 元件不再支援在 Visual Studio 中，並應取代為 NuGet 套件。 請遵循下列指示來手動移除您的專案中的元件參考。

請參閱這些指示上新增 NuGet 套件[Windows](https://docs.microsoft.com/nuget/quickstart/use-a-package)或是[Mac](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)。

一份常用的 Xamarin[外掛程式和程式庫](https://github.com/xamarin/XamarinComponents/blob/master/README.md)能夠協助找出替代項目，也就是以 NuGet 套件，您無法使用的元件。

## <a name="manually-removing-component-references"></a>以手動方式移除元件的參考

15.6 版的 Visual Studio 和 Visual studio for Mac 7.4 版本不再支援元件，在您的專案。 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

如果您將專案載入 Visual Studio 時，會顯示下列對話方塊，說明，您必須移除任何元件從您的專案以手動方式：

![警示對話方塊說明元件專案中找到，且必須移除](component-nuget-images/component-alert-vs.png)

若要從您的專案中移除的元件：

1. 開啟 **.csproj**檔案。 若要這樣做，請以滑鼠右鍵按一下專案名稱，然後選取**卸載專案**。 

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

4. 移除參考`XamarinComponentReference`並儲存檔案。 在上述範例中，會安全地移除整個`ItemGroup`。

5. 一旦儲存的檔案，以滑鼠右鍵按一下專案名稱，然後選取**重新載入專案**。

6. 您的方案中每個專案重複上述步驟。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

如果您專案載入 Visual Studio for Mac 中，會顯示下列對話方塊，說明，您必須移除任何元件從您的專案以手動方式：

![警示對話方塊說明元件專案中找到，且必須移除](component-nuget-images/component-alert.png)

若要從您的專案中移除的元件：

1. 開啟.csproj 檔案。 若要這樣做，請以滑鼠右鍵按一下專案名稱，然後選取**工具 > 編輯檔案**。

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

3. 移除參考`XamarinComponentReference`並儲存檔案。 在上述範例中，會安全地移除整個 `ItemGroup`

4. 您的方案中每個專案重複上述步驟。

-----

> [!WARNING]
> 下列指示只適用於舊版的 Visual Studio。
> **元件**節點已無法再使用新版本中的 Visual Studio 2017 或 Visual Studio for mac。

下列各節說明如何更新現有的 Xamarin 方案，才能變更 NuGet 套件的元件參考。

- [包含 NuGet 套件的元件](#contain)
- [使用 NuGet 取代的元件](#replace)

大部分元件歸類到其中一個上述的類別。
如果您使用的元件，不會出現的對等的 NuGet 封裝，請閱讀[沒有 NuGet 移轉路徑元件](#require-update)下一節。

<a name="contain" />

## <a name="components-that-contain-nuget-packages"></a>包含 NuGet 套件的元件

許多元件已經包含 NuGet 套件，並移轉路徑只是刪除其元件參考。

您可以判斷元件是否已經包含在解決方案中的元件上按兩下的 NuGet 套件：

![展開的 [元件] 節點](component-nuget-images/solution-sml.png)

**封裝** 索引標籤會列出任何在元件中的 NuGet 套件：

![套件索引標籤包含 NuGet](component-nuget-images/packages-tab-sml.png)

請注意，**組件** 索引標籤將會是空白：

![是空的組件 索引標籤](component-nuget-images/assemblies-tab-empty-sml.png)

### <a name="updating-the-solution"></a>正在更新解決方案

若要更新您的解決方案，請刪除**元件**解決方案中的項目：

![刪除元件](component-nuget-images/delete-component-sml.png)

NuGet 套件仍列於**封裝**節點，您的應用程式會編譯，並如往常般執行。 在未來，此套件的更新會透過執行**Nuget**更新功能：

![更新 NuGet 套件](component-nuget-images/nuget-update-sml.png)


<a name="replace" />

## <a name="components-with-nuget-replacements"></a>使用 NuGet 取代的元件

如果元件資訊頁面**組件** 索引標籤包含的項目，如下所示，您必須以手動方式尋找對等的 NuGet 套件。

![包含組件](component-nuget-images/assemblies-tab-sml.png)

請注意，**封裝** 索引標籤可能是空白的：

![](component-nuget-images/packages-tab-empty-sml.png)

_它可能包含 NuGet 相依性，但可予以忽略。_

若要確認更換 NuGet 封裝存在，搜尋[NuGet.org](https://www.nuget.org/packages)，使用 元件名稱，或者依作者。

例如，您可以尋找熱門**sqlite net pcl**藉由搜尋的封裝：

- [`sqlite-net-pcl`](https://www.nuget.org/packages?q=sqlite-net-pcl) – 產品名稱。
- [`praeclarum`](https://www.nuget.org/packages?q=praeclarum) – 作者設定檔。

### <a name="updating-the-solution"></a>正在更新解決方案

一旦確認元件可在 NuGet 中，請遵循下列步驟：

#### <a name="delete-the-component"></a>刪除元件

在方案中的元件上按一下滑鼠右鍵，然後選擇**移除**:

![移除元件](component-nuget-images/remove-component-sml.png)

這將刪除的元件和任何參考。 這會中斷您的組建，直到您將新增對等的 NuGet 套件，將它取代。

#### <a name="add-the-nuget-package"></a>新增 NuGet 套件

1. 以滑鼠右鍵按一下**封裝**節點，然後選擇 **新增套件...** .
2. 搜尋依名稱或作者 NuGet 取代：

    ![](component-nuget-images/nuget-search-sml.png)

3. 按下**將套件新增**。

NuGet 封裝會新增至您的專案，以及任何相依性。
如此應可修正組建。 如果組建持續失敗，請調查每個錯誤，以查看是否有元件和 NuGet 套件的 API 差異。

<a name="require-update" />

## <a name="components-without-a-nuget-migration-path"></a>元件沒有 NuGet 移轉路徑

不是擔心，如果您不立即發現您的應用程式中使用元件的取代項目。 現有的元件將會繼續以用於 Visual Studio 15.5 中，而**元件**節點會如往常般出現在您的解決方案。

不過，將未來的 Visual Studio 版本_不_還原或更新的元件。
這表示如果您開啟新的電腦上的解決方案時，元件將不會下載並安裝;作者將無法更新為您提供。 您應該規劃先：

* 從元件擷取組件，並直接在您的專案中參考它們。
* 請連絡元件作者，並詢問有關移轉至 NuGet 的計劃。
* 調查替代的 NuGet 套件，或如果元件是開放原始碼搜尋的原始程式碼。

仍在使用移轉至 NuGet，許多元件廠商和其他人 （包括販售的產品） 可能會調查替代的傳遞選項。


## <a name="related-links"></a>相關連結
- [熱門的 Xamarin 外掛程式和程式庫的清單](https://github.com/xamarin/XamarinComponents/blob/master/README.md)
- [安裝並使用 NuGet 套件 (Windows)](https://docs.microsoft.com/nuget/quickstart/use-a-package)
- [包含 NuGet 套件 (Mac)](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)
