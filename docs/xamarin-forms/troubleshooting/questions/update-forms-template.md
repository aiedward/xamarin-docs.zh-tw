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
ms.openlocfilehash: 9dc5b8e63a35c3a0b797d0794af7a31aea4969c9
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91555589"
---
# <a name="can-i-update-the-no-locxamarinforms-default-template-to-a-newer-nuget-package"></a>我可以將 Xamarin.Forms 預設範本更新為較新的 NuGet 套件嗎？

本指南使用 Xamarin.Forms .NET Standard 程式庫範本作為範例，但相同的一般方法也適用于 Xamarin.Forms 共用的專案範本。 本指南是以從1.5.1.6471 更新為2.1.0.6529 的範例撰寫的 Xamarin.Forms ，但相同的步驟也可以改為將其他版本設定為預設值。

1. 複製原始範本的 `.zip` 來源：

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2. 將解壓縮 `.zip` 至暫存位置。

3. 將舊版封裝的所有出現專案變更 Xamarin.Forms 為您想要使用的新版本。
    * `FormsTemplate\FormsTemplate.vstemplate`
    * `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    * `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    範例：`<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4. 將主要 [多專案範本](/visualstudio/ide/how-to-create-multi-project-templates) 檔案的 "name" 元素變更 (`Xamarin.Forms.PCL.vstemplate`) ，使其成為唯一的。 例如：

    > `<Name>Blank App (Xamarin.Forms Portable) - 2.1.0.6529</Name>`

5. 重新壓縮整個範本資料夾。 請務必符合檔案的原始檔案結構 `.zip` 。 檔案 `Xamarin.Forms.PCL.vstemplate` 應位於檔案的最上方 `.zip` ，而不是在任何資料夾內。

6. 在您的每個使用者 Visual Studio 範本資料夾中建立 "Mobile Apps" 子目錄：
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7. 將新的壓縮範本資料夾複製到新的 "Mobile Apps" 目錄。

8. 從步驟3下載符合版本的 NuGet 套件。 例如， [ https://nuget.org/api/v2/package/ Xamarin.Forms /2.1.0.6529](https://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (另請參閱 [https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file)) ，並將它複製到 Xamarin Visual Studio extensions 資料夾的適當子資料夾中：
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`