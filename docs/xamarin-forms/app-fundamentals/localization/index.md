---
title: Xamarin.Forms 定位
description: 內建的 .NET 當地語系化架構可以用來建立跨平臺多語系應用程式 Xamarin.Forms 。 文字和影像可以當地語系化，且應用程式可支援由右至左的文字方向。
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5dbcc63eb162454845b78fc24a3beb0ca8296453
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558150"
---
# <a name="no-locxamarinforms-localization"></a>Xamarin.Forms 定位

_內建的 .NET 當地語系化架構可以用來建立跨平臺多語系應用程式 Xamarin.Forms 。_

## <a name="no-locxamarinforms-string-and-image-localization"></a>[Xamarin.Forms 字串和影像當地語系化](text.md)

用於將 .NET 應用程式當地語系化的內建機制，使用 [RESX 檔案](/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files)和 `System.Resources`、`System.Globalization` 命名空間中的類別。 包含翻譯字串的 .RESX 檔案會內嵌在 Xamarin.Forms 元件中，以及編譯器產生的類別，可提供翻譯的強型別存取。 然後即可在程式碼中擷取已翻譯文字。

## <a name="right-to-left-localization"></a>[從右至左當地語系化](right-to-left.md)

文字方向即為眼睛瀏覽頁面 UI 項目的方向。 從右至左的當地語系化可將由右至左流動方向的支援新增至 Xamarin.Forms 應用程式。