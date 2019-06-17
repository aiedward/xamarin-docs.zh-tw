---
title: ListView 和活動生命週期
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: b2328759b3158920bc8683ec14c2aebefd7a04ae
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2019
ms.locfileid: "61187069"
---
# <a name="listview-and-the-activity-lifecycle"></a>ListView 和活動生命週期

活動經過特定狀態為您的應用程式執行，例如啟動、 執行、 暫停和停止。 如需詳細資訊，以及處理狀態轉換的特定指導方針，請參閱 <<c0> [ 活動生命週期教學課程](~/android/app-fundamentals/activity-lifecycle/index.md)。
請務必了解的活動生命週期和地方您`ListView`正確的位置中的程式碼。

所有的這份文件中的範例執行的活動中的 '的安裝工作'`OnCreate`方法和必要時執行中的 '清除' `OnDestroy`。 範例通常會使用小型資料集不會變更，因此重新更頻繁地載入資料是不必要。

不過，如果您的資料經常變更，或使用大量的記憶體可能適合使用不同的生命週期方法來填入及重新整理您`ListView`。 例如，如果基礎資料經常變更 （或可能會受到其他活動上的更新） 然後再建立的配接器`OnStart`或`OnResume`可確保最新的資料會顯示該活動會顯示每一次。

如果配接器使用的資源，例如記憶體或受管理的資料指標，請記得釋出那些資源中，它們已具現化 （例如的互補方法 在中建立的物件`OnStart`可以中處置`OnStop`)。


## <a name="configuration-changes"></a>設定變更

請務必記得該設定變更&ndash;特別是畫面旋轉和鍵盤的可見性&ndash;可能會導致目前的活動終結並重新建立 (除非另外指定，否則使用`ConfigurationChanges`屬性）。 這表示，在正常的情況下旋轉的裝置將會造成`ListView`並`Adapter`重新建立和 (除非您撰寫程式碼`OnPause`和`OnResume`) 捲軸位置和資料列選取範圍狀態將會遺失。

下列屬性會防止將活動從被終結並重新建立組態變更的結果：

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

活動應該再覆寫`OnConfigurationChanged`適當回應這些變更。 如需有關如何處理組態變更的詳細資訊，請參閱文件。

