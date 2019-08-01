---
title: Xamarin 中的影像和圖示
description: 本章節包含各種文章, 其中涵蓋在 Xamarin iOS 應用程式中使用影像的功能, 例如將它們當做圖示、啟動畫面或將畫面包含在控制項中, 以及提供自訂檔案類型的圖示。
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 443cdb5028964e64aeb831f8d706bfdb99f637b2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654450"
---
# <a name="images-and-icons-in-xamarinios"></a>Xamarin 中的影像和圖示

_本章節包含各種文章, 其中涵蓋在 Xamarin iOS 應用程式中使用影像的功能, 例如將它們當做圖示、啟動畫面或將畫面包含在控制項中, 以及提供自訂檔案類型的圖示。_

有數種方式可讓您在 iOS 應用程式內使用影像資產。 從只是將影像顯示為應用程式 ui 的一部分, 並將其指派給 UI 控制項 (例如`UIButton`或`UIImageView`) 以提供圖示和啟動畫面, Xamarin 可讓您以下列方式輕鬆地將絕佳的作品新增至 iOS 應用程式: 

- **解決獨立映射**-使用 iOS 的內建支援, 在不同的裝置解析度和類型 (IPhone、iPad 等) 上使用影像。
- **資產目錄映射集**-使用**資產目錄映射集**來管理和分組應用程式所需之指定影像資產的所有版本。
- **Ios 設計工具中的影像**-使用 ios 設計工具設定控制項的影像。
- 程式**代碼中**的`UIImage`影像–使用類別的方法來載入和使用影像資產, 並將它們指派給程式C#代碼中的 UI 控制項。
- **應用程式圖示**-定義每個 iOS 應用程式所需的應用程式圖示。 這是使用者將從 iOS 主畫面中按一下以啟動應用程式的圖示。 此外, Game Center 會使用此圖示 (如果適用的話)。
- **焦點圖示**-定義應用程式的焦點圖示。 每當使用者在焦點搜尋中輸入應用程式的名稱時, 就會顯示此圖示。
- **設定圖示**-定義應用程式的 [**設定**] 圖示。 如果使用者在其 iOS 裝置上輸入 [**設定**] 應用程式, 此圖示將會顯示在應用程式的 [設定] 清單結尾。 
- **啟動畫面**-定義應用程式的啟動畫面。 在使用者按下應用程式圖示並出現第一個視圖之前, 將會顯示空白畫面。 幸運的是, iOS 包含使用分鏡腳本來顯示影像以取代空白畫面的支援。 
- **ITunes 圖示**-提供 iTune 圖示。 如果使用傳遞應用程式的臨機操作方法 (適用于公司使用者或實際裝置上的 Beta 測試), 開發人員也必須包含512x512 和1024x1024 映射, 以用來代表 iTunes 中的應用程式。
- **檔圖示**-針對 Xamarin iOS 應用程式支援或建立的任何特定檔案類型, 使用影像做為圖示。

建立 iOS 應用程式的映射資產時, 應該考慮幾個考慮, 以及將使用這些資產的幾個地方。 其中每個都不只會影響所需的映射資產數目, 而是如何建立這些資產。 下列主題涵蓋所需的影像資產類型、這些資產如何包含在應用程式的組合中, 以及如何取用影像資產以提供所需的功能:


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

本文說明如何在 Xamarin iOS 應用程式中包含影像資產, 並使用C#程式碼或在 iOS 設計工具中將它指派給控制項, 以顯示該影像。

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[應用程式圖示](~/ios/app-fundamentals/images-icons/app-icons.md)

本文涵蓋的內容包括和管理 Xamarin iOS 應用程式中的影像資產, 以作為應用程式圖示。

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[替代的應用程式圖示](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple 已將數個增強功能新增至 iOS 10.3, 讓應用程式可以管理其圖示:

- `ApplicationIconBadgeNumber`-取得或設定跳板中應用程式圖示的徽章。
- `SupportsAlternateIcons`-如果`true`應用程式有替代的圖示集。
- `AlternateIconName`-傳回目前選取的替代圖示或`null`使用主要圖示的名稱。
- `SetAlternameIconName`-使用此方法可將應用程式的圖示切換至指定的替代圖示。


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)

本文涵蓋使用一種特殊類型的分鏡腳本, 為每個 iOS 裝置大小和解析度提供通用啟動畫面。

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[自訂文件類型](~/ios/app-fundamentals/images-icons/custom-document-types.md)

本文涵蓋的內容包括和管理 Xamarin iOS 應用程式中的影像資產, 以做為自訂檔案類型圖示。



## <a name="related-links"></a>相關連結

- [使用影像 (範例)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和映射建立指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
