---
title: 建立 MonoGame UWP 專案
description: MonoGame 可用來建立通用 Windows 平台、 目標設為 多個裝置的其中一個程式碼基底和一組內容的遊戲和應用程式。
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 9f39580d282defed354f3b9e5cbe4eb1cdec4796
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61161105"
---
# <a name="creating-a-monogame-uwp-project"></a>建立 MonoGame UWP 專案

_MonoGame 可用來建立通用 Windows 平台、 目標設為 多個裝置的其中一個程式碼基底和一組內容的遊戲和應用程式。_

本逐步解說涵蓋 MonoGame 通用 Windows 平台 (UWP) 專案建立及載入的內容。 UWP 應用程式可以執行所有的 Windows 10 裝置，包括桌面、 平板電腦、 Windows 手機及 Xbox One 上。

此逐步解說會建立空的專案會顯示*矢菊花藍*背景 （XNA 應用程式的傳統的背景色彩）。

## <a name="requirements"></a>需求

開發 MonoGame UWP 應用程式需要：

- Windows 10 作業系統
- 任何版本的 Visual Studio 2017
- Windows 10 開發人員工具
- 設定裝置以開發人員模式
- [適用於 Visual Studio MonoGame 3.7.1](http://community.monogame.net/t/monogame-3-7-1-release/11173)或更新版本

如需詳細資訊，請參閱此[頁面上設定 Windows 10 UWP 開發](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)。

Xbox One 遊戲可以在零售 Xbox One 的硬體上進行開發。 在開發電腦和 Xbox One 上需要額外的軟體。 如需設定 Xbox One 遊戲程式開發的資訊，請參閱此頁面[設定 Xbox One](https://msdn.microsoft.com/windows/uwp/xbox-apps/index)。

## <a name="creating-an-empty-template"></a>建立空白的範本

一旦已安裝所有必要的資源，並已啟用 Windows 10 電腦上的開發人員模式，我們可以建立新的 MonoGame 專案使用 Visual Studio，依照下列步驟：

1. 選取 **檔案** > **新** > **專案...**
1. 選取 **安裝** > **範本** > **Visual C#**   >  **MonoGame**類別：

    ![](uwp-images/image1.png "MonoGame 類別")

1. 選取  **MonoGame Windows 10 的通用專案**選項：

    ![](uwp-images/image2.png "選取 MonoGame Windows 10 的通用專案選項")

1. 輸入新專案的名稱，然後按一下**確定**。
如果 Visual Studio 會顯示任何錯誤，按一下 確定 之後，請確認已安裝 Windows 10 工具和裝置處於開發人員模式。

當 Visual Studio 完成建立範本時，我們可以執行它以查看執行中之空專案：

![](uwp-images/image3.png "當 Visual Studio 完成建立範本時，則執行它以查看執行中之空專案")

邊角中的數字提供診斷資訊。 要移除此資訊，請刪除中的程式碼`App.xaml.cs`中`DEBUG`中的區塊`OnLaunched`方法：


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

UWP 專案可以在相同的專案部署到任何 Windows 10 裝置。 設定 Windows 10 開發電腦和 Xbox One 之後，您可以藉由切換到遠端電腦的目標，並輸入 Xbox One 的 IP 位址部署 UWP 應用程式：

![](uwp-images/remote.png "UWP 應用程式可以部署到遠端電腦切換的目標，然後輸入 Xbox 的 IP 位址")

Xbox One 上白色框線會表示電視的非安全區域。 如需詳細資訊，請參閱 <<c0> [ 安全區域區段](#safe-area-on-xbox-one)。

![](uwp-images/safearea.png "Xbox One 上白色框線會表示非安全區域的電視")

### <a name="safe-area-on-xbox-one"></a>在 Xbox One 上的安全區域

開發遊戲主控台時，需要考慮安全區域中，也就是應該包含所有重要的視覺效果 （例如 UI 或抬頭顯示器） 的畫面中央區域。 不保證會顯示在所有的電視上，因此放置在此區域中的視覺效果可能會部分或完全隱藏某些顯示在安全區域以外的區域。

Xbox One MonoGame 範本會將視為安全的區域，並轉譯成白色框線。 這個區域也會反映在該遊戲的執行階段`Window.ClientBounds`監看式視窗，在 Visual Studio 中的這個映像所示的屬性。 請注意，用戶端邊界的高度 1016，儘管 1920 x 1080 的顯示器解析度：

![](uwp-images/clientbounds.png "請注意，用戶端邊界的高度 1016，儘管 1920 x 1080 的顯示器解析度")

## <a name="referencing-content-in-uwp-projects"></a>在 UWP 專案中的參考內容

可以參照 MonoGame 專案中的內容，直接從檔案或透過[MonoGame Content Pipeline](~/graphics-games/cocossharp/content-pipeline/index.md)。 小型遊戲專案可能會受益於從檔案載入的簡單性。 較大型的專案將受益於使用內容管線以最佳化以減少大小並載入時間的內容。 不同於在 Xbox 360 的 XNA`System.IO.File`類別可用於 Xbox One UWP 應用程式。

如需有關載入使用內容管線內容的詳細資訊，請參閱[內容管線指南](~/graphics-games/cocossharp/content-pipeline/index.md)。

### <a name="loading-content-from-file"></a>從檔案載入內容

不同於 iOS 和 Android，UWP 專案可以參考相對於可執行檔的檔案。 簡單的遊戲可以使用此技巧負載內容而不需要修改並建置內容的管線專案。

若要載入`Texture2D`從檔案：

1. 在 UWP 專案的 [Content] 資料夾中新增.png 檔案。 將內容加入 [Content] 資料夾是 XNA 和 MonoGame 的慣例。
1. 以滑鼠右鍵按一下新加入的 PNG，並選取 [屬性]。
1. 變更**複製到輸出目錄**要**Copy if Newer**。
1. 將下列程式碼新增至您的遊戲 Initialize 方法來載入`Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

如需有關使用`Texture2D`，請參閱 < [MonoGame 指南簡介](~/graphics-games/monogame/introduction/index.md)。

## <a name="summary"></a>總結

本指南涵蓋如何載入檔案時，建立新的 UWP 專案和 UWP 特有的考量。 想要建立完整的 UWP 遊戲開發人員可以深入了解有關在 MonoGame [MonoGame 指南簡介](~/graphics-games/monogame/introduction/index.md)。
