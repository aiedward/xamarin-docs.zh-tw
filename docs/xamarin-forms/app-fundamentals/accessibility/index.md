---
title: Xamarin.Forms 的協助工具
description: 建置可存取的應用程式可確保應用程式是否可由方法的使用者介面，利用某個範圍的需求和體驗的人。
ms.prod: xamarin
ms.assetid: 99B8A8E8-6F5E-46BC-9639-1C4A6D301049
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/15/2018
ms.openlocfilehash: ac0ffbdce6b0c55e8ad9d774d80e3d9b8bf84089
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116442"
---
# <a name="xamarinforms-accessibility"></a>Xamarin.Forms 的協助工具

_建置可存取的應用程式可確保應用程式是否可由方法的使用者介面，利用某個範圍的需求和體驗的人。_

讓 Xamarin.Forms 應用程式能夠存取表示思考的版面配置和設計許多使用者介面項目。 如要考量的問題的指導方針，請參閱 <<c0> [ 協助工具檢查清單](~/cross-platform/app-fundamentals/accessibility.md)。 Xamarin.Forms Api 已經可以解決許多協助工具問題，例如大字型和適當的色彩和對比設定。

[Android 的協助工具](~/android/app-fundamentals/accessibility.md)並[iOS 協助工具](~/ios/app-fundamentals/accessibility.md)指南包含的 Xamarin，所公開的原生 Api 的詳細資訊和[UWP MSDN 上的協助工具指南](https://msdn.microsoft.com/windows/uwp/accessibility/basic-accessibility-information)說明該平台的原生方法。 這些 Api 用來完全實作在每個平台上的 無障礙應用程式。

目前不具備 Xamarin.Forms*內建*支援所有協助工具在每個基礎平台上可用的 Api。 不過，它支援設定自動化屬性在使用者介面項目，以支援螢幕讀取器與瀏覽的協助工具，也就是其中一個最重要的部分，建置可存取的應用程式。 如需詳細資訊，請參閱 <<c0> [ 自動化屬性](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)。

Xamarin.Forms 應用程式也可以指定的控制項的定位順序。 如需詳細資訊，請參閱 <<c0> [ 鍵盤瀏覽](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)。

其他 Api 的協助工具 (例如[在 iOS 上的 PostNotification](~/ios/app-fundamentals/accessibility.md)) 可能更適合[ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md)或[自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)實作。 本指南並未涵蓋這些。

## <a name="testing-accessibility"></a>測試協助工具

Xamarin.Forms 應用程式通常是多個平台為目標，這表示測試根據的平台的協助工具功能。 請遵循下列連結以了解如何測試在每個平台上的協助工具：

- [**iOS 測試**](~/ios/app-fundamentals/accessibility.md)
- [**Android 的測試**](~/android/app-fundamentals/accessibility.md)
- [**Windows AccScope (MSDN)**](https://msdn.microsoft.com/library/windows/desktop/dn433239)

## <a name="related-links"></a>相關連結

- [跨平台的協助工具](~/cross-platform/app-fundamentals/accessibility.md)
- [自動化屬性](~/xamarin-forms/app-fundamentals/accessibility/automation-properties.md)
- [鍵盤協助工具](~/xamarin-forms/app-fundamentals/accessibility/keyboard.md)
