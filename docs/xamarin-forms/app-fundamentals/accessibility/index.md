---
title: Xamarin.Forms 協助工具
description: 建置無障礙應用程式，可確保應用程式便於對使用者介面具有多方面需求和體驗的人員使用。
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: ac0ffbdce6b0c55e8ad9d774d80e3d9b8bf84089
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116442"
---
# <a name="xamarinforms-accessibility"></a>Xamarin.Forms 協助工具

_建置無障礙應用程式，可確保應用程式便於對使用者介面具有多方面需求和體驗的人員使用。_

使 Xamarin.Forms 應用程式便於存取，代表要考慮許多使用者介面元素的版面配置和設計。 請參閱[協助工具檢查清單](~/cross-platform/app-fundamentals/accessibility.md)以取得需考量問題的方針。 Xamarin.Forms API 已經可以解決許多協助工具問題，例如大字型、適當色彩和對比設定。

[Android 的協助工具](~/android/app-fundamentals/accessibility.md)和 [iOS 協助工具](~/ios/app-fundamentals/accessibility.md)指南包含 Xamarin 所公開之原生 API 的詳細資料，並且 [MSDN 上的 UWP 協助工具指南](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information)會說明該平台的原生方法。 這些 API 會用於在每個平台上完整實作無障礙應用程式。

Xamarin.Forms 目前不具備對每個基礎平台上所有可用協助工具 API 的「內建」支援。 不過，Xamarin.Forms 支援在使用者介面元素上設定自動化屬性，以支援螢幕閱讀程式與瀏覽協助工具，也就是建置無障礙應用程式最重要的一部分。 如需詳細資訊，請參閱[自動化屬性](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)。

Xamarin.Forms 應用程式也可以指定控制項的定位順序。 如需詳細資訊，請參閱[鍵盤導覽](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)。

其他協助工具 API (例如 [iOS 上的 PostNotification](~/ios/app-fundamentals/accessibility.md)) 可能更適合於 [`DependencyService`](~/xamarin-forms/app-fundamentals/dependency-service/index.md) 或[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)實作。 本指南並未涵蓋這些內容。

## <a name="testing-accessibility"></a>測試協助工具

Xamarin.Forms 應用程式通常以多個平台為目標，意即會根據平台測試協助工具功能。 請瀏覽下列連結，了解如何測試每個平台上的協助工具：

- [**iOS 測試**](~/ios/app-fundamentals/accessibility.md)
- [**Android 測試**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>相關連結

- [跨平台協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [自動化屬性](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [鍵盤協助工具](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)
