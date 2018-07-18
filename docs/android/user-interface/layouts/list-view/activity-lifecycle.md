---
title: ListView 和活動的生命週期
ms.prod: xamarin
ms.assetid: 40840D03-6074-30A2-74DA-3664703E3367
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 6e15fb8796ae6a616c5eae44059caae3d9478aef
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764214"
---
# <a name="listview-and-the-activity-lifecycle"></a>ListView 和活動的生命週期

活動經過特定狀態應用程式執行，例如啟動、 執行、 在暫停和停止。 如需詳細資訊，與處理狀態轉換的特定指導方針，請參閱[活動生命週期教學課程](~/android/app-fundamentals/activity-lifecycle/index.md)。
請務必了解的活動生命週期和位置您`ListView`正確的位置中的程式碼。

這份文件中的範例的所有執行的活動中的 '的安裝工作'`OnCreate`方法和 （要求時） 中執行 '清除' `OnDestroy`。 範例通常會使用小型資料集不會變更，因此無須重新更頻繁地載入資料。

不過，如果您的資料經常變更，或者使用大量的記憶體可能適合使用不同的存留週期方法填入及重新整理您`ListView`。 例如，如果基礎資料經常變更 （或可能會受到其他活動的更新） 然後再建立中的配接器`OnStart`或`OnResume`可確保最新的資料會顯示該活動會顯示每一次。

如果配接器使用的資源，例如記憶體或受管理的資料指標，請記住，釋放這些資源中扮演互補的方法，以其中它們被具現化 （例如 在中建立物件`OnStart`可以中處置`OnStop`)。


## <a name="configuration-changes"></a>設定變更

請務必記住該組態變更&ndash;特別螢幕旋轉和鍵盤的可見性&ndash;可能會導致目前的活動終結並重新建立 (除非您指定其他方式使用`ConfigurationChanges`屬性）。 這表示，在正常的情況下旋轉的裝置將會造成`ListView`和`Adapter`重新建立和 (除非您已經撰寫程式碼`OnPause`和`OnResume`) 捲軸位置和資料列選取項目狀態都將遺失。

下列屬性會防止被終結並重新建立組態變更的結果中的活動：

```csharp
[Activity(ConfigurationChanges="keyboardHidden|orientation")]
```

活動應該覆寫`OnConfigurationChanged`來適當地回應這些變更。 如需有關如何處理組態變更，請參閱文件。

