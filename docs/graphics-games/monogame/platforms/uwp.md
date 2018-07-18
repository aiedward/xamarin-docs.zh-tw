---
title: 建立 MonoGame UWP 專案
description: MonoGame 可以用來建立通用 Windows 平台、 目標多個裝置的其中一個程式碼基底和一組內容的遊戲和應用程式。
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: f539ede3bb90f216463a55cca30554a5f50e2386
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
ms.locfileid: "33922674"
---
# <a name="creating-a-monogame-uwp-project"></a>建立 MonoGame UWP 專案

_MonoGame 可以用來建立通用 Windows 平台、 目標多個裝置的其中一個程式碼基底和一組內容的遊戲和應用程式。_

這個逐步解說將說明建立 MonoGame 通用 Windows 平台 (UWP) 專案和載入的內容。 UWP 應用程式可以執行所有 Windows 10 裝置，包括桌上型電腦、 平板電腦、 Windows Phone 和 Xbox One。

這個逐步解說會建立空專案會顯示*矢菊花藍*背景 （XNA 應用程式的傳統的背景色彩）。

## <a name="requirements"></a>需求

開發 MonoGame UWP 應用程式需要：

- Windows 10 作業系統
- 任何版本的 Visual Studio 2015
- Windows 10 開發人員工具
- 開發人員模式下設定裝置
- [Visual Studio 的 MonoGame 3.5](http://www.monogame.net/2016/03/17/monogame-3-5/)或更新版本

如需詳細資訊，請參閱此[頁面上設定的 Windows 10 UWP 開發](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)。

Xbox One 的遊戲可以在零售 Xbox One 硬體上進行開發。 開發 PC 和 Xbox One 上需要額外的軟體。 設定 Xbox One 開發遊戲的資訊，請參閱此頁面，在[設定 Xbox One](https://msdn.microsoft.com/windows/uwp/xbox-apps/index)。

## <a name="creating-an-empty-template"></a>建立空白的範本

一旦已安裝所有必要的資源，並在 Windows 10 電腦上已啟用開發人員模式，我們可以建立新的 MonoGame 專案依照下列步驟使用 Visual Studio:

1. 選取**檔案** > **新** > **專案...**
1. 選取**已安裝** > **範本** > **Visual C#** > **MonoGame**類別： 

    ![](uwp-images/image1.png "MonoGame 類別")

1. 選取**MonoGame Windows 10 的通用專案**選項： 

    ![](uwp-images/image2.png "選取 MonoGame Windows 10 的通用專案選項")

1. 輸入新專案的名稱，然後按一下**確定**。
如果 Visual Studio 會顯示任何錯誤，按一下 [確定] 之後，請確認 Windows 10 工具會安裝，而且裝置處於開發人員模式。

Visual Studio 完成建立範本時，我們可以執行以查看執行空專案：

![](uwp-images/image3.png "Visual Studio 一次完成建立範本，並加以執行，請參閱執行空專案")

邊角中的數字提供診斷資訊。 這項資訊可以移除刪除的程式碼中`App.xaml.cs`中`DEBUG`中區塊`OnLaunched`方法：


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

## <a name="running-on-xbox-one"></a>一個 Xbox 上執行

UWP 專案可以在相同專案部署到任何 Windows 10 裝置。 設定 Windows 10 開發電腦和 Xbox One 之後，可以切換至遠端電腦的目標，並輸入 Xbox One 的 IP 位址部署 UWP 應用程式：

![](uwp-images/remote.png "UWP 應用程式可以部署到遠端電腦切換的目標，並輸入 Xbox 的 IP 位址")

在 Xbox One 白色框線會表示電視非安全區域。 如需詳細資訊，請參閱[安全區域區段](#Safe_Area_on_Xbox_One)。

![](uwp-images/safearea.png "在 Xbox One 白色框線代表非安全區域電視")

### <a name="safe-area-on-xbox-one"></a>在 Xbox 一個安全的區域

開發遊戲主控台需要考慮是一個區域中央的畫面應該包含所有重要的視覺效果 （例如 UI 或抬頭顯示器） 的安全區域。 在安全的區域外部區域並非因此放置在此區域的視覺效果可能會部分或完全不可見的部分顯示畫面上會顯示在所有的電視上。

為 Xbox One MonoGame 範本視為安全的區域，並為白色框線呈現出來。 這個區域也會反映在遊戲的執行階段`Window.ClientBounds`監看式視窗，在 Visual Studio 中的此映像中所示的屬性。 請注意，用戶端界限的高度 1016，儘管 1920 x 1080 顯示器解析度：

![](uwp-images/clientbounds.png "請注意，用戶端界限的高度 1016，儘管 1920 x 1080 顯示解析度")

## <a name="referencing-content-in-uwp-projects"></a>在 UWP 專案中參考的內容

您可以參考 MonoGame 專案中的內容，直接從檔案或透過[MonoGame 內容管線](~/graphics-games/cocossharp/content-pipeline/index.md)。 小型遊戲專案可能可以從檔案載入的簡易性。 較大型的專案將受益於使用內容的管線來最佳化內容，以縮小大小並載入速度。 不同於在 Xbox 360，XNA`System.IO.File`類別位於 Xbox 一個 UWP 應用程式。

如需有關載入內容時使用內容的管線的詳細資訊，請參閱[內容管線指南](~/graphics-games/cocossharp/content-pipeline/index.md)。 

### <a name="loading-content-from-file"></a>從檔案載入內容

不同於 iOS 和 Android，UWP 專案可以參考相對於可執行檔的檔案。 簡單的遊戲可以使用此技巧負載內容而不需要修改和建置內容的管線專案。

若要載入`Texture2D`從檔案：

1. UWP 專案中的內容資料夾加入.png 檔案。 加入的內容資料夾的內容是在 XNA 和 MonoGame 慣例。
1. 以滑鼠右鍵按一下新加入的 PNG，然後選取屬性。
1. 變更**複製到輸出目錄**至**更新時才複製**。
1. 下列程式碼加入至您的遊戲初始化方法以載入`Texture2D`:

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

如需有關使用`Texture2D`，請參閱[MonoGame 指南簡介](~/graphics-games/monogame/introduction/index.md)。

## <a name="summary"></a>總結

本指南涵蓋如何在載入檔案時建立新的 UWP 專案和 UWP 特定考量。 開發人員想要建立完整的 UWP 遊戲閱讀更多有關中 MonoGame [MonoGame 指南簡介](~/graphics-games/monogame/introduction/index.md)。
