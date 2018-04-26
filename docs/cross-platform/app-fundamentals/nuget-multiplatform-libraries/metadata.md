---
title: 編輯 NuGet 中繼資料
description: 若要編輯多平台程式庫 NuGet 中繼資料中使用專案選項
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6d30f564d54b96d358d37059f9dababaf8f3314e
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/26/2018
---
# <a name="editing-nuget-metadata"></a>編輯 NuGet 中繼資料

_若要編輯多平台程式庫 NuGet 中繼資料中使用專案選項_

程式庫專案類型 （例如 PCL 或.NET 標準，或新的 NuGet 專案類型） 會有**NuGet 封裝**一節中**專案選項**視窗。

**中繼資料**區段設定中使用的值[ **.nuspec** NuGet 封裝資訊清單檔](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file)。

## <a name="required-information"></a>必要的資訊

**一般** 索引標籤包含四個欄位，必須輸入要產生的 NuGet 封裝：

[![](metadata-images/metadata-general-sml.png "NuGet 封裝所需的中繼資料視窗")](metadata-images/metadata-general.png#lightbox)

- **識別碼**– 封裝識別碼，這應該是唯一 Nuget.org （或套件發佈的任一處）。 後面要接著[指引](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number)而且只會使用在 URL 中的字元 (沒有空格，並避免大部分的特殊字元)。
- **版本**– 選擇版本號碼與一致[NuGet 的版本控制規則](https://docs.microsoft.com/nuget/create-packages/dependency-versions)。
- **作者**– 以逗點分隔名稱清單。
- **描述**-當使用者選取的封裝時，會顯示封裝的功能概觀。

> [!NOTE]
> 請記得建置發佈給 NuGet 或其他使用者的新版本時，遞增版本號碼。

如需詳細資訊，請參閱[所需的項目參考](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements)如需詳細資訊，以及為這些詳細指示上[選擇唯一的封裝識別碼，以及設定的版本號碼](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number)和[設定封裝類型](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type)。

> [!IMPORTANT]
> 此索引標籤上的所有欄位都必須都輸入;否則，會出現錯誤訊息： _"專案沒有 NuGet 中繼資料，將不會建立在 NuGet 封裝。專案選項中的中繼資料 區段中只能指定 NuGet 套件中繼資料 」_

## <a name="optional-metadata"></a>選擇性中繼資料

**詳細資料** 索引標籤包含要包含在 NuGet 封裝資訊清單檔中的選擇性欄位。

[![](metadata-images/metadata-detail-sml.png "NuGet 套件的選擇性中繼資料視窗")](metadata-images/metadata-detail.png#lightbox)

請參閱[選擇性項目參考](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements)如需有關必要和選擇性欄位。

> [!NOTE]
> 如果上發佈 NuGet 套件時[NuGet.org](https://www.nuget.org)建議您提供的資訊越好。


## <a name="related-links"></a>相關連結

- [.nuspec 參考](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
