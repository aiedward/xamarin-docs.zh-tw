---
title: 移植 Visual basic.net
description: 本指南將說明如何使用 Visual Basic 撰寫 Xamarin.iOS 和 Xamarin.Android 為目標的方案中可用的可攜式類別程式庫 (PCL) 專案。
ms.prod: xamarin
ms.assetid: f264c632-8feb-4015-a5e5-cb9c681c787d
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 71ee153135df97d3b4fa149d3d788d3b940fe944
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2018
ms.locfileid: "33917963"
---
# <a name="portable-visual-basicnet"></a>移植 Visual basic.net

Xamarin iOS 和 Android 專案原本不支援 Visual Basic;不過開發人員可以使用可攜式類別庫，將現有的 Visual Basic 程式碼移轉至 iOS 和 Android，或在 Visual Basic 中撰寫的應用程式邏輯的重要部分。 Xamarin.Forms 應用程式可以建立完全在 Visual Basic 中 （不包括自訂轉譯器、 相依性服務和 XAML 程式碼後置）。

## <a name="requirements"></a>需求

Xamarin.Android 4.10.1 Xamarin.iOS 7.0.4 和 Xamarin Studio 4.2，這表示使用這些工具所建立的任何 Xamarin 專案就可以將 Visual Basic PCL 組件已加入可攜式類別庫支援。

若要建立及編譯 Visual Basic 可攜式類別庫中，您必須使用 Visual Studio Windows (Visual Studio 2012 或更新版本) 上。

> [!NOTE]
> Visual Basic 程式庫只能建立和使用 Visual Studio 編譯。 Xamarin.iOS 和 Xamarin.Android 不支援 Visual Basic 語言。
>
> 如果您在 Visual Studio 中您可以從 Xamarin.iOS 和 Xamarin.Android 專案參考 Visual Basic 專案。
>
> 如果您的 iOS 和 Android 專案必須也要載入 Visual Studio 中的 Mac 您應該從 Visual Basic PCL 參考輸出組件。


## <a name="creating-a-visual-basicnet-pcl"></a>建立 Visual basic.net PCL

本節逐步解說如何建立使用 Visual Studio 的 Visual Basic 可攜式類別庫。
然後，程式庫可參考其他專案，包括 Xamarin.iOS、 Xamarin.Android 和 Xamarin.Forms 應用程式中。

### <a name="creating-a-pcl"></a>建立 PCL

在 Visual Studio 中加入 Visual Basic PCL 時，您必須選擇描述何種平台程式庫應該與相容的設定檔。 設定檔簡介 PCL 文件中說明。

建立 PCL 並選擇其設定檔的步驟如下：

1.  在**新專案**畫面上，選取**Visual Basic > 類別庫 （可攜式）** 選項：

    [![](images/image1-sml.png "建立新的 Visual Basic 可攜式程式庫")](images/image1.png#lightbox)

1.  Visual Studio 會立即出現提示，以下列**加入可攜式類別庫**對話方塊，讓您可以設定設定檔。 刻度的平台，您需要支援，並按下**確定**。

    [![](images/image2-sml.png "藉由選擇平台選取 PCL 設定檔")](images/image2.png#lightbox)

1.  Visual Basic PCL 專案中所示，會顯示**方案總管 中**如下所示：

    [![](images/image3-sml.png "空白的 Visual Studio PCL 專案")](images/image3.png#lightbox)


PCL 現在已準備好要加入的 Visual Basic 程式碼的。 PCL 專案可以參考其他專案 （應用程式專案、 程式庫專案和甚至是其他 PCL 專案）。

### <a name="editing-the-pcl-profile"></a>編輯 PCL 設定檔

PCL 設定檔 （可控制哪些 PCL 是相容的平台） 可以檢視和變更專案上按一下滑鼠右鍵，然後選擇**屬性 > 文件庫 > 變更...**.在這個螢幕擷取畫面顯示產生的對話方塊：

 [![](images/image4-sml.png "在專案屬性中編輯 PCL 設定檔")](images/image4.png#lightbox)

如果在設定檔之後加入 PCL 已新增過程式碼變更，，很可能如果程式碼會參考不屬於新選取的設定檔的功能，將不再編譯程式庫。


## <a name="summary"></a>總結

本文示範如何使用 Visual Basic 程式碼中使用 Visual Studio 和可攜式類別庫的 Xamarin 應用程式。 雖然 Xamarin 不直接支援 Visual Basic，Visual Basic 編譯 PCL 可讓使用要包含在 iOS 和 Android 應用程式中的 Visual Basic 撰寫的程式碼。

下列頁面說明如何使用 Visual basic.net PCLs 原生或 Xamarin.Forms 應用程式：

- [建立原生 Xamarin.iOS 和 Xamarin.Android 應用程式使用 VB](native-apps.md)
- [Vb 建置 Xamarin.Forms 應用程式](xamarin-forms.md)


## <a name="related-links"></a>相關連結

- [TaskyPortableVB （範例）](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/TaskyPortableVB)
- [XamarinFormsVB （範例）](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [使用.NET Framework (Microsoft) 進行跨平台開發](http://msdn.microsoft.com/library/gg597391(v=vs.110).aspx)
