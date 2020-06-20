---
title: 我可以將 Xamarin.Forms 預設範本更新為較新的 NuGet 套件嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bdead80671a1ae6539de6614441df7e86863a5a6
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137471"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>我可以將 Xamarin.Forms 預設範本更新為較新的 NuGet 套件嗎？

本指南會使用 Xamarin.Forms .NET Standard 程式庫範本作為範例，但相同的一般方法也適用于共用的 Xamarin.Forms 專案範本。 本指南是以從 Xamarin.Forms 1.5.1.6471 更新至2.1.0.6529 的範例來撰寫，但同樣的步驟也可以將其他版本設定為預設值。

1. `.zip`從下列來源複製原始範本：

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2. 將解壓縮 `.zip` 至暫存位置。

3. 將舊版套件的所有出現專案變更 Xamarin.Forms 為您想要使用的新版本。
    * `FormsTemplate\FormsTemplate.vstemplate`
    * `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    * `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    範例：`<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4. 變更主要[多專案範本](https://msdn.microsoft.com/library/ms185308.aspx)檔案（）的 "name" 元素 `Xamarin.Forms.PCL.vstemplate` ，使其成為唯一的。 例如：

    > `<Name>Blank App (Xamarin.Forms Portable) - 2.1.0.6529</Name>`

5. 將整個範本資料夾重新壓縮。 請務必符合檔案的原始檔案結構 `.zip` 。 檔案 `Xamarin.Forms.PCL.vstemplate` 應該位於檔案的頂端 `.zip` ，而不是在任何資料夾內。

6. 在您的每個使用者 Visual Studio templates 資料夾中建立 "Mobile Apps" 子目錄：
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7. 將新的 [已壓縮的範本] 資料夾複製到新的 [Mobile Apps] 目錄。

8. 下載與步驟3中的版本相符的 NuGet 套件。 例如， [ https://nuget.org/api/v2/package/ Xamarin.Forms /2.1.0.6529](https://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) （另請參閱 [https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file) ），並將它複製到 Xamarin Visual Studio extensions 資料夾的適當子資料夾中：
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
