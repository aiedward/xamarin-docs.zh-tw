---
title: 映像和在 Xamarin.iOS 中的圖示
description: 本節包含各種不同的文章，涵蓋使用 Xamarin.iOS 應用程式，例如使用它們以圖示中的映像，啟動畫面，或它們包括在控制項，並提供自訂的文件類型的圖示。
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: a837d680a21b9cdbc39e42f5fa3520622e0b49aa
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827194"
---
# <a name="images-and-icons-in-xamarinios"></a>映像和在 Xamarin.iOS 中的圖示

_本節包含各種不同的文章，涵蓋使用 Xamarin.iOS 應用程式，例如使用它們以圖示中的映像，啟動畫面，或它們包括在控制項，並提供自訂的文件類型的圖示。_

有數種方式使用 iOS 應用程式內的資產該映像。 只顯示一部分的應用程式 UI 映像指派至 UI 控制項這類`UIButton`或`UIImageView`，若要提供圖示與啟動畫面，Xamarin.iOS 輕鬆地加入 iOS 應用程式中的絕佳作品，透過下列方式： 

- **解析度獨立影像**– 處理跨不同裝置解析度和類型 （iPhone、 iPad） 映像時，用於 iOS 的內建支援。
- **資產目錄的影像集合**-使用**資產目錄映像集**來管理及群組的應用程式所需的特定映像資產的所有版本。
- **IOS 設計工具中的映像**-使用 iOS 設計工具，以設定控制項的映像。
- **在程式碼中的映像**– 使用`UIImage`類別的方法，載入和使用映像資產並將它們指派至 UI 控制項，在C#程式碼。
- **應用程式圖示**-定義每個 iOS 應用程式所需的應用程式圖示。 這是使用者將點選圖示以啟動應用程式的 iOS 主畫面。 此外，此圖示會使用 Game Center，如果適用的話。
- **焦點圖示**-定義應用程式的焦點圖示。 只要使用者在 Spotlight 搜尋中輸入應用程式的名稱，此圖示就會顯示。
- **設定圖示**-定義的應用程式**設定**圖示。 如果使用者輸入**設定**其 iOS 裝置，此圖示上的應用程式將會顯示應用程式的設定清單的結尾。 
- **啟動畫面**-定義應用程式的啟動螢幕。 使用者可點選的應用程式圖示之後，才會出現的第一個檢視，將會顯示空白畫面。 幸好，iOS 會提供支援使用分鏡腳本中顯示影像取代空白畫面。 
- **iTunes 圖示**-提供要圖示。 如果使用提供的應用程式 （無論是公司使用者或 beta 版測試實際裝置上） 的特定方法，開發人員也需要包括 512x512 與 1024x1024 的影像，將用來代表在 iTunes 中的應用程式。
- **文件圖示**-映像作為 Xamarin.iOS 應用程式支援，或建立任何特定文件類型的圖示。

有建立 iOS 應用程式，以及將使用這些資產的其中幾個地方的影像資產時應該列入考量的幾個考量。 每一種有不只幾個影像資產會是必要項，但這些資產的建立方式會影響。 下列主題涵蓋所需的影像資產、 如何在應用程式的套件組合中包含這些資產和影像資產提供必要的功能的使用方式的類型：


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

本文章涵蓋包括在 Xamarin.iOS 應用程式和使用 C# 程式碼，或將它指派給 iOS 設計工具中的控制項，顯示該映像中的影像資產。

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[應用程式圖示](~/ios/app-fundamentals/images-icons/app-icons.md)

本文涵蓋包括及管理在 Xamarin.iOS 應用程式中做為應用程式圖示的影像資產。

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[替代的應用程式圖示](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple iOS 10.3 可讓應用程式來管理它的圖示已新增數個增強功能：

 - `ApplicationIconBadgeNumber` -取得或設定跳板系列中的應用程式圖示的徽章。
 - `SupportsAlternateIcons` -如果`true`應用程式有另一組的圖示。
 - `AlternateIconName` -傳回目前選取的替代圖示的名稱或`null`如果使用的主要圖示。
 - `SetAlternameIconName` -使用這個方法來指定替代的圖示切換應用程式的圖示。


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)

本文章涵蓋使用分鏡腳本的一種特殊類型的每個 iOS 裝置大小和解析度提供通用的啟動螢幕。

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[自訂文件類型](~/ios/app-fundamentals/images-icons/custom-document-types.md)

本文涵蓋包括及管理在 Xamarin.iOS 應用程式中要做為自訂文件類型的圖示的影像資產。



## <a name="related-links"></a>相關連結

- [使用映像 （範例）](https://developer.xamarin.com/samples/monotouch/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和影像建立指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
