---
title: 編輯 NuGet 中繼資料
description: 本文件說明如何使用專案選項，以編輯多平台程式庫的 NuGet 中繼資料。 它會討論必要和選擇性的中繼資料。
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 3680b02003a844668b0b5c97e5d4c0d296ae3500
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266866"
---
# <a name="editing-nuget-metadata"></a>編輯 NuGet 中繼資料

_若要編輯多平台程式庫的 NuGet 中繼資料使用的專案選項_

程式庫專案類型 （例如 PCL 或.NET Standard，或新的 NuGet 專案類型） 都有**NuGet 套件**一節**專案選項**視窗。

**中繼資料**區段會設定中使用的值[ **.nuspec** NuGet 封裝資訊清單檔](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file)。

## <a name="required-information"></a>所需的資訊

**一般** 索引標籤包含四個欄位，必須輸入要產生 NuGet 封裝：

[![](metadata-images/metadata-general-sml.png "NuGet 套件所需的中繼資料 視窗")](metadata-images/metadata-general.png#lightbox)

- **識別碼**– Nuget.org （或套件散發的任何地方） 內應該是唯一的套件識別碼。 請遵循這[指引](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number)而且只會使用在 URL 中有效的字元 (不含空格，並避免大部分的特殊字元)。
- **版本**– 選擇和一致的版本號碼[NuGet 的版本控制規則](https://docs.microsoft.com/nuget/create-packages/dependency-versions)。
- **作者**– 以逗點分隔名稱清單。
- **描述**– 當使用者選取的封裝時，會顯示封裝的功能概觀。

> [!NOTE]
> 請務必建立新的版本發佈至 NuGet 或其他使用者時，遞增版本號碼。

如需詳細資訊，請參閱 <<c0> [ 所需的項目參考](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements)如需詳細資訊，以及為這些上詳述的指示[選擇唯一的套件識別碼並設定版本號碼](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number)並[設定套件類型](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type)。

> [!IMPORTANT]
> 此索引標籤上的所有欄位都必須都輸入;否則，會出現一則錯誤訊息：_「 專案沒有 NuGet 中繼資料，因此不會建立 NuGet 套件。NuGet 套件中繼資料可以在專案選項中的 [中繼資料] 區段中指定 」_

## <a name="optional-metadata"></a>選擇性中繼資料

**詳細資料** 索引標籤包含要包含在 NuGet 封裝資訊清單檔中的選擇性欄位。

[![](metadata-images/metadata-detail-sml.png "NuGet 封裝的選擇性中繼資料 視窗")](metadata-images/metadata-detail.png#lightbox)

請參閱[選擇性的項目參考](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements)如需有關必要和選擇性欄位。

> [!NOTE]
> 如果在發佈 NuGet 套件時[NuGet.org](https://www.nuget.org)建議提供資訊越多越好。


## <a name="related-links"></a>相關連結

- [.nuspec 參考](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
