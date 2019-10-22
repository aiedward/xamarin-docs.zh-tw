---
ms.openlocfilehash: d46968f9c53314abe561e7f4871cfbf6e07b7002
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2019
ms.locfileid: "61047482"
---
## <a name="firewall-configuration"></a>防火牆設定

為了讓測試提交到 Xamarin Test Cloud，提交測試的電腦必須能夠與測試雲端伺服器通訊。 防火牆必須設定為允許進出埠80和443上**testcloud.xamarin.com**之伺服器的網路流量。 此端點由 DNS 管理，而 IP 位址可能會變更。 

在某些情況下，測試（或執行測試的裝置）必須與受防火牆保護的 web 伺服器通訊。 在此案例中，必須將防火牆設定為允許來自下列 IP 位址的流量：

* **195.249.159.238**
* **195.249.159.239**
