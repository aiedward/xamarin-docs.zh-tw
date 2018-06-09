---
title: Xamarin.Forms 協助工具
description: 建置可存取的應用程式可確保應用程式是否可由人利用某個範圍的需求和經驗的使用者介面的方法。
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 813100acad03684fa9db5343534aee7ca13400c1
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/08/2018
ms.locfileid: "35241851"
---
# <a name="xamarinforms-accessibility"></a>Xamarin.Forms 協助工具

_建置可存取的應用程式可確保應用程式是否可由人利用某個範圍的需求和經驗的使用者介面的方法。_

讓 Xamarin.Forms 應用程式能夠存取表示思考的版面配置和設計許多使用者介面項目。 如要考量的問題的指導方針，請參閱[網頁可及性檢查清單](~/cross-platform/app-fundamentals/accessibility.md)。 許多協助工具的考量，如大字型，以及適合色彩和對比設定已經由此 Xamarin.Forms 應用程式開發介面。

[Android 的協助工具](~/android/app-fundamentals/accessibility.md)和[iOS 協助工具](~/ios/app-fundamentals/accessibility.md)指南包含 Xamarin，所公開的原生 Api 的詳細資料和[UWP MSDN 上的協助工具指南](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information)說明在該平台上的原生方法。 這些 Api 可用來完整實作每個平台上的 協助工具應用程式。

目前不具備 Xamarin.Forms*內建*支援所有 Api 可在每一個基礎平台上的存取範圍。 不過，它支援設定協助工具的值在使用者介面項目，以支援畫面的讀取器和導覽協助工具，也就是其中最重要的組件，建立可存取的應用程式。 如需詳細資訊，請參閱[使用者介面項目上設定協助工具值](~/xamarin-forms/app-fundamentals/accessibility/setting-accessibility-values.md)。

其他協助工具應用程式開發介面 (例如[在 iOS 上的 PostNotification](~/ios/app-fundamentals/accessibility.md)) 可能會更適合於[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)或[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)實作。 本指南未涵蓋這些資料。

## <a name="testing-accessibility"></a>測試協助工具

Xamarin.Forms 應用程式通常是多個平台為目標，這表示測試的平台的協助工具功能。 請遵循下列連結，以了解如何測試每個平台上的存取範圍：

- [**iOS 測試**](~/ios/app-fundamentals/accessibility.md)
- [**Android 的測試**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)


## <a name="related-links"></a>相關連結

- [跨平台的協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [在使用者介面元素上設定存取範圍值](~/xamarin-forms/app-fundamentals/accessibility/setting-accessibility-values.md)
