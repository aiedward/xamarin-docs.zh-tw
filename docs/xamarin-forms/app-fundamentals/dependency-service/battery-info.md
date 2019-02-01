---
title: 檢查電池狀態
description: 本文說明如何使用 Xamarin.Forms DependencyService 類別，以原生方式存取每個平台的電池資訊。
ms.prod: xamarin
ms.assetid: CF1C5A73-84ED-407D-BDC5-EB1D83D2D3DB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/09/2016
ms.openlocfilehash: 08278c2bc380892706320dbd0e69642257b73005
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233779"
---
# <a name="checking-battery-status"></a>檢查電池狀態

[![下載範例](~/media/shared/download.png) 下載範例](https://developer.xamarin.com/samples/DependencyService)

本文會逐步解說如何建立可檢查電池狀態的應用程式。 本文是以 James Montemagno 的電池外掛程式為依據。 如需詳細資訊，請參閱 [GitHub 存放庫](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery)。

由於 Xamarin.Forms 不包含檢查目前電池狀態的功能，因此本應用程式必須使用 [`DependencyService`](xref:Xamarin.Forms.DependencyService) 來利用原生 API。  本文涵蓋使用 `DependencyService` 的下列步驟：

- **[建立介面](#Creating_the_Interface)** &ndash; 了解如何在共用程式碼中建立介面。
- **[iOS 實作](#iOS_Implementation)** &ndash; 了解如何以適用於 iOS 的原生程式碼來實作介面。
- **[Android 實作](#Android_Implementation)** &ndash; 了解如何以適用於 Android 的原生程式碼來實作介面。
- **[通用 Windows 平台實作](#UWPImplementation)** &ndash; 了解如何以適用於通用 Windows 平台 (UWP) 的原生程式碼來實作介面。
- **[在共用程式碼中實作](#Implementing_in_Shared_Code)** &ndash; 了解如何使用 `DependencyService` 從共用程式碼呼叫原生實作。

完成時，使用 `DependencyService` 的應用程式會有下列結構：

![](battery-info-images/battery-diagram.png "DependencyService 應用程式結構")

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>建立介面

首先，在共用程式碼中建立介面以表示所需的功能。 如果是檢查電池的應用程式，其相關資訊包括剩餘電量百分比、裝置是否收費，以及裝置如何接收電源：

```csharp
namespace DependencyServiceSample
{
  public enum BatteryStatus
  {
    Charging,
    Discharging,
    Full,
    NotCharging,
    Unknown
  }

  public enum PowerSource
  {
    Battery,
    Ac,
    Usb,
    Wireless,
    Other
  }

  public interface IBattery
  {
    int RemainingChargePercent { get; }
    BatteryStatus Status { get; }
    PowerSource PowerSource { get; }
  }
}
```

以共用程式碼撰寫這個介面的程式碼時，可讓 Xamarin.Forms 應用程式存取每個平台上的電源管理 API。

> [!NOTE]
> 實作介面的類別必須具有無參數的建構函式，才能使用 `DependencyService`。 介面無法定義建構函式。

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>iOS 實作

您必須在每個平台特定應用程式專案中實作 `IBattery` 介面。 iOS 實作會使用原生 [`UIDevice`](xref:UIKit.UIDevice) API 來存取電池資訊。 請注意，下列類別具有無參數的建構函式，以便讓 `DependencyService` 可以建立新的執行個體：

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;

namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery
  {
    public BatteryImplementation()
    {
      UIDevice.CurrentDevice.BatteryMonitoringEnabled = true;
    }

    public int RemainingChargePercent
    {
      get
      {
        return (int)(UIDevice.CurrentDevice.BatteryLevel * 100F);
      }
    }

    public BatteryStatus Status
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return BatteryStatus.Charging;
          case UIDeviceBatteryState.Full:
            return BatteryStatus.Full;
          case UIDeviceBatteryState.Unplugged:
            return BatteryStatus.Discharging;
          default:
            return BatteryStatus.Unknown;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        switch (UIDevice.CurrentDevice.BatteryState)
        {
          case UIDeviceBatteryState.Charging:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Full:
            return PowerSource.Ac;
          case UIDeviceBatteryState.Unplugged:
            return PowerSource.Battery;
          default:
            return PowerSource.Other;
        }
      }
    }
  }
}
```

最後，將這個 `[assembly]` 屬性新增到類別上方 (且在任何已定義的命名空間外)，包括任何必要的 `using` 陳述式：

```csharp
using UIKit;
using Foundation;
using DependencyServiceSample.iOS;//necessary for registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.iOS
{
  public class BatteryImplementation : IBattery {
    ...
```

這個屬性會將類別註冊為 `IBattery` 介面的實作，這表示 `DependencyService.Get<IBattery>` 可以在共用程式碼中用來建立其執行個體：

<a name="Android_Implementation" />

## <a name="android-implementation"></a>Android 實作

Android 實作會使用 [`Android.OS.BatteryManager`](https://developer.xamarin.com/api/type/Android.OS.BatteryManager/) API。 這項實作比 iOS 版本更複雜，需要檢查以處理電池權限不足的問題：

```csharp
using System;
using Android;
using Android.Content;
using Android.App;
using Android.OS;
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid;

namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery
  {
    private BatteryBroadcastReceiver batteryReceiver;
    public BatteryImplementation() { }

    public int RemainingChargePercent
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              var level = battery.GetIntExtra(BatteryManager.ExtraLevel, -1);
              var scale = battery.GetIntExtra(BatteryManager.ExtraScale, -1);

              return (int)Math.Floor(level * 100D / scale);
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }

      }
    }

    public DependencyServiceSample.BatteryStatus Status
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;
              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);
              if (isCharging)
                return DependencyServiceSample.BatteryStatus.Charging;

              switch(status)
              {
                case (int)BatteryStatus.Charging:
                  return DependencyServiceSample.BatteryStatus.Charging;
                case (int)BatteryStatus.Discharging:
                  return DependencyServiceSample.BatteryStatus.Discharging;
                case (int)BatteryStatus.Full:
                  return DependencyServiceSample.BatteryStatus.Full;
                case (int)BatteryStatus.NotCharging:
                  return DependencyServiceSample.BatteryStatus.NotCharging;
                default:
                  return DependencyServiceSample.BatteryStatus.Unknown;
              }
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }

    public PowerSource PowerSource
    {
      get
      {
        try
        {
          using (var filter = new IntentFilter(Intent.ActionBatteryChanged))
          {
            using (var battery = Application.Context.RegisterReceiver(null, filter))
            {
              int status = battery.GetIntExtra(BatteryManager.ExtraStatus, -1);
              var isCharging = status == (int)BatteryStatus.Charging || status == (int)BatteryStatus.Full;

              var chargePlug = battery.GetIntExtra(BatteryManager.ExtraPlugged, -1);
              var usbCharge = chargePlug == (int)BatteryPlugged.Usb;
              var acCharge = chargePlug == (int)BatteryPlugged.Ac;

              bool wirelessCharge = false;
              wirelessCharge = chargePlug == (int)BatteryPlugged.Wireless;

              isCharging = (usbCharge || acCharge || wirelessCharge);

              if (!isCharging)
                return DependencyServiceSample.PowerSource.Battery;
              else if (usbCharge)
                return DependencyServiceSample.PowerSource.Usb;
              else if (acCharge)
                return DependencyServiceSample.PowerSource.Ac;
              else if (wirelessCharge)
                return DependencyServiceSample.PowerSource.Wireless;
              else
                return DependencyServiceSample.PowerSource.Other;
            }
          }
        }
        catch
        {
          System.Diagnostics.Debug.WriteLine ("Ensure you have android.permission.BATTERY_STATS");
          throw;
        }
      }
    }
  }
}
```

將這個 `[assembly]` 屬性新增到類別上方 (且在任何已定義的命名空間外)，包括任何必要的 `using` 陳述式：

```csharp
...
using BatteryStatus = Android.OS.BatteryStatus;
using DependencyServiceSample.Droid; //enables registration outside of namespace

[assembly: Xamarin.Forms.Dependency (typeof (BatteryImplementation))]
namespace DependencyServiceSample.Droid
{
  public class BatteryImplementation : IBattery {
    ...
```

這個屬性會將類別註冊為 `IBattery` 介面的實作，這表示 `DependencyService.Get<IBattery>` 可以在共用程式碼中用來建立其執行個體：

<a name="UWPImplementation" />

## <a name="universal-windows-platform-implementation"></a>通用 Windows 平台實作

UWP 實作會使用 `Windows.Devices.Power` API 來取得電池狀態資訊：

```csharp
using DependencyServiceSample.UWP;
using Xamarin.Forms;

[assembly: Dependency(typeof(BatteryImplementation))]
namespace DependencyServiceSample.UWP
{
    public class BatteryImplementation : IBattery
    {
        private BatteryStatus status = BatteryStatus.Unknown;
        Windows.Devices.Power.Battery battery;

        public BatteryImplementation()
        {
        }

        private Windows.Devices.Power.Battery DefaultBattery
        {
            get
            {
                return battery ?? (battery = Windows.Devices.Power.Battery.AggregateBattery);
            }
        }

        public int RemainingChargePercent
        {
            get
            {
                var finalReport = DefaultBattery.GetReport();
                var finalPercent = -1;

                if (finalReport.RemainingCapacityInMilliwattHours.HasValue && finalReport.FullChargeCapacityInMilliwattHours.HasValue)
                {
                    finalPercent = (int)((finalReport.RemainingCapacityInMilliwattHours.Value /
                        (double)finalReport.FullChargeCapacityInMilliwattHours.Value) * 100);
                }
                return finalPercent;
            }
        }

        public BatteryStatus Status
        {
            get
            {
                var report = DefaultBattery.GetReport();
                var percentage = RemainingChargePercent;

                if (percentage >= 1.0)
                {
                    status = BatteryStatus.Full;
                }
                else if (percentage < 0)
                {
                    status = BatteryStatus.Unknown;
                }
                else
                {
                    switch (report.Status)
                    {
                        case Windows.System.Power.BatteryStatus.Charging:
                            status = BatteryStatus.Charging;
                            break;
                        case Windows.System.Power.BatteryStatus.Discharging:
                            status = BatteryStatus.Discharging;
                            break;
                        case Windows.System.Power.BatteryStatus.Idle:
                            status = BatteryStatus.NotCharging;
                            break;
                        case Windows.System.Power.BatteryStatus.NotPresent:
                            status = BatteryStatus.Unknown;
                            break;
                    }
                }
                return status;
            }
        }

        public PowerSource PowerSource
        {
            get
            {
                if (status == BatteryStatus.Full || status == BatteryStatus.Charging)
                {
                    return PowerSource.Ac;
                }
                return PowerSource.Battery;
            }
        }
    }
}
```

命名空間宣告上的 `[assembly]` 屬性會將類別註冊為 `IBattery` 介面的實作，這表示 `DependencyService.Get<IBattery>` 可以在共用程式碼中用來建立其執行個體：

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>在共用程式碼中實作

現在，每個平台均已實作介面，即可撰寫共用應用程式來加以利用。 應用程式會提供一個具有按鈕的頁面，當點選按鈕時，就會使用目前的電池狀態來更新其文字。 它會使用 `DependencyService` 來取得 `IBattery` 介面的執行個體。 在執行階段，這個執行個體將會是具有原生 SDK 完整存取權的平台特定實作。

```csharp
public MainPage ()
{
    var button = new Button {
        Text = "Click for battery info",
        VerticalOptions = LayoutOptions.CenterAndExpand,
        HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
    button.Clicked += (sender, e) => {
        var bat = DependencyService.Get<IBattery>();

        switch (bat.PowerSource){
          case PowerSource.Battery:
            button.Text = "Battery - ";
            break;
          case PowerSource.Ac:
            button.Text = "AC - ";
            break;
          case PowerSource.Usb:
            button.Text = "USB - ";
            break;
          case PowerSource.Wireless:
            button.Text = "Wireless - ";
            break;
          case PowerSource.Other:
          default:
            button.Text = "Other - ";
            break;
        }
        switch (bat.Status){
          case BatteryStatus.Charging:
            button.Text += "Charging";
            break;
          case BatteryStatus.Discharging:
            button.Text += "Discharging";
            break;
          case BatteryStatus.NotCharging:
            button.Text += "Not Charging";
            break;
          case BatteryStatus.Full:
            button.Text += "Full";
            break;
          case BatteryStatus.Unknown:
          default:
            button.Text += "Unknown";
            break;
        }
    };
    Content = button;
}
```

在 iOS、Android 或 UWP 上執行此應用程式，然後按下按鈕，就會更新按鈕文字以反映裝置的目前電源狀態。

![](battery-info-images/battery.png "電池狀態範例")


## <a name="related-links"></a>相關連結

- [DependencyService (Samples)](https://developer.xamarin.com/samples/DependencyService)
- [使用 DependencyService (範例)](https://developer.xamarin.com/samples/UsingDependencyService/)
- [Xamarin.Forms 範例](https://github.com/xamarin/xamarin-forms-samples)
