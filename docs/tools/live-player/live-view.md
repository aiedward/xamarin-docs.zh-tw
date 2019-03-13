---
redirect_url: /xamarin/tools/live-player/
title: XAML Live 預覽
description: 本文件討論如何使用 Xamarin Live Player 即時預覽 XAML 頁面、 變更 XAML，並查看立即出現在裝置上的變更。
ms.prod: xamarin
ms.assetid: 86E9A179-21F8-4F3A-A9CE-36F0FC5DB4A8
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: 1602c98eceaff607c79400a37c4ace60d5bf8807
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110592"
---
# <a name="xaml-live-previewing"></a>XAML Live 預覽

Xamarin Live Player 的好處之一是能夠即時預覽 XAML 頁面、 在 Visual Studio 中，程式碼進行變更及查看立即出現在您的裝置上的變更。 在您的 Android 裝置上或在模擬器或模擬器上，可即時預覽。 本指南示範如何使用即時預覽功能來檢視個別 XAML 螢幕。

## <a name="requirements"></a>需求

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 執行 Windows 7 或更新版本的電腦。
2. Visual Studio 2017 15.4 版或更高版本**使用.NET 進行行動開發**安裝工作負載。

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. Mac OS X 10.11，macOS 10.12，或更新版本。
2. Visual Studio for Mac 7.2 或更新版本。 我們建議的最新版本。

-----

<a name="deploydevice" />

## <a name="deploying-to-device"></a>部署至裝置

您可以使用 Xamarin Live Player 與您的 Android 裝置之前，您必須下載 Xamarin Live Player 應用程式，並將其加入 Visual Studio 中所述[安裝](~/tools/live-player/install.md)指南。 一旦您已成功配對您的裝置，Visual studio，您就可以開始即時預覽 XAML 頁面。 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 開啟您想要在 Visual Studio 2017 編輯器中的即時預覽 XAML 頁面：

    ![](live-view-images/vs-image1.png)

2. 將裝置組態設定為**偵錯**並從清單中選取的 Live Player 裝置：

    ![](live-view-images/vs-image2.png)

3. 若要執行此 XAML 頁面在您的裝置上的即時檢視，請選取**工具 > Xamarin Live Player > 即時執行目前檢視**從功能表列：

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 開啟您想要在 Visual Studio 中的即時預覽 Mac 編輯器的 XAML 頁面：

    ![](live-view-images/image1.png)

2. 將裝置組態設定為**偵錯**並從清單中選取的 Live Player 裝置：

    ![](live-view-images/image2.png)

3. 若要執行此 XAML 頁面在您的裝置上的即時檢視，請選取**執行 > 即時執行目前檢視**從功能表列：

    ![](live-view-images/image3.png)

-----

## <a name="deploying-to-android-emulator"></a>部署至 Android 模擬器

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. 開啟您想要在 Visual Studio 2017 編輯器中的即時預覽 XAML 頁面：

    ![](live-view-images/vs-image1.png)

2. 將裝置組態設定為**偵錯**適用於 Android 和選取從清單中的 Live Player 裝置：

    ![](live-view-images/vs-image4.png)

3. 若要執行 Android 模擬器上的即時檢視此 XAML 頁面，請選取**工具 > Xamarin Live Player > 即時執行目前檢視**從功能表列：

    ![](live-view-images/vs-image3.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio for Mac](#tab/macos)

1. 開啟您想要在 Visual Studio 中的即時預覽 Mac 編輯器的 XAML 頁面：

    ![](live-view-images/image7.png)

2. 將裝置組態設定為**偵錯**適用於 Android 和選取從清單中的 Live Player 裝置：

    ![](live-view-images/image6.png)

3. 若要執行此 XAML 頁面為您的裝置上的即時檢視，選取 執行 > 即時執行目前檢視從功能表列：

    ![](live-view-images/image3.png)

-----

## <a name="related-links"></a>相關連結

- [Xamarin Live Player 概觀](https://xamarin.com/live)
- [部落格文章](https://blog.xamarin.com/live-player/)
- [Xamarin Live Player 範例](~/tools/live-player/samples.md)
