---
標題：「 Xamarin.Forms 應用程式生命週期」描述：「本文將說明如何回應應用程式生命週期，包括生命週期方法、頁面通知事件和強制回應導覽事件。」
assetid： 69B416CF-B243-4790-AB29-F030B32465BE ms. 技術： xamarin-表單作者： davidbritch ms. author： dabritch ms. 日期：05/31/2018 否-loc： [ Xamarin.Forms ， Xamarin.Essentials ]
---

# <a name="xamarinforms-app-lifecycle"></a>Xamarin.Forms應用程式生命週期

[`Application`](xref:Xamarin.Forms.Application)基類提供下列功能：

- [生命週期方法](#lifecycle-methods) `OnStart` 、 `OnSleep` 和 `OnResume` 。
- [頁面導覽事件](#page-navigation-events) [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing) 、 [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) 。
- 強制回應[導覽事件](#modal-navigation-events) `ModalPushing` 、、 `ModalPushed` `ModalPopping` 和 `ModalPopped` 。

## <a name="lifecycle-methods"></a>生命週期方法

[`Application`](xref:Xamarin.Forms.Application)類別包含三個可覆寫以回應生命週期變更的虛擬方法：

- `OnStart` - 會在應用程式啟動時呼叫。
- `OnSleep` - 會在每次應用程式被移到背景時呼叫。
- `OnResume` - 會在應用程式被傳送到背景後又再次繼續時呼叫。

> [!NOTE]
> 「沒有」** 任何終止應用程式的方法。 正常情況下 (亦即非當機時) 會從 *OnSleep* 狀態終止應用程式，而不會提供程式碼任何其他通知。

若要觀察這些方法的呼叫時機，請在每個方法中實作 `WriteLine` 呼叫 (如下所示)，並在每個平台上進行測試。

```csharp
protected override void OnStart()
{
    Debug.WriteLine ("OnStart");
}
protected override void OnSleep()
{
    Debug.WriteLine ("OnSleep");
}
protected override void OnResume()
{
    Debug.WriteLine ("OnResume");
}
```

> [!IMPORTANT]
> 在 Android 上，`OnStart` 方法將會被輪流呼叫，同時也會在應用程式首次啟動時被呼叫，前提是主要活動在 `[Activity()]` 屬性中缺少 `ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation`。

## <a name="page-navigation-events"></a>頁面導覽事件

類別上有兩個事件 [`Application`](xref:Xamarin.Forms.Application) ，可提供顯示和消失頁面的通知：

- [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)-頁面即將出現在螢幕上時引發。
- [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing)-頁面即將從螢幕消失時引發。

如果您想在畫面上出現頁面時追蹤它們，則可使用這些事件。

> [!NOTE]
> [`PageAppearing`](xref:Xamarin.Forms.Application.PageAppearing)和 [`PageDisappearing`](xref:Xamarin.Forms.Application.PageDisappearing) 事件會 [`Page`](xref:Xamarin.Forms.Page) 分別在和事件之後，從基類引發 [`Page.Appearing`](xref:Xamarin.Forms.Page.Appearing) [`Page.Disappearing`](xref:Xamarin.Forms.Page.Disappearing) 。

## <a name="modal-navigation-events"></a>強制回應導覽事件

類別上有四個事件 [`Application`](xref:Xamarin.Forms.Application) ，每個都有自己的事件引數，可讓您回應顯示和關閉的模式頁面：

- `ModalPushing` - 會在頁面被強制推送時引發。
- `ModalPushed` - 會在頁面被強制推送後引發。
- `ModalPopping` - 會在頁面被強制快顯時引發。
- `ModalPopped` - 會在頁面被強制快顯後引發。

> [!NOTE]
> `ModalPopping` 事件引數 (`ModalPoppingEventArgs` 型別) 包含 `Cancel` 屬性。 當 `Cancel` 設為 `true` 時，強制回應快顯已取消。
