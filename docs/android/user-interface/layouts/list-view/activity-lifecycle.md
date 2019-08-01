---
title: Xamarin. Android ListView 和活動生命週期
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 7c6e395a353dcfd737ad244df9d169edc5b08f1c
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510299"
---
# <a name="xamarinandroid-listview-and-the-activity-lifecycle"></a>Xamarin. Android ListView 和活動生命週期

活動會在應用程式執行時經歷特定狀態, 例如啟動、執行、暫停和停止。 如需詳細資訊, 以及處理狀態轉換的特定指導方針, 請參閱[活動生命週期教學](~/android/app-fundamentals/activity-lifecycle/index.md)課程。
請務必瞭解活動生命週期, 並將您的`ListView`程式碼放在正確的位置。

本檔中的所有範例都會在活動的`OnCreate`方法中執行「設定工作」, 並在中`OnDestroy`執行「清除」。 這些範例通常會使用不會變更的小型資料集, 因此不需要更頻繁地重新載入資料。

不過, 如果您的資料經常變更或使用許多記憶體, 則可能適合使用不同的生命週期方法來填入和`ListView`重新整理。 例如, 如果基礎資料經常變更 (或可能會受到其他活動的更新影響), 則在或`OnStart` `OnResume`中建立介面卡, 可確保每次顯示活動時都會顯示最新的資料。

如果介面卡使用記憶體或受控資料指標之類的資源, 請記得將這些資源以互補方法釋放到其具現化的位置 (例如, 在中`OnStart`建立的物件可以在中`OnStop`處置。


## <a name="configuration-changes"></a>設定變更

請務必記住, 設定變更&ndash;特別是螢幕旋轉和鍵盤可見度&ndash; , 會導致目前的活動遭到終結並重新建立 (除非`ConfigurationChanges`您以其他方式使用屬性)。 這表示在正常情況下, `ListView`輪替裝置會導致重新建立和`Adapter` (除非您已在和`OnResume`中`OnPause`撰寫程式碼), 而且捲軸位置和資料列選取狀態將會遺失。

下列屬性會防止活動因設定變更而遭終結並重新建立:

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

活動應該會覆寫`OnConfigurationChanged` , 以適當地回應這些變更。 如需如何處理設定變更的詳細資訊, 請參閱檔。

