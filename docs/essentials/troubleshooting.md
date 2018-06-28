---
title: Xamarin.Essentials： 疑難排解
description: 本文件說明如何疑難排解使用 Xamarin.Essentials 程式庫進行開發時遇到的問題。
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 06/26/2018
ms.openlocfilehash: 3dba315aec2475cb334110ba7555f773f4165aa1
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066730"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials： 疑難排解

![發行前版本的 NuGet](~/media/shared/pre-release.png)

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Xamarin.Android.Support.Compat 偵測到的錯誤： 版本衝突

更新 NuGet 封裝時，可能會發生下列錯誤 （或加入新的封裝） 與使用 Xamarin.Essentials Xamarin.Forms 專案：

```
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.7.0.17-preview -> Xamarin.Android.Support.CustomTabs 27.0.2 -> Xamarin.Android.Support.Compat (= 27.0.2) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

問題在於不相符的相依性的兩個 NuGets。 這可以透過手動方式新增特定版本的相依性解析 (在此情況下**Xamarin.Android.Support.Compat**)，可以同時支援。

若要這樣做，請加入 NuGet，手動為衝突的來源並使用**版本**清單來選取特定版本。 目前版本 27.0.2 Xamarin.Android.Support.Compat NuGet 會解決此錯誤。

請參閱[此部落格文章](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/)如需詳細資訊和如何解決問題的視訊。

如果遇到任何問題或 bug 請回報尋找[Xamarin.Essentials GitHub 儲存機制](http://github.com/xamarin/Essentials)。
