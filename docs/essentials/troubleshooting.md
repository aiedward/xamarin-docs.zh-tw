---
title: Xamarin.Essentials： 疑難排解
description: 本文件說明如何使用 Xamarin.Essentials 程式庫進行開發時所遇到的問題進行疑難排解。
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 06/26/2018
ms.openlocfilehash: 8cb18ab029d2fd161c60fda7e130f319b8f0c3ab
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947370"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials： 疑難排解

![發行前版本的 NuGet](~/media/shared/pre-release.png)

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Xamarin.Android.Support.Compat 偵測到的錯誤： 版本衝突

更新 NuGet 套件時，可能會發生下列錯誤 （或新增新的封裝） 與使用 Xamarin.Essentials 的 Xamarin.Forms 專案：

```
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.8.0-preview -> Xamarin.Android.Support.CustomTabs 27.0.2.1 -> Xamarin.Android.Support.Compat (= 27.0.2.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

問題在於不相符的兩個 Nuget 的相依性。 這可以解決手動加入特定版本的相依性 (在此情況下**Xamarin.Android.Support.Compat**)，可以同時支援。

若要這樣做，請新增 NuGet 以手動的方式，做為衝突的來源並使用**版本**清單來選取特定版本。 目前版本的 Xamarin.Android.Support.Compat Xamarin.Android.Support.Core.Util NuGet 27.0.2.1 會解決這個錯誤。

請參閱[此部落格文章](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/)的詳細資訊，並提供有關如何解決此問題的影片。

如果遇到任何問題或尋找 bug 請於下列位置回報[Xamarin.Essentials GitHub 存放庫](http://github.com/xamarin/Essentials)。
