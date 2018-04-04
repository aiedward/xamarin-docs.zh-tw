---
title: 影像和圖示
description: 本節包含各種不同的文件涵蓋使用 Xamarin.iOS 應用程式，例如使用它們以圖示中的映像，啟動畫面，或它們包括在控制項，並提供用於自訂的文件類型的圖示。
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: fd191c898d5bb015d2d394d42db1049bb0128fb7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
---
# <a name="images-and-icons"></a>影像和圖示

_本節包含各種不同的文件涵蓋使用 Xamarin.iOS 應用程式，例如使用它們以圖示中的映像，啟動畫面，或它們包括在控制項，並提供用於自訂的文件類型的圖示。_

有幾種方式可在 iOS 應用程式內使用資產該映像。 只顯示一部分的應用程式的 UI，例如指派到 UI 控制項影像`UIButton`或`UIImageView`，提供圖示和啟動畫面，Xamarin.iOS 輕鬆地加入 iOS 應用程式中的絕佳作品，以下列方式： 

- **解析度獨立影像**– 用於 iOS 的內建支援處理跨不同裝置解析度和型別 （iPhone、 iPad） 的映像。
- **資產目錄映像集**-使用**資產目錄映像集**來管理及群組的應用程式所需的特定映像資產的所有版本。
- **在 iOS 設計工具中的映像**-使用 iOS 設計工具，以設定控制項的影像。
- **在程式碼中的映像**– 使用`UIImage`類別的方法來載入和使用的影像資產並將它們指派至 C# 程式碼中的 UI 控制項。
- **應用程式圖示**-定義每個 iOS 應用程式所需的應用程式圖示。 這是使用者會點選圖示從 [iOS] 主畫面啟動應用程式。 此外，使用這個圖示是由遊戲中心的話。
- **醒目圖示**-定義應用程式的焦點圖示。 每當使用者 Spotlight 搜尋中，輸入應用程式的名稱，則會顯示這個圖示。
- **設定圖示**-定義應用程式的**設定**圖示。 如果使用者輸入**設定**其 iOS 裝置，此圖示上的應用程式將會顯示在應用程式的設定清單的結尾。 
- **啟動畫面**-定義應用程式的啟動螢幕。 在使用者點選應用程式圖示之後，才會出現的第一個檢視，將會顯示空白的畫面。 幸運的是，iOS 會包含顯示影像取代空白畫面，藉由將分鏡腳本的支援。 
- **iTunes 圖示**-提供 iTune 圖示。 如果使用傳送應用程式 （無論是公司的使用者或是在實際裝置上測試） 的特定方法時，開發人員也必須包含 512 x 512，1024 x 1024 映像，將用來表示應用程式，在 iTunes。
- **文件圖示**-影像當做 Xamarin.iOS 應用程式支援，或建立任何特定文件類型的圖示。

有建立 iOS 應用程式，以及將會使用這些資產的幾個地方的影像資產時應該納入考量的幾個考量。 每一種有不只多少影像資產會是必要項目，但這些資產的建立方式會影響。 下列主題涵蓋將需要的影像資產、 這些資產如何納入應用程式的組合和影像資產以提供必要的功能的使用方式類型：


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

本文涵蓋包括 Xamarin.iOS 應用程式和使用 C# 程式碼，或將其指派給 iOS 設計工具中的控制項，顯示該映像中的影像資產。

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[應用程式圖示](~/ios/app-fundamentals/images-icons/app-icons.md)

本文涵蓋包括和管理 Xamarin.iOS 應用程式中要做為應用程式圖示的影像資產。

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[替代的應用程式圖示](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple iOS 10.3 提供可讓應用程式管理它的圖示已加入數個增強功能：

 - `ApplicationIconBadgeNumber` -取得或設定應用程式圖示的徽章 Springboard 中。
 - `SupportsAlternateIcons` -如果`true`應用程式有替代組圖示。
 - `AlternateIconName` -傳回目前選取的替代圖示的名稱或`null`如果使用主要的圖示。
 - `SetAlternameIconName` -使用這個方法指定替代的圖示切換應用程式的圖示。


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)

這篇文章說明如何使用每個 iOS 裝置大小和解析度為提供通用的啟動螢幕的一種特殊的分鏡腳本。

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[自訂文件類型](~/ios/app-fundamentals/images-icons/custom-document-types.md)

本文涵蓋包括和管理 Xamarin.iOS 應用程式中要做為自訂文件類型圖示的影像資產。



## <a name="related-links"></a>相關連結

- [使用映像 （範例）](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和映像建立指導方針](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
