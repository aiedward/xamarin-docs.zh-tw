---
title: Xamarin 中的影像和圖示
description: 本節包含各種文章，涵蓋如何在 Xamarin iOS 應用程式中使用影像，例如使用它們作為圖示、啟動畫面或將它們包含在控制項中，以及提供自訂檔案類型的圖示。
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 6048690bc68c3998b67dc89fdc191ea9158ae952
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437277"
---
# <a name="images-and-icons-in-xamarinios"></a>Xamarin 中的影像和圖示

_本節包含各種文章，涵蓋如何在 Xamarin iOS 應用程式中使用影像，例如使用它們作為圖示、啟動畫面或將它們包含在控制項中，以及提供自訂檔案類型的圖示。_

在 iOS 應用程式中使用影像資產的方式有好幾種。 從直接將影像顯示為應用程式 UI 的一部分，並將其指派給 UI 控制項（例如 `UIButton` 或）， `UIImageView` 以提供圖示和啟動畫面，Xamarin 可讓您輕鬆地以下列方式將絕佳的作品新增至 iOS 應用程式： 

- **解決方案獨立映射** -使用 iOS 的內建支援，可在不同裝置解析度和類型 (IPhone、iPad 等 ) 使用影像。
- **資產目錄映射集** -使用 **資產目錄映射集** 來管理和分組應用程式所需之特定影像資產的所有版本。
- **Ios 設計工具中的影像** -使用 ios 設計工具來設定控制項的影像。
- 程式**代碼中的影像**–使用 `UIImage` 類別的方法來載入和使用影像資產，並將它們指派給 c # 程式碼中的 UI 控制項。
- **應用程式圖示** -定義每個 iOS 應用程式所需的應用程式圖示。 這是使用者將從 iOS 主畫面上點擊以啟動應用程式的圖示。 此外，Game Center 也會使用此圖示（如果適用）。
- **焦點圖示** -定義應用程式的焦點圖示。 每當使用者在焦點搜尋中輸入應用程式的名稱時，就會顯示此圖示。
- **設定圖示** -定義應用程式的 **設定** 圖示。 如果使用者在其 iOS 裝置上輸入「 **設定** 」應用程式，則會在應用程式的 [設定] 清單結尾顯示此圖示。 
- **啟動畫面** -定義應用程式的啟動畫面。 當使用者按下應用程式圖示，並在第一個視圖出現之前，會顯示空白畫面。 幸運的是，iOS 支援使用分鏡腳本來顯示影像，以取代空白畫面。 
- **ITunes 圖示** -提供 iTune 圖示。 如果使用提供應用程式的臨機操作方法 (針對公司使用者或在實際裝置上進行搶鮮版（Beta）的測試) ，開發人員也必須包含512x512 和1024x1024 映射，以用來代表 iTunes 中的應用程式。
- **檔圖示** -使用影像做為任何適用于 Xamarin iOS 應用程式所支援或建立之特定檔案類型的圖示。

建立 iOS 應用程式的影像資產時，您應該考慮幾個考慮，以及將使用這些資產的數個位置。 其中每個都不只會影響需要多少映射資產，還會影響這些資產的建立方式。 下列主題涵蓋需要的映射資產類型、應用程式配套中包含這些資產的方式，以及如何取用影像資產以提供必要的功能：

## <a name="displaying-an-image"></a>[顯示影像](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

本文涵蓋如何在 Xamarin iOS 應用程式中包含影像資產，以及如何使用 c # 程式碼或在 iOS 設計工具中將其指派給控制項，以顯示該影像。

## <a name="application-icons"></a>[應用程式圖示](~/ios/app-fundamentals/images-icons/app-icons.md)

本文涵蓋的內容包括和管理要作為應用程式圖示的 Xamarin iOS 應用程式中的影像資產。

## <a name="alternate-app-icons"></a>[替代的應用程式圖示](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple 已在 iOS 10.3 中新增數個增強功能，可讓應用程式管理其圖示：

- `ApplicationIconBadgeNumber` -取得或設定跳板中應用程式圖示的徽章。
- `SupportsAlternateIcons` -如果 `true` 應用程式具有替代的圖示集。
- `AlternateIconName` -傳回目前選取之替代圖示的名稱， `null` 如果使用主要圖示則為。
- `SetAlternameIconName` -使用此方法將應用程式的圖示切換至指定的替代圖示。

## <a name="launch-screens"></a>[啟動畫面](~/ios/app-fundamentals/images-icons/launch-screens.md)

本文涵蓋如何使用一種特殊的分鏡腳本，為每個 iOS 裝置大小和解析度提供通用啟動畫面。

## <a name="custom-document-types"></a>[自訂文件類型](~/ios/app-fundamentals/images-icons/custom-document-types.md)

本文涵蓋的內容包括和管理要做為自訂檔案類型圖示的 Xamarin iOS 應用程式中的影像資產。

## <a name="related-links"></a>相關連結

- [使用影像 (範例) ](/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [自訂圖示和映射建立指導方針](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)