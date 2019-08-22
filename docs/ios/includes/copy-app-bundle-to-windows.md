---
ms.openlocfilehash: 22a8542c0e829db8b889abc2c7fe5f5ab9d19ed4
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/16/2019
ms.locfileid: "69527630"
---

在 Visual Studio 和 Mac Build Agent 中建置 iOS 應用程式時，.app 套件組合不會複製回 Windows 電腦。 適用於 Visual Studio 7.4 的 Xamarin 工具加入一個新的 `CopyAppBundle` 屬性，可讓 CI 組建將 .app 套件組合複製回 Windows。

若要使用此功能，請將 `CopyAppBundle` 屬性加入至 .csproj 中您想要套用此功能的屬性群組底下。 例如，下列範例示範如何將 .app 套件組合複製回 Windows 電腦，以取得適用於 **iPhoneSimulator** 的**偵錯**組建：

```xml
<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">
    <CopyAppBundle>true</CopyAppBundle>
</PropertyGroup>
```
