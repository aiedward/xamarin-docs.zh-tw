---
title: Xamarin.Forms翻譯
description: 內建的 .NET 當地語系化架構可用來使用建立跨平臺多語系應用程式 Xamarin.Forms 。 文字和影像可以當地語系化，且應用程式可支援由右至左的文字方向。
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: dddb80e8fb683547d2bf6ba0b74e870fe240695c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137562"
---
# <a name="xamarinforms-localization"></a>Xamarin.Forms翻譯

_內建的 .NET 當地語系化架構可用來使用建立跨平臺多語系應用程式 Xamarin.Forms 。_

## <a name="xamarinforms-string-and-image-localizationtextmd"></a>[Xamarin.Forms字串和影像當地語系化](text.md)

用於將 .NET 應用程式當地語系化的內建機制，使用 [RESX 檔案](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files)和 `System.Resources`、`System.Globalization` 命名空間中的類別。 包含翻譯字串的 RESX 檔案會內嵌在 Xamarin.Forms 元件中，以及編譯器所產生的類別，以提供對翻譯的強型別存取。 然後即可在程式碼中擷取已翻譯文字。

## <a name="right-to-left-localization"></a>[從右至左當地語系化](right-to-left.md)

文字方向即為眼睛瀏覽頁面 UI 項目的方向。 由右至左的當地語系化會將由右至左流程方向的支援新增至 Xamarin.Forms 應用程式。
