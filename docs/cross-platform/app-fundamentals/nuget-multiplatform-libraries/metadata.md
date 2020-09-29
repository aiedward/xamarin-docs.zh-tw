---
title: 編輯 NuGet 中繼資料
description: 本檔說明如何使用專案選項來編輯多平臺程式庫的 NuGet 中繼資料。 它會討論必要和選用的中繼資料。
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: a3e1e8d1be1f84f707c80c42adb6b8d1e3f234a9
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457012"
---
# <a name="editing-nuget-metadata"></a>編輯 NuGet 中繼資料

_使用專案選項來編輯多平臺程式庫的 NuGet 中繼資料_

程式庫專案類型 (例如 PCL 或 .NET Standard，或新的 NuGet 專案類型) 在 [**專案選項**] 視窗中具有 [ **nuget 套件**] 區段。

**中繼資料**區段會設定[ **nuspec** NuGet 套件資訊清單](/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file)檔案中使用的值。

## <a name="required-information"></a>必要資訊

[ **一般** ] 索引標籤包含必須輸入以產生 NuGet 套件的四個欄位：

[![NuGet 套件所需的中繼資料視窗](metadata-images/metadata-general-sml.png)](metadata-images/metadata-general.png#lightbox)

- **識別碼** –套件識別碼，在 NuGet.org (或封裝散發) 的任何位置都應該是唯一的。 遵循此 [指引](/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) ，並只使用 URL 中有效的字元 (沒有空格，並避免) 大部分的特殊字元。
- **版本** –選擇與 [NuGet 的版本控制規則](/nuget/create-packages/dependency-versions)一致的版本號碼。
- **作者** –以逗號分隔的名稱清單。
- **描述** -概述當使用者選取套件時所顯示的套件功能。

> [!NOTE]
> 請記得在建立新版本以散發給 NuGet 或其他使用者時，遞增版本號碼。

如需詳細資訊，請參閱 [必要的元素參考](/nuget/schema/nuspec#required-metadata-elements) ，以取得詳細資訊，以及有關 [選擇唯一封裝識別碼和設定版本號碼](/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) 及 [設定套件類型](/nuget/create-packages/creating-a-package#setting-a-package-type)的詳細指示。

> [!IMPORTANT]
> 此索引標籤上的所有欄位都必須輸入;否則會出現錯誤訊息：「_專案沒有 nuget 中繼資料，因此將不會建立 nuget 套件。您可以在 [專案選項] 的 [中繼資料] 區段中指定 NuGet 套件中繼資料_。

## <a name="optional-metadata"></a>選擇性中繼資料

[ **詳細資料** ] 索引標籤包含 NuGet 套件資訊清單檔案中包含的選擇性欄位。

[![NuGet 套件選用中繼資料視窗](metadata-images/metadata-detail-sml.png)](metadata-images/metadata-detail.png#lightbox)

如需必要和選擇性欄位的詳細資訊，請參閱選擇性的專案 [參考](/nuget/schema/nuspec#optional-metadata-elements) 。

> [!NOTE]
> 如果在 [NuGet.org](https://www.nuget.org) 上發佈 NuGet 套件，建議盡可能提供最多的資訊。

## <a name="related-links"></a>相關連結

- [nuspec 參考](/nuget/schema/nuspec#general-form-and-schema)