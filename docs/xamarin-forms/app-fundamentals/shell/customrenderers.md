---
title: Xamarin.FormsShell 自訂轉譯器
description: Xamarin.FormsShell 應用程式可透過各種 Shell 類別所公開的屬性和方法進行高度自訂。 不過，它也能夠在需要更複雜的平台專用自訂時，建立 Shell 自訂轉譯器。
ms.prod: xamarin
ms.assetid: 3B1A6AE8-1D1E-4C34-B9AB-48F4444FEF32
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/29/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 76f9b4e243af908e5d00ba8b812dfd143104fe65
ms.sourcegitcommit: 69d9a61ba479f707d96eb4c1c56a4b05a2a2a26f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/30/2020
ms.locfileid: "87426847"
---
# <a name="no-locxamarinforms-shell-custom-renderers"></a>Xamarin.FormsShell 自訂轉譯器

Shell 應用程式的優點之一， Xamarin.Forms 是透過各種 Shell 類別所公開的屬性和方法，可高度自訂其外觀和行為。 不過，它也能夠在需要更複雜的平台專用自訂時，建立 Shell 自訂轉譯器。 如同其他自訂轉譯器，可以將 Shell 自訂轉譯器只加入至一個平台專案來自訂外觀和行為，同時在另一個平台上允許預設行為；或者，可將不同的 Shell 自訂轉譯器加入至每個平台專案，以自訂 iOS 和 Android 上的外觀與行為。

Shell 應用程式會在 iOS 和 Android 上使用 `ShellRenderer` 類別來轉譯。 在 iOS 上，`ShellRenderer` 類別可在下列 `Xamarin.Forms.Platform.iOS` 命名空間中找到。 在 Android 上，`ShellRenderer` 類別可在下列 `Xamarin.Forms.Platform.Android` 命名空間中找到。

建立 Shell 自訂轉譯器的流程如下：

1. 將 `Shell` 類別子類別化。 此動作已經在您的 Shell 應用程式中完成。
1. 取用子類別化的 `Shell` 類別。 此動作已經在您的 Shell 應用程式中完成。
1. 在所需的平台上，建立衍生自 `ShellRenderer` 類別的自訂轉譯器類別。

## <a name="create-a-custom-renderer-class"></a>建立自訂轉譯器類別

建立 Shell 自訂轉譯器類別的流程如下：

1. 建立 `ShellRenderer` 類別的子類別。
1. 覆寫必要的方法來執行必要的自訂。
1. 將 `ExportRendererAttribute` 加入至 `ShellRenderer` 子類別，以指出將使用它來轉譯 Shell 應用程式。 這個屬性是用來向註冊自訂轉譯器 Xamarin.Forms 。

> [!NOTE]
> 您可以選擇在每個平台專案中提供 Shell 自訂轉譯器。 如果自訂轉譯器尚未註冊，則將使用預設的 `ShellRenderer` 類別。

`ShellRenderer` 類別會公開下列可覆寫的方法：

| iOS | Android | UWP |
| --- | --- | --- |
| `SetElementSize`<br />`CreateFlyoutRenderer`<br />`CreateNavBarAppearanceTracker`<br />`CreatePageRendererTracker`<br />`CreateShellFlyoutContentRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellItemTransition`<br />`CreateShellSearchResultsRenderer`<br />`CreateShellSectionRenderer`<br />`CreateTabBarAppearanceTracker`<br />`Dispose`<br />`OnCurrentItemChanged`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`UpdateBackgroundColor` | `CreateFragmentForPage`<br />`CreateShellFlyoutContentRenderer`<br />`CreateShellFlyoutRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellSectionRenderer`<br />`CreateTrackerForToolbar`<br />`CreateToolbarAppearanceTracker`<br />`CreateTabLayoutAppearanceTracker`<br />`CreateBottomNavViewAppearanceTracker`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`SwitchFragment`<br />`Dispose` | `CreateShellFlyoutTemplateSelector`<br />`CreateShellHeaderRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellSectionRenderer`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`UpdateFlyoutBackdropColor`<br />`UpdateFlyoutBackgroundColor` |

`FlyoutItem` 和 `TabBar` 類別為 `ShellItem` 類別的別名，而 `Tab` 類別則為 `ShellSection` 類別的別名。 因此，在為 `FlyoutItem` 物件建立自訂轉譯器時，應該覆寫 `CreateShellItemRenderer` 方法，而在為 `Tab` 物件建立自訂轉譯器時應該覆寫 `CreateShellSectionRenderer` 方法。

> [!IMPORTANT]
> `ShellSectionRenderer` `ShellItemRenderer` 在 IOS、ANDROID 和 UWP 上有其他的 Shell 轉譯器類別，例如和。 不過，這些其他轉譯器類別都會透過在 `ShellRenderer` 類別中覆寫來建立。 因此，自訂這些其他轉譯器類別的行為可藉由將它們子類別化，並在子類別化之 `ShellRenderer` 類別的適當覆寫中建立該子類別的執行個體來實現。

### <a name="ios-example"></a>iOS 範例

下列程式碼範例會顯示適用於 iOS 且子類別化的 `ShellRenderer`，其會在 Shell 應用程式的導覽列上設定背景影像：

```csharp
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(Xaminals.AppShell), typeof(Xaminals.iOS.MyShellRenderer))]
namespace Xaminals.iOS
{
    public class MyShellRenderer : ShellRenderer
    {
        protected override IShellSectionRenderer CreateShellSectionRenderer(ShellSection shellSection)
        {
            var renderer = base.CreateShellSectionRenderer(shellSection);
            if (renderer != null)
            {
                (renderer as ShellSectionRenderer).NavigationBar.SetBackgroundImage(UIImage.FromFile("monkey.png"), UIBarMetrics.Default);
            }
            return renderer;
        }
    }
}
```

`MyShellRenderer` 類別會覆寫 `CreateShellSectionRenderer` 方法，並擷取基底類別所建立的轉譯器。 它接著會在導覽列上設定背景影像，然後返回轉譯器，藉以修改轉譯器。

### <a name="android-example"></a>Android 範例

下列程式碼範例會顯示適用於 Android 且子類別化的 `ShellRenderer`，其會在 Shell 應用程式的導覽列上設定背景影像：

```csharp
using Android.Content;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(Xaminals.AppShell), typeof(Xaminals.Droid.MyShellRenderer))]
namespace Xaminals.Droid
{
    public class MyShellRenderer : ShellRenderer
    {
        public MyShellRenderer(Context context) : base(context)
        {
        }

        protected override IShellToolbarAppearanceTracker CreateToolbarAppearanceTracker()
        {
            return new MyShellToolbarAppearanceTracker(this);
        }
    }
}
```

`MyShellRenderer` 類別會覆寫 `CreateToolbarAppearanceTracker` 方法，並傳回 `MyShellToolbarAppearanceTracker` 類別的執行個體。 下列範例會顯示 `MyShellToolbarAppearanceTracker` 類別 (衍生自 `ShellToolbarAppearanceTracker` 類別)：

```csharp
using Android.Support.V7.Widget;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

namespace Xaminals.Droid
{
    public class MyShellToolbarAppearanceTracker : ShellToolbarAppearanceTracker
    {
        public MyShellToolbarAppearanceTracker(IShellContext context) : base(context)
        {
        }

        public override void SetAppearance(Toolbar toolbar, IShellToolbarTracker toolbarTracker, ShellAppearance appearance)
        {
            base.SetAppearance(toolbar, toolbarTracker, appearance);
            toolbar.SetBackgroundResource(Resource.Drawable.monkey);
        }
    }
}
```

`MyShellToolbarAppearanceTracker` 類別會覆寫 `SetAppearance` 方法，並藉由在工具列上設定背景影像來修改它。

> [!IMPORTANT]
> 僅需將 `ExportRendererAttribute` 加入至衍生自 `ShellRenderer` 類別的自訂轉譯器。 其他子類別化的 Shell 轉譯器類別都會由子類別化的 `ShellRenderer` 類別來建立。

## <a name="related-links"></a>相關連結

- [Xamarin.Forms自訂轉譯器](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
