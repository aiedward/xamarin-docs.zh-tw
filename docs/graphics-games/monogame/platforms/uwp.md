---
title: 建立 MonoGame UWP 專案
description: MonoGame 可以用來建立通用 Windows 平臺的遊戲和應用程式，以一個程式碼基底和一組內容為目標的多個裝置為目標。
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 5d970c596d403c7d55ccc23bb5e9ba7e5fbd623a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91431965"
---
# <a name="creating-a-monogame-uwp-project"></a>建立 MonoGame UWP 專案

_MonoGame 可以用來建立通用 Windows 平臺的遊戲和應用程式，以一個程式碼基底和一組內容為目標的多個裝置為目標。_

本逐步解說涵蓋 MonoGame 通用 Windows 平臺 (UWP) 專案的建立和內容載入。 UWP 應用程式可以在所有 Windows 10 裝置上執行，包括桌上型電腦、平板電腦、Windows 手機及 Xbox One。

本逐步解說會建立空的專案，其會顯示 *矢菊花的藍色* 背景 (您) 的操作應用程式傳統背景色彩。

## <a name="requirements"></a>需求

開發 MonoGame UWP 應用程式需要：

- Windows 10 作業系統
- 任何版本的 Visual Studio 2017
- Windows 10 開發人員工具
- 將裝置設定為開發人員模式
- [適用于 Visual Studio 或更新版本的 MonoGame 3.7.1](http://community.monogame.net/t/monogame-3-7-1-release/11173)

如需詳細資訊，請參閱此頁面，以瞭解如何 [針對 WINDOWS 10 UWP 開發進行設定](/windows/uwp/get-started/get-set-up)。

Xbox One 遊戲可以在零售 Xbox One 硬體上進行開發。 開發電腦與 Xbox One 都需要額外的軟體。 如需設定遊戲開發 Xbox One 的詳細資訊，請參閱此頁面上的 [設定 Xbox One](/windows/uwp/xbox-apps/)。

## <a name="creating-an-empty-template"></a>建立空白範本

在 Windows 10 機上安裝所有必要的資源並啟用開發人員模式之後，我們就可以依照下列步驟，使用 Visual Studio 來建立新的 MonoGame 專案：

1. 選取 [檔案]   > [新增]   > [專案...]
1. 選取 [**已安裝**的  >  **範本**]  >  **Visual c #**  >  **MonoGame**類別：

    ![MonoGame 類別](uwp-images/image1.png)

1. 選取 [ **MonoGame Windows 10 通用專案** ] 選項：

    ![選取 [MonoGame Windows 10 通用專案] 選項](uwp-images/image2.png)

1. 輸入新專案的名稱，然後按一下 **[確定]**。
如果 Visual Studio 在按一下 [確定] 之後顯示任何錯誤，請確認已安裝 Windows 10 工具，且裝置處於開發人員模式。

一旦 Visual Studio 完成範本的建立之後，我們就可以執行它來查看執行中的空白專案：

![一旦 Visual Studio 完成範本的建立之後，請執行該範本以查看執行中的空白專案](uwp-images/image3.png)

角落中的數位會提供診斷資訊。 您可以藉由刪除方法的區塊中的程式碼來移除這項資訊 `App.xaml.cs` `DEBUG` `OnLaunched` ：

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

UWP 專案可以從相同專案部署至任何 Windows 10 裝置。 設定 Windows 10 開發電腦和 Xbox One 之後，您可以將目標切換至遠端電腦，並輸入 Xbox One 的 IP 位址，藉以部署 UWP 應用程式：

![您可以將目標切換至遠端電腦，並輸入 Xbox 的 IP 位址，藉以部署 UWP 應用程式](uwp-images/remote.png)

在 Xbox One 上，白色框線代表電視的非安全區域。 如需詳細資訊，請參閱 [ [安全區域] 區段](#safe-area-on-xbox-one)。

![在 Xbox One 上，白色框線代表電視的非安全區域](uwp-images/safearea.png)

### <a name="safe-area-on-xbox-one"></a>Xbox One 上的安全區域

開發適用于主控台的遊戲需要考慮「安全區域」，這是畫面中央的一個區域，其中應包含 UI 或抬頭顯示器) 等所有重要的視覺效果 (。 不保證在所有電視上都能看到安全區域以外的區域，因此放置在此區域的視覺效果可能會在部分顯示器上部分或完全隱藏。

Xbox One 的 MonoGame 範本會將安全區域視為白色框線。 此區域也會在執行時間反映在遊戲的 `Window.ClientBounds` 屬性中，如 Visual Studio 的 [監看式] 視窗的影像所示。 請注意，儘管1920x1080 顯示解析度，用戶端界限的高度是1016：

![請注意，儘管1920x1080 顯示器解析度，用戶端界限的高度是1016](uwp-images/clientbounds.png)

## <a name="referencing-content-in-uwp-projects"></a>在 UWP 專案中參考內容

您可以直接從檔案或透過 [MonoGame 內容管線](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)參考 MonoGame 專案中的內容。 小型遊戲專案可能會因為從檔案載入的簡易性而獲益。 較大型的專案將受益于使用內容管線將內容優化以縮減大小和載入時間。 不同于 Xbox 360 的操作類型， `System.IO.File` 類別可在 XBOX ONE UWP 應用程式上使用。

如需使用內容管線載入內容的詳細資訊，請參閱 [內容管線指南](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md)。

### <a name="loading-content-from-file"></a>正在從檔案載入內容

不同于 iOS 和 Android，UWP 專案可以參考與可執行檔相關的檔案。 簡單的遊戲可以使用這項技術來載入內容，而不需要修改和建立內容管線專案。

若要從檔案載入 `Texture2D` ：

1. 將 .png 檔案新增至 UWP 專案中的 Content 資料夾。 在 [內容] 資料夾中加入內容是 MonoGame 的慣例。
1. 以滑鼠右鍵按一下新加入的 PNG，然後選取 [屬性]。
1. **如果有更新**，請將 [**複製到輸出目錄**] 變更為 [複製]。
1. 將下列程式碼新增至遊戲的 Initialize 方法，以載入 `Texture2D` ：

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

如需使用的詳細資訊 `Texture2D` ，請參閱 [MonoGame 指南簡介](~/graphics-games/monogame/introduction/index.md)。

## <a name="summary"></a>摘要

本指南說明如何在載入檔案時建立新的 UWP 專案和 UWP 特有的考慮。 有興趣建立完整 UWP 遊戲的開發人員可以在《 [MonoGame Guide 簡介》](~/graphics-games/monogame/introduction/index.md)中閱讀更多有關 MonoGame 的資訊。