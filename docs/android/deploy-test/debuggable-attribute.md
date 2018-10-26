---
title: 可偵錯的屬性
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 66bbc7015daaba04f8431b31f639c9173484c790
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108661"
---
# <a name="debuggable-attribute"></a>可偵錯的屬性



為了進行偵錯，Android 支援 Java 偵錯連線通訊協定 (Java Debug Wire Protocol, JDWP, JDWP)。 這是一項允許如 ADB 的工具與 JVM 進行通訊的技術。 雖然 JDWP 在開發過程中很重要，但在發佈應用程式之前應停用它。

JDWP 的值可為 Android 應用程式中的 `android:debuggable` 屬性。 Xamarin.Android 提供下列方式來設定這個屬性：

1.  建立 `AndroidManifext.xml` 檔案，並在其中設定 `android:debuggable` 屬性。
2.  在 `.CS` 檔案中包含 `ApplicationAttribute`，如：`[assembly: Application(Debuggable=false)]`。


若同時具有 `AndroidManifest.xml` 及 `ApplicationAttribute`，則 `AndroidManifest.xml` 的內容會優先於 `ApplicationAttribute` 所指定的內容。

若 `AndroidManifest.xml` 和 `ApplicationAttribute` 都不存在，則 `android:debuggable` 的預設值便會取決於是否有產生偵錯符號。 若有偵錯符號，則 Xamarin.Android 便會將 `android:debuggable` 屬性設為 `true`。

請注意，`android:debuggable` 屬性的值不一定會相依於組建組態。 發行組建的 `android:debuggable` 屬性也可設為 true。


## <a name="related-links"></a>相關連結

- [Debuggable apps in the Android market](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/) (Android Market 中的可偵錯應用程式)
