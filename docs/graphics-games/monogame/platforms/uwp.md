---
title: 建立 MonoGame UWP 專案
description: MonoGame 可用於建立通用 Windows 平臺的遊戲和應用程式，並以一個程式碼基底和一組內容做為多個裝置的目標。
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: aa43513154499a39c27f5ad35fce9584ce7827f8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/06/2019
ms.locfileid: "70763535"
---
# <a name="creating-a-monogame-uwp-project"></a>建立 MonoGame UWP 專案

_MonoGame 可用於建立通用 Windows 平臺的遊戲和應用程式，並以一個程式碼基底和一組內容做為多個裝置的目標。_

本逐步解說涵蓋 MonoGame 通用 Windows 平臺（UWP）專案的建立和內容載入。 UWP 應用程式可在所有 Windows 10 裝置上執行，包括桌面、平板電腦、Windows phone 和 Xbox one。

此逐步解說會建立空的專案，以顯示*矢菊花藍色*背景（應用程式的傳統背景色彩）。

## <a name="requirements"></a>需求

開發 MonoGame UWP 應用程式需要：

- Windows 10 作業系統
- 任何版本的 Visual Studio 2017
- Windows 10 開發人員工具
- 將裝置設定為開發人員模式
- [適用于 Visual Studio 或更新版本的 MonoGame 3.7.1](http://community.monogame.net/t/monogame-3-7-1-release/11173)

如需詳細資訊，請參閱[設定 Windows 10 UWP 開發的此頁面](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)。

Xbox one 遊戲可以在零售 Xbox one 硬體上開發。 開發電腦和 Xbox One 上都需要額外的軟體。 如需設定 Xbox one 以進行遊戲開發的相關資訊，請參閱此頁面上的[設置 xbox](https://msdn.microsoft.com/windows/uwp/xbox-apps/index)one。

## <a name="creating-an-empty-template"></a>建立空白範本

在 Windows 10 電腦上安裝所有必要的資源並啟用開發人員模式後，我們可以依照下列步驟，使用 Visual Studio 建立新的 MonoGame 專案：

1.  > 選取 [檔案] [新增] [專案 ...] > 
1. 選取 [**已安裝** > 的**範本** > ]  > **視覺效果C#**  **MonoGame**類別：

    ![](uwp-images/image1.png "MonoGame 類別")

1. 選取 [ **MonoGame Windows 10 通用專案**] 選項：

    ![](uwp-images/image2.png "選取 [MonoGame Windows 10 通用專案] 選項")

1. 輸入新專案的名稱，然後按一下 **[確定]** 。
如果 Visual Studio 在按一下 [確定] 之後顯示任何錯誤，請確認已安裝 Windows 10 工具，且裝置處於開發人員模式。

一旦 Visual Studio 完成建立範本之後，我們就可以執行它來查看執行中的空白專案：

![](uwp-images/image3.png "一旦 Visual Studio 完成建立範本之後，請執行它來查看空的專案正在執行")

角落中的數位會提供診斷資訊。 刪除`App.xaml.cs` 方法`OnLaunched`中`DEBUG`區塊內的程式碼，即可移除這項資訊：

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
#if DEBUG
    if (System.Diagnostics.Debugger.IsAttached)
    {
        this.DebugSettings.EnableFrameRateCounter = true;
    }
#endif
    ...
```

## <a name="running-on-xbox-one"></a>在 Xbox One 上執行

UWP 專案可以從相同的專案部署到任何 Windows 10 裝置。 設定 Windows 10 開發電腦和 Xbox One 之後，您可以將目標切換到遠端電腦並輸入 Xbox One 的 IP 位址，藉以部署 UWP 應用程式：

![](uwp-images/remote.png "UWP 應用程式可以藉由將目標切換到遠端電腦並輸入 Xbox One IP 位址來進行部署")

在 Xbox One 上，白色框線代表電視的非安全區域。 如需詳細資訊，請參閱[安全區域一節](#safe-area-on-xbox-one)。

![](uwp-images/safearea.png "在 Xbox One 上，白色框線代表電視的非安全區域")

### <a name="safe-area-on-xbox-one"></a>Xbox One 上的安全區域

開發主控台遊戲需要考慮安全區域，這是畫面中央的區域，其中應包含所有重要的視覺效果（例如 UI 或抬頭顯示器）。 不保證在所有電視上都可以看到 [安全] 區域以外的區域，因此，放置在此區域中的視覺效果可能會在某些顯示器上部分或完全隱藏。

適用于 Xbox One 的 MonoGame 範本會考慮安全區域，並將其呈現為白色框線。 這個區域也會反映在遊戲`Window.ClientBounds`屬性中的執行時間，如 Visual Studio 中 [監看式] 視窗的這個影像中所示。 請注意，用戶端界限的高度為1016，儘管1920x1080 顯示解析度：

![](uwp-images/clientbounds.png "請注意，用戶端界限的高度是1016，儘管1920x1080 顯示解析度")

## <a name="referencing-content-in-uwp-projects"></a>參考 UWP 專案中的內容

MonoGame 專案中的內容可以直接從檔案或透過[MonoGame 內容管線](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)參考。 小型遊戲專案可能會因為從檔案載入的簡易性而獲益。 較大的專案將受益于使用內容管線將內容優化，以減少大小和載入時間。 不同于 xbox 360 上的「操作`System.IO.File`類型」，此類別可在 xbox one UWP 應用程式中使用。

如需有關使用「內容管線」載入內容的詳細資訊，請參閱[內容管線指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)。

### <a name="loading-content-from-file"></a>正在從檔案載入內容

不同于 iOS 和 Android，UWP 專案可以參考與可執行檔相關的檔案。 簡單遊戲可以使用這項技術來載入內容，而不需要修改和建立內容管線專案。

若要`Texture2D`從檔案載入：

1. 將 .png 檔案新增至 UWP 專案中的 Content 資料夾。 將內容新增至 [內容] 資料夾是 [MonoGame] 中的慣例。
1. 以滑鼠右鍵按一下新加入的 PNG，然後選取 [屬性]。
1. 將 [**複製到輸出目錄**] 變更為 [**更新時複製**]。
1. 將下列程式碼新增至遊戲的 Initialize 方法，以載入`Texture2D`：

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

如需有關使用的`Texture2D`詳細資訊，請參閱[簡介 MonoGame 指南](~/graphics-games/monogame/introduction/index.md)。

## <a name="summary"></a>總結

本指南涵蓋如何在載入檔案時，建立新的 UWP 專案和 UWP 特有的考慮。 有興趣建立完整 UWP 遊戲的開發人員，可以在[MonoGame 簡介指南](~/graphics-games/monogame/introduction/index.md)中閱讀更多有關 MonoGame 的資訊。
