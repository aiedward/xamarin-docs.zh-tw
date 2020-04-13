---
title: Xamarin.Forms 當地語系化
description: 內建的 .NET 當地語系化架構，可用於透過 Xamarin.Forms 建置跨平台多語系應用程式。 文字和影像可以當地語系化，且應用程式可支援由右至左的文字方向。
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
ms.openlocfilehash: b580c6e41aa689ff8fcea698c40d7aaf5f2ca050
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "74135270"
---
# <a name="xamarinforms-localization"></a>Xamarin.Forms 當地語系化

_內建的 .NET 當地語系化架構，可用於透過 Xamarin.Forms 建置跨平台多語系應用程式。_

## <a name="xamarinforms-string-and-image-localization"></a>[Xamarin.表單字串和影像當地語系化](text.md)

用於將 .NET 應用程式當地語系化的內建機制，使用 [RESX 檔案](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files)和 `System.Resources`、`System.Globalization` 命名空間中的類別。 包含已翻譯字串的 RESX 檔案內嵌在 Xamarin.Forms 組件中，外加編譯器產生的類別，此類別提供對翻譯的強型別存取。 然後即可在程式碼中擷取已翻譯文字。

## <a name="right-to-left-localization"></a>[由右至左當地語系化](right-to-left.md)

文字方向即為眼睛瀏覽頁面 UI 項目的方向。 從右至左的當地語系化，會將由右至左文字方向的支援新增至 Xamarin.Forms 應用程式。
