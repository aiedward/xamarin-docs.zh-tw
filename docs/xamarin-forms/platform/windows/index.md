---
title: "Windows 平台功能"
ms.topic: article
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/20/2017
ms.openlocfilehash: f77290a8c780d7dd5c936af576b39228d91687aa
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="windows-platform-features"></a>Windows 平台功能

開發 Windows 平台的 Xamarin.Forms 應用程式需要 Visual Studio。 [需求 頁面](~/xamarin-forms/get-started/installation.md)包含必要條件的詳細資訊。

![](images/allhanselman.png "在 Windows 上執行的 Xamarin.Forms 應用程式")

## <a name="platform-support"></a>平台支援

Xamarin.Forms 範本在 Visual Studio 中使用包含預設的 Windows 專案：

* **通用 Windows 平台應用程式**-Xamarin.Forms 應用程式也可以針對 Windows 10 進行最佳化。 通用 (UWP) 應用程式可以在電話、 平板電腦及桌上型裝置上執行。

如果您已安裝正確的開發選項在 Visual Studio 中，它也可[新增](installation/index.md)這些專案類型，以支援舊版的 Windows:

* **Windows 8.1** -您可以部署到 tablet Xamarin.Forms 應用程式，並使用 WinRT 控制項專案做為 Windows 8.1 應用程式的桌面的表單係數。
* **Windows Phone 8.1** -Xamarin.Forms 已針對 Windows Phone 8.1 的平台使用 WinRT 完整支援。 使用 Windows Phone 8.1 支援應用程式的外觀及操作可能會不同 Xamarin.Forms Windows Phone 應用程式之前以 Silverlight 為基礎。


> [!NOTE]
> Xamarin.Forms 1.x 和 2.x 支援_Windows Phone 8 Silverlight_應用程式開發，但是這種專案類型已被取代。


## <a name="getting-started"></a>快速入門

移至**檔案 > 新增 > 專案**Visual Studio 中，選擇其中一個**跨平台 > 空白應用程式 (Xamarin.Forms)**範本，若要開始使用。

舊的 Xamarin.Forms 方案，或建立 macOS 上並不會以上所列的所有 Windows 專案 （但它們必須以手動方式加入）。
如果您想要的目標 Windows 平台不在您的方案，vist[安裝指示](installation/index.md)新增所需的 Windows 專案類型。


## <a name="samples"></a>範例

[所有範例](https://github.com/xamarin/xamarin-forms-book-preview-2)Charles Petzold 活頁簿的[*建立行動應用程式使用 Xamarin.Forms* ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)包括 Windows Phone 8.1、 Windows 8.1，以及 （適用於 Windows 10) 的通用 Windows 平台專案。

["Scott Hanselman"示範應用程式](https://github.com/jamesmontemagno/Hanselman.Forms)分開，而且也包含 Apple Watch 及 Android 穿專案 （分別使用 Xamarin.iOS 和 Xamarin.Android，Xamarin.Forms 不會執行在這些平台上）。


## <a name="related-links"></a>相關連結

- [安裝 Windows 專案](~/xamarin-forms/platform/windows/installation/index.md)
