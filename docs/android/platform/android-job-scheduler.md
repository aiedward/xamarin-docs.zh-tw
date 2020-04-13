---
title: Android 工作排程器
description: 本指南討論如何使用 Android 作業計劃程式 API 安排後台工作。
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/19/2018
ms.openlocfilehash: 10d2ae6ac35f02d75ef6e04a0531ec3f5dafd668
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "78291965"
---
# <a name="android-job-scheduler"></a>Android 工作排程器

_本指南討論如何使用 Android 作業計劃程式 API 安排後台工作,該 API 在運行 Android 5.0(API 級別 21)及以上的 Android 設備上提供。_

## <a name="overview"></a>概觀 

使 Android 應用程式對使用者回應的最佳方式之一是確保在後台執行複雜或長時間運行的工作。 但是,後台工作不會對使用者使用設備的體驗產生負面影響,這一點很重要。 

例如,後台作業可能每隔三或四分鐘輪詢一個網站,以查詢對特定數據集的更改。 這似乎是良性的,但它會對電池壽命產生災難性的影響。 應用程式將反覆喚醒設備,將 CPU 提升到更高的電源狀態,打開收音機電源,發出網路請求,然後處理結果。 情況變得更糟,因為設備不會立即斷電並返回到低功耗空閒狀態。 計劃不當的背景工作可能會無意中使設備處於不必要和過多的電源要求的狀態。 這種看似無辜的活動(輪詢網站)將使設備在相對較短的時間內無法使用。

Android 提供以下 API 來説明在後台執行工作,但僅此本身,它們不足以進行智慧作業調度。 

- **[意向服務](~/android/app-fundamentals/services/creating-a-service/intent-services.md)**&ndash;意向服務非常適合執行工作,但它們無法提供安排工作的方法。
- **[警報管理員](https://developer.android.com/reference/android/app/AlarmManager.html)**&ndash;這些 API 只允許計畫工作,但無法實際執行該工作。 此外,警報管理器只允許基於時間的約束,這意味著在一定時間或經過一段時間后發出警報。 
- **[廣播接收器](~/android/app-fundamentals/broadcast-receivers.md)**&ndash;Android 應用程式可以設定廣播接收器以執行回應系統範圍的事件或意圖的工作。 但是,廣播接收器不提供對作業何時運行的任何控制。 此外,Android 作業系統中的更改將限制廣播接收器何時工作,或限制它們可以回應的工作類型。 

高效執行後台工作有兩個關鍵功能(有時稱為_後台作業_或_作業_):

1. **明智地安排工作**&ndash;當應用程式在後台工作時,重要的是它作為一個好公民這樣做。 理想情況下,應用程式不應要求運行作業。 相反,應用程式應指定作業可以運行時必須滿足的條件,然後使用作業系統安排該作業,該作業將在滿足條件時執行該作業。 這允許 Android 運行作業,以確保設備上的最大效率。 例如,可以批處理網路請求以同時運行所有請求,以最大限度地利用網路所涉及的開銷。
2. **封裝工作**&ndash;執行後台工作的代碼應封裝在獨立元件中,該元件可以獨立於使用者介面運行,如果工作由於某種原因無法完成,則相對容易重新計劃。

Android 作業計劃程式是內置於 Android 作業系統的框架,它提供了流暢的 API 以簡化日程安排後台工作。  Android 作業計劃程式由以下類型組成:

- 是`Android.App.Job.JobScheduler`一種系統服務,用於代表 Android 應用程式安排、執行並在必要時取消作業。
- 是`Android.App.Job.JobService`一個抽象類,必須擴展與將在應用程式的主線程上運行作業的邏輯。 這意味著,`JobService`負責如何非同步地執行工作。
- 當`Android.App.Job.JobInfo`作業應運行時,物件保留指導 Android 的條件。

要安排使用 Android 作業計劃程式,Xamarin.Android 應用程式必須將代碼封裝在`JobService`擴充類的 類別中。 `JobService`有三種生命週期方法,可以在作業的生存期內調用:

- **布爾 OnStartJob(作業參數參數)**&ndash;此方法由`JobScheduler`調用 以執行工作,並在應用程式的主線程上運行。 如果剩餘工作或`JobService``true``false`工作已完成,則由 is 以非同步方式執行工作並返回。
    
    調用此方法`JobScheduler`時,它將在作業期間請求並保留來自 Android 的喚醒鎖。 作業完成後,由的責任`JobService`通過調`JobScheduler``JobFinished`用 方法(下稱描述)來告知此事實。

- **作業完成(作業參數參數,布爾需要重新計畫)**&ndash;此方法必須由調`JobService`用`JobScheduler`以告訴 已完成的工作。 如果未`JobFinished`調用,`JobScheduler`則不會拆下喚醒鎖,從而導致不必要的電池耗盡。 

- 當 Android 過早停止作業時,將調用 **「作業停止作業」(作業參數參數)。** &ndash; 如果應根據`true`重試條件重新安排作業(下面詳細討論),則應返回該作業。

可以指定約束或觸發器,這些_約束_或_觸發器_將控制作業何時可以或應運行。 例如,可以約束作業,使其僅在設備充電時運行,或在拍攝照片時啟動作業。

本指南將詳細討論如何實現類`JobService`,並安排它與`JobScheduler`。

## <a name="requirements"></a>需求

Android 作業計劃程式需要 Android API 級別 21(Android 5.0)或更高版本。 

## <a name="using-the-android-job-scheduler"></a>使用 Android 作業計劃程式

使用 Android 作業計劃 API 有三個步驟:

1. 實現作業服務類型來封裝工作。
2. 使用物件`JobInfo.Builder`創建將`JobInfo``JobScheduler`保存 執行作業的條件的物件。 
3. 使用`JobScheduler.Schedule`計劃作業。

### <a name="implement-a-jobservice"></a>實施作業服務

Android 作業計劃程式庫執行的所有工作都必須以`Android.App.Job.JobService`擴展 抽象類的類型完成。 建立`JobService`與使用 Android`Service`架構 建立非常相似: 

1. 擴充`JobService`類 。
2. 使用 來修飾子`ServiceAttribute`類`Name`,並將 參數設置為由包名稱和類的名稱組成的字串(請參閱以下示例)。
3. 設定`Permission`屬性`ServiceAttribute`設定為字串`android.permission.BIND_JOB_SERVICE`。
4. 重寫`OnStartJob`方法,添加代碼以執行該工作。 Android將在應用程式的主線程上調用此方法來運行作業。 在線程上執行幾毫秒的工作,以避免阻塞應用程式。
5. 完成工作後,`JobService`必須調`JobFinished`用 方法。 此方法是告訴`JobScheduler``JobService`工作 完成的方式。 無法調用`JobFinished`將導致對`JobService`設備 提出不必要的要求,從而縮短電池使用時間。 
6. 最好也重寫該方法`OnStopJob`。 當作業在完成之前關閉時,Android 會調用此方法,併為`JobService`提供 正確處置任何資源的機會。 如果需要重新計畫作業`true`,`false`或者不需要重新運行作業,此方法應返回。

以下代碼是應用程式最簡單的`JobService`範例,使用 TPL 同步執行某些工作:

```csharp
[Service(Name = "com.xamarin.samples.downloadscheduler.DownloadJob", 
         Permission = "android.permission.BIND_JOB_SERVICE")]
public class DownloadJob : JobService
{
    public override bool OnStartJob(JobParameters jobParams)
    {            
        Task.Run(() =>
        {
            // Work is happening asynchronously
                      
            // Have to tell the JobScheduler the work is done. 
            JobFinished(jobParams, false);
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(JobParameters jobParams)
    {
        // we don't want to reschedule the job if it is stopped or cancelled.
        return false; 
    }
}
```

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>建立工作資訊以安排工作

Xamarin.Android 應用程式不會直接實例化`JobService`,`JobInfo`而是將`JobScheduler`物件傳遞給 。 `JobScheduler`將實體化要求`JobService`的物件,`JobService`根據中的中繼資料調度與`JobInfo`執行 。 物件`JobInfo`必須包含以下資訊:

- **JobId**&ndash;這是`int`一個用於識別 工作`JobScheduler`的值 。 重用此值將更新任何現有作業。 該值對於應用程序來說必須是唯一的。 
- **JobService**&ndash;此參數`ComponentName`是 顯`JobScheduler`式標識 應用於運行作業的類型。 

此擴充方法展示`JobInfo.Builder`如何`Context`使用 Android 建立 ,例如活動:

```csharp
public static class JobSchedulerHelpers
{
    public static JobInfo.Builder CreateJobBuilderUsingJobId<T>(this Context context, int jobId) where T:JobService
    {
        var javaClass = Java.Lang.Class.FromType(typeof(T));
        var componentName = new ComponentName(context, javaClass);
        return new JobInfo.Builder(jobId, componentName);
    }
}

// Sample usage - creates a JobBuilder for a DownloadJob and sets the Job ID to 1.
var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1);

var jobInfo = jobBuilder.Build();  // creates a JobInfo object.
```

Android 作業計劃程式的一個強大功能是能夠控制作業何時運行或作業可能在什麼條件下運行。 下表描述了允許`JobInfo.Builder`應用在作業可以運行時影響的一些方法:  

|  方法 | 描述   |
|---|---|
| `SetMinimumLatency`  | 指定在運行作業之前應觀察到的延遲(以毫秒為單位)。 |
| `SetOverridingDeadline`  | 聲明作業必須在此時間之前運行(以毫秒為單位)已過。 |
| `SetRequiredNetworkType`  | 指定作業的網路要求。 |
| `SetRequiresBatteryNotLow` | 僅當設備未向用戶顯示"電池電量不足"警告時,作業才能運行。 |
| `SetRequiresCharging` | 僅當電池正在充電時,作業才能運行。 |
| `SetDeviceIdle` | 當設備忙時,作業將運行。 |
| `SetPeriodic` | 指定應定期運行作業。 |
| `SetPersisted` | 作業應跨設備重新啟動。 | 

提供了`SetBackoffCriteria`一些指導,說明在`JobScheduler`嘗試再次運行作業之前應等待多長時間。 回退條件有兩個部分:毫秒的延遲(預設值 30 秒)和應使用的退退類型(有時稱為_回退策略_或_重試策略_)。 這兩個策略封裝在枚舉`Android.App.Job.BackoffPolicy`中:

- `BackoffPolicy.Exponential`&ndash;指數級回退策略將在每次失敗后以指數級方式增加初始回退值。 工作首次失敗時,庫將等待指定的初始間隔,然後再重新安排作業, 例如 30 秒。 第二次作業失敗時,庫將等待至少60秒,然後再嘗試運行該作業。 第三次嘗試失敗后,庫將等待 120 秒,等等。 這是預設值。
- `BackoffPolicy.Linear`&ndash;此策略是一個線性回退,應重新計畫作業以設定的時間間隔運行(直到它成功)。 線性回退最適合必須儘快完成的工作或能夠快速解決自己的問題。 

有關建立`JobInfo`物件的更多詳細資訊,請閱讀 Google[的`JobInfo.Builder`班級文件](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html)。

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>透過工作資訊將參數傳遞到工作

參數透過建立式`PersistableBundle``Job.Builder.SetExtras`方法一起傳遞的傳遞給作業:

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

`PersistableBundle`從屬性`Android.App.Job.JobParameters.Extras``OnStartJob`存取`JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>安排工作

為了安排作業,Xamarin.Android 應用程式將獲得`JobScheduler`對 系統服務的`JobScheduler.Schedule`引用, 並調用該方法與在上`JobInfo`一步中 創建的物件。 `JobScheduler.Schedule`將立即返回兩個整數值之一:

- **作業計劃.結果成功**&ndash;作業已成功計劃。 
- **作業計劃計劃.結果失敗**&ndash;作業無法計劃。 這通常是由衝突`JobInfo`參數引起的。

此代碼是計畫工作並通知使用者計畫嘗試結果的範例:

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
    snackBar.Show();
}
else
{
    var snackBar = Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
    snackBar.Show();
}
```

### <a name="cancelling-a-job"></a>取消工作

可以取消已計劃的所有作業,或者僅取消使用`JobsScheduler.CancelAll()`方法或方法`JobScheduler.Cancel(jobId)`的 單個作業:

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>總結

本指南討論了如何使用 Android 作業計劃程式在後台智慧地執行工作。 它討論了如何封裝要作為`JobService`執行 的工作,以及`JobScheduler`如何使用 來安排該工作,指定`JobTrigger`具有的條件 ,以及如何使用`RetryStrategy`來處理失敗。

## <a name="related-links"></a>相關連結

- [智慧作業計劃](https://developer.android.com/topic/performance/scheduling.html)
- [工作計劃 API 引用](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [使用作業計劃表安排作業,如專業作業](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android 電池和記憶體優化 - Google I/O 2016 (視頻)](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [安卓工作計劃 - 勒內·魯珀特](https://www.youtube.com/watch?v=aSjBBPYjelE)
