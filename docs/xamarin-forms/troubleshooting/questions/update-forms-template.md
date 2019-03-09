---
title: 可以更新 Xamarin.Forms 預設範本至較新的 NuGet 套件嗎？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: e439d39dd8591cad14485e64aabab2d6016a8e27
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2019
ms.locfileid: "57668221"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>可以更新 Xamarin.Forms 預設範本至較新的 NuGet 套件嗎？

本指南使用 Xamarin.Forms.NET Standard 程式庫範本做為範例，但相同的一般方法也適用於 Xamarin.Forms 共用專案範本。 本指南以 Xamarin.Forms 1.5.1.6471 至 2.1.0.6529，從更新的範例，但相同的步驟都可以將其他版本設定為預設值。

1.  將原始範本複製`.zip`從：

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2.  將解壓縮`.zip`到暫存位置。

3.  將所有的舊版本的表單封裝發生次數變更為您想要使用的新版本。
    *   `FormsTemplate\FormsTemplate.vstemplate`
    *   `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    *   `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    範例： `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4.  變更主要的 「 名稱 」 項目[多專案範本檔案](https://msdn.microsoft.com/library/ms185308.aspx)(`Xamarin.Forms.PCL.vstemplate`) 使其成為唯一。 例如: 
    > <Name>空白應用程式 （Xamarin.Forms 可攜式）-2.1.0.6529</Name>

5.  重新壓縮整個範本資料夾。 請務必符合的原始的檔案結構`.zip`檔案。 `Xamarin.Forms.PCL.vstemplate`檔案應該在頂端`.zip`不在任何資料夾內的檔案。

6.  在您每個使用者的 Visual Studio 範本資料夾中建立 [行動應用程式] 子目錄：
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7.  將新的壓縮最多範本資料夾複製到新的 [行動應用程式] 目錄中。

8.  下載 NuGet 封裝，以符合步驟 3 中的版本。 例如， [ http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529 ](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (請參閱[ https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file ](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file))，並將它複製到 Xamarin Visual Studio 擴充功能資料夾的適當子資料夾：
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
