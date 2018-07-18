---
title: 可以加入較新的 NuGet 套件更新 Xamarin.Forms 預設範本嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: fc479b4b0651e3312b855673730be21c2076d833
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049830"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>可以加入較新的 NuGet 套件更新 Xamarin.Forms 預設範本嗎？

本指南使用 Xamarin.Forms.NET 標準程式庫範本做為範例，但相同的一般方法也可用於 Xamarin.Forms 共用專案範本。 本指南會寫入 Xamarin.Forms 至 2.1.0.6529 1.5.1.6471 從更新的範例，但改為設定為預設值的其他版本可能會有相同的步驟。

1.  將原始範本複製`.zip`從：

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2.  解壓縮`.zip`到暫存位置。

3.  您可以變更所有 Form 封裝的舊版本的項目到您想要使用新的版本。
    *   `FormsTemplate\FormsTemplate.vstemplate`
    *   `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    *   `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    範例： `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4.  變更主要的 「 名稱 」 項目[多專案範本檔案](http://msdn.microsoft.com/library/ms185308.aspx)(`Xamarin.Forms.PCL.vstemplate`)，使它成為唯一。 例如: 
    > <Name>空白應用程式 （Xamarin.Forms 可攜式）-2.1.0.6529</Name>

5.  重新壓縮整個範本資料夾。 請確定符合原始的檔案結構`.zip`檔案。 `Xamarin.Forms.PCL.vstemplate`檔案應該會在頂端`.zip`檔案，不可以在任何資料夾內。

6.  在您每個使用者的 Visual Studio 範本資料夾中建立 [行動應用程式] 子目錄：
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7.  將新的壓縮向上範本資料夾複製到新的 [行動應用程式] 目錄。

8.  下載符合步驟 3 版本的 NuGet 套件。 例如， [ http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529 ](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (另請參閱[ http://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file ](http://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file))，並將它複製到 Visual Studio 的 Xamarin 擴充功能資料夾的適當子資料夾：
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
