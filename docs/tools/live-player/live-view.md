---
title: "XAML 即時預覽"
description: "測試您的 iOS 或 Android 裝置上的即時應用程式程式碼變更"
ms.topic: article
ms.prod: xamarin
ms.assetid: 19B1F126-866E-4672-92D2-BE2B70ACF0F1
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 12/21/2017
ms.openlocfilehash: 0982818556eb3aac55b30343075017c3773199ec
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2018
---
# <a name="xaml-live-previewing"></a>XAML 即時預覽

Xamarin Live 播放程式的優點之一是能夠即時預覽 XAML 頁面，在 Visual Studio 中的程式碼進行變更，看到立即出現在您的裝置上的變更。 在您的 iOS 或 Android 裝置上，或在模擬器或模擬器，可即時預覽。 本指南示範如何使用即時預覽功能來檢視個別的 XAML 螢幕。

## <a name="requirements"></a>需求

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 執行 Windows 7 或更高版本的電腦。
2. Visual Studio 2017 15.4 或更新版本與版本**行動應用程式開發的.NET**安裝的工作負載。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. Mac OS X 10.11 macOS 10.12，或更新版本。
2. Visual Studio for Mac 7.2 或更新版本。 我們建議的最新版本。

-----



<a name="deploydevice" />

## <a name="deploying-to-device"></a>部署至裝置

您可以使用 Xamarin Live Player 與您的 iOS 或 Android 裝置之前，您必須先下載 Xamarin Live 播放器應用程式，並將其加入 Visual Studio 中所述[安裝](~/tools/live-player/install.md)指南。 一旦您擁有已成功地搭配您 Visual Studio 的裝置，您可以開始即時預覽您的 XAML 頁面。 

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 開啟您想要在 Visual Studio 2017 編輯器中的即時預覽的 XAML 頁面：

    ![](live-view-images/vs-image1.png)

2. 設定裝置設定為**偵錯 | iPhone**適用於 iOS 或**偵錯**Android 和即時 Player 裝置清單中選取：

    ![](live-view-images/vs-image2.png)

3. 若要執行此 XAML 頁面為您的裝置上的即時檢視，選取**工具 > Xamarin Live Player > 即時執行的目前檢視**從功能表列：

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 開啟您想要在 Visual Studio 中的即時預覽 Mac 編輯器的 XAML 頁面：

    ![](live-view-images/image1.png)

2. 設定裝置設定為**偵錯 | iPhone**適用於 iOS 或**偵錯**Android 和即時 Player 裝置清單中選取：

    ![](live-view-images/image2.png)

3. 若要執行此 XAML 頁面為您的裝置上的即時檢視，選取**執行 > 即時執行的目前檢視**從功能表列：

    ![](live-view-images/image3.png)

-----








## <a name="deploying-to-android-emulator"></a>部署至 Android 模擬器

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. 開啟您想要在 Visual Studio 2017 編輯器中的即時預覽的 XAML 頁面：

    ![](live-view-images/vs-image1.png)

2. 設定裝置設定為**偵錯**Android 和即時 Player 裝置清單中選取：

    ![](live-view-images/vs-image4.png)

3. 若要以 Android 模擬器上的即時檢視，請執行此 XAML 頁面，選取**工具 > Xamarin Live Player > 即時執行的目前檢視**從功能表列：

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 開啟您想要在 Visual Studio 中的即時預覽 Mac 編輯器的 XAML 頁面：

    ![](live-view-images/image7.png)

2. 設定裝置設定為**偵錯**Android 和即時 Player 裝置清單中選取：

    ![](live-view-images/image6.png)

3. 若要執行此 XAML 頁面為您的裝置上的即時檢視，選取 [執行] > 即時執行目前的檢視從功能表列：

    ![](live-view-images/image3.png)

-----





## <a name="deploying-to-ios-simulator"></a>部署至 iOS 模擬器。

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

目前沒有使用在 Windows 上的遠端 iOS 模擬器上的即時 XAML 預覽支援。 您應該改為[部署至裝置](#deploydevice)。

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio for Mac](#tab/vsmac)

1. 開啟您想要在 Visual Studio 中的即時預覽 Mac 編輯器的 XAML 頁面：

    ![](live-view-images/image1.png)

2. 設定裝置設定為**偵錯 | iPhoneSimulator**適用於 iOS 和 iOS 模擬器中，從清單選取：

    ![](live-view-images/image2.png)

3. 選取**執行 > 即時執行的目前檢視**從功能表列，以啟動模擬器並顯示您的 XAML 頁面：

    ![](live-view-images/image4.png)

4. 一旦啟動模擬器，您可以開始編輯 XAML，以及查看顯示即時預覽：

    ![](live-view-images/image5.png)  

-----








## <a name="related-links"></a>相關連結

- [Xamarin Player 即時概觀](https://xamarin.com/live)
- [部落格文章](https://blog.xamarin.com/live-player/)
- [Xamarin Player 即時範例](~/tools/livehttps://developer.xamarin.com/samples.md)
