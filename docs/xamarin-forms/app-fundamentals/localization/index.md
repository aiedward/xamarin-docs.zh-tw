---
title: Xamarin.Forms 當地語系化
description: 內建的.NET 當地語系化 framework 可用來建置跨平台與 Xamarin.Forms 多語系應用程式。
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/13/2018
ms.openlocfilehash: 29624eeccc8542b3296774f6b77480b664bee478
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2018
---
# <a name="localization"></a>當地語系化

_內建的.NET 當地語系化 framework 可用來建置跨平台與 Xamarin.Forms 多語系應用程式。_

## <a name="string-and-image-localizationtextmd"></a>[字串和當地語系化映像](text.md)

當地語系化 .NET 應用程式使用的內建機制[RESX 檔案](http://msdn.microsoft.com/library/ekyft91f(v=vs.90).aspx)中的類別和`System.Resources`和`System.Globalization`命名空間。 RESX 檔案包含已翻譯的字串會內嵌 Xamarin.Forms 組件，以及編譯器所產生的類別，提供強型別存取翻譯。 然後可以在程式碼中擷取翻譯的文字。

## <a name="right-to-left-localizationright-to-leftmd"></a>[由右至左當地語系化](right-to-left.md)

資料流程方向是所在頁面的 UI 元素會掃描該眼睛的方向。 由右至左當地語系化加入 Xamarin.Forms 應用程式的支援由右至左文字方向。
