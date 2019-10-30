---
title: 為什麼 Visual Studio 不在我的組建中包括參考的程式庫專案？
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: b9009db8-e716-43aa-b40e-6f28a8eb1b82
author: davidortinau
ms.author: daortin
ms.date: 12/02/2016
ms.openlocfilehash: aba2e9dacd930c6302a96a8daf929eadab485922
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2019
ms.locfileid: "73012761"
---
# <a name="why-doesnt-visual-studio-include-my-referenced-library-project-in-my-build"></a>為什麼 Visual Studio 不在我的組建中包括參考的程式庫專案？

Visual Studio uses the **Configuration Manager** to determine which projects in a solution are automatically included in a given build or deployment configuration.

Some templates that are generated with a referenced library project will already have the referenced library included in the configuration; but otherwise it will need to be set manually.

## <a name="how-to-use-the-configuration-manager"></a>How to use the Configuration Manager

1. Open **Build > Configuration Manager**
2. Select the configuration to customize, e.g. **Debug | iPhone**
3. Select checkboxes for the projects you wish to include.

> [!NOTE]
> Greyed out boxes are handled automatically, and shouldn't need any changes.

Screencast of these steps: [https://screencast.com/t/zLoQOpEn](https://screencast.com/t/zLoQOpEn)
