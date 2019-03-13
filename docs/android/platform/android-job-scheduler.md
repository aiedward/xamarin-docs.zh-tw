---
title: Android 工作排程器
description: 本指南會討論如何使用 Android 工作排程器 API 的背景工作的排程。
ms.prod: xamarin
ms.assetid: 673BB8C3-C5CC-43EC-BA8F-758F15D986C9
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: c0f638afbf044a2e3e6f309839cb22137cf95912
ms.sourcegitcommit: 7eed80186e23e6aff3ddbbf7ce5cd1fa20af1365
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2018
ms.locfileid: "51527010"
---
# <a name="android-job-scheduler"></a>Android 工作排程器

_本指南將告訴您如何使用 Android 的工作排程器 API，也就是在執行 Android 5.0 (API level 21) 的 Android 裝置上可用的背景工作的排程和更新版本。_


## <a name="overview"></a>總覽 

將 Android 應用程式回應使用者的最佳方式之一是確保複雜或長時間執行的工作在背景中執行。 不過，很重要，背景工作不會造成負面影響的使用者經驗與裝置。 

比方說，背景工作可能分鐘輪詢一次網站每隔三或四個查詢的特定資料集的變更。 不過，它會造成災難性影響電池壽命，這似乎良性的。 應用程式重複會喚醒裝置、 提高至更高的電源狀態的 CPU、 收音機開啟電源，請網路要求，然後處理結果。 因為裝置不會立即會在關閉電源，並返回 低電源閒置狀態時，它會更糟。 效能不佳的排程背景工作可能會不小心保護裝置的不必要和多餘電力需求的狀態中。 這個看似無害的活動 （輪詢網站） 會使裝置在相對較短的一段時間無法使用。

Android 提供下列 Api 可協助在背景中執行工作，但本身不足夠的智慧型作業排程。 

* **[Intent 服務](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash;意圖服務適合用於執行工作，但它們提供無法排定工作。
* **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)**  &ndash;這些 Api 只允許排程，但提供沒辦法實際執行工作的工作。 此外，AlarmManager 只允許以時間為基礎的條件約束，這表示在特定時間，或經過一段時間之後，發出警示。 
* **[廣播接收器](~/android/app-fundamentals/broadcast-receivers.md)** &ndash; Android 應用程式可以設定來執行工作以回應系統事件或意圖的廣播的接收器。 不過，廣播的接收器不提供任何控制何時應執行作業。 Android 作業系統的變更也會限制當廣播的接收器是可行的或它們可以回應的工作種類。 

有兩個重要的功能，有效率地執行背景工作 (有時稱為_背景工作_或是_作業_):

1. **聰明的工作排程**&ndash;很重要，當應用程式負責在背景中的工作，它會以良好的公民。 在理想情況下，應用程式不應要求執行作業。 相反地，應用程式應指定作業可以執行，以及然後排程該工作會在符合條件時執行工作的作業系統必須符合的條件。 這可讓 Android 執行作業，以確保在裝置上的最高效率。 比方說，網路要求，可能會批次處理以發揮最大的負擔與網路有關的所有項目執行。
2. **封裝工作**&ndash;執行背景工作的程式碼應該封裝在個別的元件可以獨立使用者介面中執行，以及將相當容易重新排定工作無法完成時因為某些原因。

Android 工作排程器是內建 Android 作業系統可提供簡化的排程背景工作的 fluent API 的架構。  Android 工作排程器是由下列類型所組成：

* `Android.App.Job.JobScheduler`是一種系統服務，用來排程、 執行和必要時取消，代表 Android 應用程式的工作。
* `Android.App.Job.JobService`是抽象類別必須使用會在應用程式的主執行緒執行作業的邏輯擴充。 這表示`JobService`負責是以非同步方式執行工作的方式。
* `Android.App.Job.JobInfo`物件會保存引導 Android 時，工作應該執行的準則。

若要排程與 Android 的工作排程器工作，Xamarin.Android 應用程式必須封裝可擴充的類別中的程式碼`JobService`類別。 `JobService` 有三個可以呼叫作業的存留期間的生命週期方法：

* **bool （JobParameters 參數） OnStartJob** &ndash;藉由呼叫這個方法`JobScheduler`執行工作和應用程式的主執行緒上執行的。 它負責`JobService`來以非同步方式執行的工作並`true`剩餘，如果沒有工作或`false`如果工作已完成。
    
    當`JobScheduler`呼叫這個方法，它將會要求，並在作業期間保留從 Android wakelock。 當工作完成時，它負責`JobService`告訴`JobScheduler`呼叫此事實`JobFinished`（接下來所述） 的方法。

* **（JobParameters 參數，bool needsReschedule） JobFinished** &ndash;必須由呼叫這個方法`JobService`告訴`JobScheduler`中完成工作的。 如果`JobFinished`不會呼叫，`JobScheduler`將不會移除 wakelock，導致不必要的電池清空。 

* **bool （JobParameters 參數） OnStopJob** &ndash; Android 提前停止工作時，這會呼叫。 它應該傳回`true`如果應該重試準則 （詳述如下） 為基礎重新排程作業。

您可指定_條件約束_或是_觸發程序_，將控制作業時可以或應該執行。 比方說，就可以限制作業，使其只會執行當裝置正在充電或啟動的工作，當圖片。

本指南會討論詳述如何實作`JobService`類別，並將它與排程`JobScheduler`。

## <a name="requirements"></a>需求

Android 工作排程器需要 Android API level 21 (Android 5.0) 或更高版本。 

## <a name="using-the-android-job-scheduler"></a>使用 Android 工作排程器

有三個步驟，使用 Android JobScheduler API:

1. 實作 JobService 型別來封裝工作。
2. 使用`JobInfo.Builder`物件來建立`JobInfo`物件，將存放的準則`JobScheduler`来執行此作業。 
3. 作業使用排程`JobScheduler.Schedule`。

### <a name="implement-a-jobservice"></a>實作 JobService

Android 工作排程器程式庫所執行的所有工作都必須在此型別擴充`Android.App.Job.JobService`抽象類別。 建立`JobService`非常類似於建立`Service`Android 架構： 

1. 擴充`JobService`類別。
2. 裝飾具有子類別`ServiceAttribute`並設定`Name`封裝名稱和類別的名稱組成的字串參數 （請參閱下列範例）。
3. 設定`Permission`上的屬性`ServiceAttribute`字串`android.permission.BIND_JOB_SERVICE`。
4. 覆寫`OnStartJob`方法，加入程式碼來執行工作。 Android 會叫用這個方法來執行作業的應用程式的主執行緒上。 需要較久的幾毫秒，應該以避免封鎖應用程式的執行緒上執行的工作。
5. 工作完成時，`JobService`必須呼叫`JobFinished`方法。 這個方法是如何`JobService`告知`JobScheduler`工作已完成。 若要呼叫的失敗`JobFinished`將會導致`JobService`放在裝置上，縮短電池壽命的不必要的需求。 
6. 它是個不錯的主意，也會覆寫`OnStopJob`方法。 當作業正在關閉之前已完成，並提供 Android 便會呼叫此方法`JobService`得以正確地處置任何資源。 這個方法會傳回`true`如有必要重新排定工作，或`false`如果不是說不定重新執行工作。
   

下列程式碼是簡單的範例`JobService`應用程式，請使用 TPL 以非同步方式執行一些工作：

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

### <a name="creating-a-jobinfo-to-schedule-a-job"></a>建立排程作業 JobInfo

Xamarin.Android 應用程式進行具現化`JobService`直接管理，而是會通過`JobInfo`物件到`JobScheduler`。 `JobScheduler`會具現化要求`JobService`物件，排程及執行`JobService`中的中繼資料根據`JobInfo`。 A`JobInfo`物件必須包含下列資訊：

* **JobId** &ndash;這是第`int`值，用來識別工作以`JobScheduler`。 重複使用此值將會更新任何現有的作業。 值必須是唯一的應用程式。 
* **JobService** &ndash;這個參數是`ComponentName`明確識別型別，`JobScheduler`應該使用來執行作業。 
  

這個擴充方法示範如何建立`JobInfo.Builder`android `Context`，例如活動：

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

Android 工作排程器的強大功能之一是能夠控制作業的執行，或在什麼條件作業可能會執行。 下表描述的一些方法上`JobInfo.Builder`，可讓應用程式，以影響作業可以執行時：  


|  方法 | 描述   |
|---|---|
| `SetMinimumLatency`  | 指定執行工作之前，應該要觀察的延遲 （以毫秒為單位）。 |
| `SetOverridingDeadline`  | 宣告的作業都必須執行才能經過這個時間 （以毫秒為單位）。 |
| `SetRequiredNetworkType`  | 指定作業的網路需求。 |
| `SetRequiresBatteryNotLow` | 當裝置未向使用者顯示 「 電力偏低 」 警告，可能只會執行作業。 |
| `SetRequiresCharging` | 當電池在充電時，可能只會執行作業。 |
| `SetDeviceIdle` | 當裝置正在使用中時，會執行作業。 |
| `SetPeriodic` | 指定應定期執行作業。 |
| `SetPersisted` | 作業會在裝置重新啟動期間應該 perisist。 | 


`SetBackoffCriteria`提供一些指引長度`JobScheduler`應等待嘗試再次執行工作。 有兩個部分的輪詢準則： 應該使用毫秒 （30 秒的預設值） 和類型的撤退延遲 (有時稱為_輪詢原則_或_重試原則_). 兩個原則，都會封裝在`Android.App.Job.BackoffPolicy`列舉：

* `BackoffPolicy.Exponential` &ndash; 指數輪詢原則會以指數方式增加初始輪詢值之後每個失敗。 工作失敗，第一次程式庫會在等候之前先重新排定工作 – 也就是範例 30 秒內指定的初始間隔。 作業失敗時，第二次程式庫會等候至少 60 秒，然後再嘗試執行此作業。 第三個嘗試失敗之後，程式庫會等待 120 秒，以此類推。 這是預設值。
* `BackoffPolicy.Linear` &ndash; 此策略是線性的輪詢應該重新排定工作，可以執行設定的間隔 （直到成功）。 線性輪詢最適合針對必須儘速完成的工作或將快速自行解決問題。 

如需更多有關建立`JobInfo`物件，請閱讀[Google 的文件`JobInfo.Builder`類別](https://developer.android.com/reference/android/app/job/JobInfo.Builder.html)。

#### <a name="passing-parameters-to-a-job-via-the-jobinfo"></a>將參數傳遞至透過 JobInfo 作業

藉由建立的參數傳遞至作業`PersistableBundle`連同傳遞`Job.Builder.SetExtras`方法：

```csharp
var jobParameters = new PersistableBundle();
jobParameters.PutInt("LoopCount", 11);

var jobBuilder = this.CreateJobBuilderUsingJobId<DownloadJob>(1)
                     .SetExtras(jobParameters)
                     .Build();
```

`PersistableBundle`從存取`Android.App.Job.JobParameters.Extras`中的屬性`OnStartJob`方法`JobService`:

```csharp
public override bool OnStartJob(JobParameters jobParameters)
{
    var loopCount = jobParams.Extras.GetInt("LoopCount", 10);
    
    // rest of code omitted
} 
```

### <a name="scheduling-a-job"></a>排程工作

若要排程的作業，Xamarin.Android 應用程式會參考`JobScheduler`系統服務，並呼叫`JobScheduler.Schedule`方法`JobInfo`在上一個步驟中建立的物件。 `JobScheduler.Schedule` 會立即傳回並提供兩個整數值的其中一個：

* **JobScheduler.ResultSuccess** &ndash;作業順利排程。 
* **JobScheduler.ResultFailure** &ndash;無法排程工作。 這種情形通常因衝突`JobInfo`參數。

此程式碼是排程工作和通知使用者有排程嘗試的結果範例：

```csharp
var jobScheduler = (JobScheduler)GetSystemService(JobSchedulerService);
var scheduleResult = jobScheduler.Schedule(jobInfo);

if (JobScheduler.ResultSuccess == scheduleResult)
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_success, Snackbar.LengthShort);
}
else
{
    Snackbar.Make(FindViewById(Android.Resource.Id.Content), Resource.String.jobscheduled_failure, Snackbar.LengthShort);
}
```
 
### <a name="cancelling-a-job"></a>正在取消工作

它是可以取消已排程的所有作業或只是單一工作，使用`JobsScheduler.CancelAll()`方法或`JobScheduler.Cancel(jobId)`方法：

```csharp
// Cancel all jobs
jobScheduler.CancelAll(); 

// to cancel a job with jobID = 1
jobScheduler.Cancel(1)
```
  
## <a name="summary"></a>總結

本指南會討論如何使用 Android 工作排程器以聰明的方式在背景中執行的工作。 它討論如何將封裝執行的工作`JobService`以及如何使用`JobScheduler`排程工作，指定與條件`JobTrigger`和失敗應該如何處理包含`RetryStrategy`。

## <a name="related-links"></a>相關連結

- [智慧型工作排程](https://developer.android.com/topic/performance/scheduling.html)
- [JobScheduler API 參考](https://developer.android.com/reference/android/app/job/JobScheduler.html)
- [排程作業像 JobScheduler 與專業](https://medium.com/google-developers/scheduling-jobs-like-a-pro-with-jobscheduler-286ef8510129)
- [Android 使用電池及記憶體最佳化-Google I/O 2016 （影片）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
- [Android JobScheduler-René Ruppert-Xamarin University](https://www.youtube.com/watch?v=aSjBBPYjelE)
