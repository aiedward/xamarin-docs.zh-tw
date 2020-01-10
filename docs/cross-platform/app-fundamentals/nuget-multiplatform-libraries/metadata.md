---
title: 編輯 NuGet 中繼資料
description: 本檔說明如何使用專案選項來編輯多平臺程式庫的 NuGet 中繼資料。 它會討論必要和選擇性的中繼資料。
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 5e71ff86869c42d478fbf1ee3f6de5bff59431fc
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728209"
---
# <a name="editing-nuget-metadata"></a>編輯 NuGet 中繼資料

_使用專案選項來編輯多平臺程式庫的 NuGet 中繼資料_

程式庫專案類型（例如 PCL 或 .NET Standard 或新的 NuGet 專案類型）具有 [**專案選項**] 視窗中的 [ **NuGet 套件**] 區段。

[**中繼資料**] 區段會設定[ **nuspec** NuGet 套件資訊清單](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file)檔中使用的值。

## <a name="required-information"></a>必要資訊

[**一般**] 索引標籤包含四個必須輸入才能產生 NuGet 套件的欄位：

[![](metadata-images/metadata-general-sml.png "NuGet package required metadata window")](metadata-images/metadata-general.png#lightbox)

- **識別碼–套件**識別碼，在 NuGet.org 中（或封裝的散發位置）必須是唯一的。 遵循此[指引](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number)，並只使用 URL 中有效的字元（不含空格，並避免大部分的特殊字元）。
- **版本**-選擇與[NuGet 版本控制規則](https://docs.microsoft.com/nuget/create-packages/dependency-versions)一致的版本號碼。
- **作者**–以逗號分隔的名稱清單。
- **描述**–在使用者選取套件時所顯示的套件功能總覽。

> [!NOTE]
> 請記得在建立新版本以散發至 NuGet 或其他使用者時，遞增版本號碼。

如需詳細資訊，請參閱[必要專案參考](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements)以取得詳細資訊，以及這些有關[選擇唯一封裝識別碼和設定版本號碼](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number)和[設定套件類型](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type)的詳細指示。

> [!IMPORTANT]
> 必須輸入此索引標籤上的所有欄位;否則，將會出現錯誤訊息：「_專案沒有 nuget 中繼資料，因此將不會建立 nuget 套件。您可以在 [專案選項] 的 [中繼資料] 區段中指定 NuGet 套件中繼資料_。

## <a name="optional-metadata"></a>選擇性中繼資料

[**詳細資料**] 索引標籤包含要包含在 NuGet 套件資訊清單檔案中的選擇性欄位。

[![](metadata-images/metadata-detail-sml.png "NuGet package optional metadata window")](metadata-images/metadata-detail.png#lightbox)

如需必要和選擇性欄位的詳細資訊，請參閱[選擇性元素參考](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements)。

> [!NOTE]
> 如果 NuGet 套件是在[NuGet.org](https://www.nuget.org)上散發，建議您盡可能提供最多的資訊。

## <a name="related-links"></a>相關連結

- [.nuspec 參考](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
