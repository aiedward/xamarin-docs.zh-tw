---
title: Firebase 工作發送器
description: 本指南將討論如何使用 Google 的 Firebase 作業發送器程式庫來排程背景工作。
ms.prod: xamarin
ms.assetid: 3DB9C7A3-D351-481D-90C5-BEC25D1B9910
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 280fe11f935db0a364f3342b22bb9544cdda1e6d
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/10/2020
ms.locfileid: "73020238"
---
# <a name="firebase-job-dispatcher"></a>Firebase 工作發送器

_本指南將討論如何使用 Google 的 Firebase 作業發送器程式庫來排程背景工作。_

## <a name="overview"></a>總覽

讓 Android 應用程式回應使用者的最佳方式之一，是確保在背景執行複雜或長時間的工作。 不過，背景工作不會對裝置的使用者體驗造成負面影響。 

例如，背景工作可能會每隔三或四分鐘輪詢一次網站，以查詢特定資料集的變更。 這似乎無害，但會對電池壽命造成災難性的影響。 應用程式會重複喚醒裝置、將 CPU 提升為較高的電源狀態、開啟無線電、進行網路要求，然後處理結果。 因為裝置不會立即關閉電源並回到低電源閒置狀態，所以會變得更糟。 排程不良的背景工作可能會不小心讓裝置處於不必要和過度的電源需求的狀態。 這個看似無害的活動（輪詢網站）將在相當短的時間內，使裝置無法使用。

Android 提供下列 Api 來協助在背景中執行工作，但本身並不足以進行智慧型作業排程。 

- **[意圖服務](~/android/app-fundamentals/services/creating-a-service/intent-services.md)** &ndash; 意圖服務很適合用來執行工作，不過，它們不提供任何方式來排程工作。
- **[AlarmManager](https://developer.android.com/reference/android/app/AlarmManager.html)** &ndash; 這些 api 只允許排程工作，但不提供實際執行工作的方式。 此外，AlarmManager 只允許以時間為基礎的條件約束，這表示在特定時間或經過一段特定時間之後引發警示。 
- **[JobScheduler](https://developer.android.com/reference/android/app/job/JobScheduler.html)** &ndash; JobSchedule 是絕佳的 API，可與作業系統搭配運作來排程作業。 不過，它只適用于以 API 層級21或更高版本為目標的 Android 應用程式。 
- &ndash; Android 應用程式的 **[廣播接收器](~/android/app-fundamentals/broadcast-receivers.md)** ，可以設定廣播接收器來執行工作，以回應全系統的事件或意圖。 不過，廣播接收器不會對應執行作業的時機提供任何控制。 Android 作業系統中的變更也會限制廣播接收者的運作時間，或其可以回應的工作類型。 

有兩個主要功能可有效率地執行背景工作（有時稱為_背景作業_或_作業_）：

1. 以**智慧方式排程工作**&ndash; 在應用程式于背景執行工作時，請務必將其做為良好公民。 在理想情況下，應用程式不應該要求執行作業。 相反地，應用程式應該指定作業可以執行時必須符合的條件，然後排程符合條件時要執行的工作。 這可讓 Android 以智慧方式執行工作。 例如，網路要求可能會批次處理成同時執行，以充分利用網路所涉及的額外負荷。
2. **封裝工作**&ndash; 要執行背景工作的程式碼，應該封裝在可獨立于使用者介面之外執行的離散元件中，而且如果工作因某些原因而無法完成，則會相對容易重新排定。

Firebase 作業發送器是 Google 的程式庫，提供 Fluent API 來簡化排程背景工作。 其目的是要取代 Google Cloud Manager。 Firebase 作業發送器包含下列 Api：

- `Firebase.JobDispatcher.JobService` 是抽象類別，必須使用將在背景工作中執行的邏輯進行擴充。
- `Firebase.JobDispatcher.JobTrigger` 會宣告作業應該啟動的時間。 這通常是以時間視窗表示，例如，在開始作業前至少等候30秒，但在5分鐘內執行作業。
- `Firebase.JobDispatcher.RetryStrategy` 包含當作業無法正常執行時應執行之動作的相關資訊。 重試策略會指定嘗試重新執行工作之前要等待的時間長度。 
- `Firebase.JobDispatcher.Constraint` 是一個選擇性的值，它會描述必須符合才能執行作業的條件，例如裝置處於非計費的網路或收費。
- `Firebase.JobDispatcher.Job` 是一個 API，可將先前的 Api 整合至 `JobDispatcher`所能排程的工作單位。 `Job.Builder` 類別是用來具現化 `Job`。
- `Firebase.JobDispatcher.JobDispatcher` 會使用前三個 Api 來排程作業系統的工作，並在必要時提供取消作業的方法。

若要排程 Firebase 作業發送器的工作，Xamarin. Android 應用程式必須將程式碼封裝在擴充 `JobService` 類別的類型中。 `JobService` 有三個生命週期方法，可在作業的存留期間呼叫：

- **`bool OnStartJob(IJobParameters parameters)`** &ndash; 此方法會發生工作，且應一律加以執行。 它會在主執行緒上執行。 如果剩餘工作，這個方法會傳回 `true`，如果工作已完成，則會 `false`。 
- **`bool OnStopJob(IJobParameters parameters)`** &ndash; 當作業因某些原因而停止時，就會呼叫此程式。 如果應該重新排程作業以供稍後重新排程，則應該傳回 `true`。
- **`JobFinished(IJobParameters parameters, bool needsReschedule)`** &ndash; 當 `JobService` 完成任何非同步工作時，會呼叫這個方法。 

若要排程作業，應用程式會具現化 `JobDispatcher` 物件。 然後，會使用 `Job.Builder` 來建立 `Job` 物件，並將其提供給 `JobDispatcher`，這將會嘗試並排程要執行的作業。

本指南將討論如何將 Firebase 作業發送器新增至 Xamarin Android 應用程式，並使用它來排程背景工作。

## <a name="requirements"></a>需求

Firebase 作業發送器需要 Android API 層級9或更高版本。 Firebase 作業發送器程式庫依賴 Google Play Services 所提供的部分元件;裝置必須安裝 Google Play Services。

## <a name="using-the-firebase-job-dispatcher-library-in-xamarinandroid"></a>在 Xamarin 中使用 Firebase 作業發送器程式庫

若要開始使用 Firebase 作業發送器，請先將[Firebase. JobDispatcher NuGet 封裝](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)新增至 Xamarin 專案。 在 NuGet 套件管理員中搜尋**Firebase. JobDispatcher**套件（仍在發行前版本中）。

新增 Firebase 作業發送器程式庫之後，請建立 `JobService` 類別，然後將它排程為與 `FirebaseJobDispatcher`的實例一起執行。

### <a name="creating-a-jobservice"></a>建立 JobService

Firebase 作業發送器程式庫所執行的所有工作都必須在擴充 `Firebase.JobDispatcher.JobService` 抽象類別的類型中完成。 建立 `JobService` 與使用 Android framework 建立 `Service` 非常類似： 

1. 擴充 `JobService` 類別
2. 使用 `ServiceAttribute`裝飾子類別。 雖然不是絕對必要，但建議您明確設定 `Name` 參數，以協助進行 `JobService`的調試。 
3. 加入 `IntentFilter`，在**androidmanifest.xml**中宣告 `JobService`。 這也會協助 Firebase 作業發送器程式庫找出並叫用 `JobService`。

下列程式碼是應用程式最簡單 `JobService` 的範例，使用 TPL 以非同步方式執行一些工作：

```csharp
[Service(Name = "com.xamarin.fjdtestapp.DemoJob")]
[IntentFilter(new[] {FirebaseJobServiceIntent.Action})]
public class DemoJob : JobService
{
    static readonly string TAG = "X:DemoService";

    public override bool OnStartJob(IJobParameters jobParameters)
    {
        Task.Run(() =>
        {
            // Work is happening asynchronously (code omitted)
                       
        });

        // Return true because of the asynchronous work
        return true;  
    }

    public override bool OnStopJob(IJobParameters jobParameters)
    {
        Log.Debug(TAG, "DemoJob::OnStartJob");
        // nothing to do.
        return false;
    }
}
```

### <a name="creating-a-firebasejobdispatcher"></a>建立 FirebaseJobDispatcher

在可以排程任何工作之前，必須先建立 `Firebase.JobDispatcher.FirebaseJobDispatcher` 物件。 `FirebaseJobDispatcher` 負責排程 `JobService`。 下列程式碼片段是建立 `FirebaseJobDispatcher`實例的一種方式： 

 ```csharp
// This is the "Java" way to create a FirebaseJobDispatcher object
IDriver driver = new GooglePlayDriver(context);
FirebaseJobDispatcher dispatcher = new FirebaseJobDispatcher(driver);
```

在先前的程式碼片段中，`GooglePlayDriver` 是可協助 `FirebaseJobDispatcher` 與裝置上 Google Play Services 中的一些排程 Api 互動的類別。 參數 `context` 是任何 Android `Context`，例如活動。 目前 `GooglePlayDriver` 是 Firebase 作業發送器程式庫中唯一的 `IDriver` 實作為。 

Firebase 作業發送器的 Xamarin. Android 系結提供了從 `Context`建立 `FirebaseJobDispatcher` 的擴充方法： 

```csharp
FirebaseJobDispatcher dispatcher = context.CreateJobDispatcher();
```

一旦 `FirebaseJobDispatcher` 具現化之後，就可以建立 `Job`，然後在 `JobService` 類別中執行程式碼。 `Job` 是由 `Job.Builder` 物件所建立，並將在下一節中討論。

### <a name="creating-a-firebasejobdispatcherjob-with-the-jobbuilder"></a>使用作業建立 JobDispatcher 作業的 Firebase

`Firebase.JobDispatcher.Job` 類別負責封裝執行 `JobService`所需的中繼資料。 `Job` 包含作業可以執行之前必須符合的任何條件約束、如果 `Job` 重複出現，或任何會導致執行作業的觸發程式等資訊。  `Job` 必須要有一個_標記_（可識別 `FirebaseJobDispatcher`作業的唯一字串）和應該執行之 `JobService` 的型別。 當執行作業時，Firebase 作業發送器會具現化 `JobService`。  `Job` 是使用 `Firebase.JobDispatcher.Job.JobBuilder` 類別的實例所建立。 

下列程式碼片段是如何使用 Xamarin. Android 系結建立 `Job` 的最簡單範例：

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .Build();
```

`Job.Builder` 將針對作業的輸入值執行一些基本驗證檢查。 如果 `Job.Builder` 無法建立 `Job`，則會擲回例外狀況。  `Job.Builder` 將會建立具有下列預設值的 `Job`：

- `Job`的_存留期_（排程執行的時間長度）只有在裝置重新開機後才會重新開機 &ndash; 完成 `Job` 遺失為止。
- `Job` 不會週期性 &ndash; 只會執行一次。
- 系統會將 `Job` 排程為儘快執行。
- `Job` 的預設重試策略是使用_指數_輪詢（如需更多詳細資料，請見 <<c2>設定 RetryStrategy一節）

### <a name="scheduling-a-job"></a>排程工作

建立 `Job`之後，必須使用 `FirebaseJobDispatcher` 進行排程，才能執行該工作。 排程 `Job`的方法有兩種：

```csharp
// This will throw an exception if there was a problem scheduling the job
dispatcher.MustSchedule(myJob);

// This method will not throw an exception; an integer result value is returned
int scheduleResult = dispatcher.Schedule(myJob);
```

`FirebaseJobDispatcher.Schedule` 傳回的值將會是下列其中一個整數值：

- 已成功排程 `Job` &ndash; `FirebaseJobDispatcher.ScheduleResultSuccess`。
- `FirebaseJobDispatcher.ScheduleResultUnknownError` &ndash; 發生導致 `Job` 無法排程的未知問題。
- `FirebaseJobDispatcher.ScheduleResultNoDriverAvailable` &ndash; 使用了不正確 `IDriver`，或 `IDriver` 無法使用。 
- `FirebaseJobDispatcher.ScheduleResultUnsupportedTrigger` &ndash; 不支援 `Trigger`。
- `FirebaseJobDispatcher.ScheduleResultBadService` &ndash; 服務未正確設定或無法使用。

### <a name="configuring-a-job"></a>設定作業

您可以自訂作業。 如何自訂作業的範例包括下列各項：

- 將[參數傳遞至 &ndash; `Job` 的作業](#Passing_Parameters_to_a_Job)，可能需要額外的值來執行其工作，例如下載檔案。
- [設定條件約束](#Setting_Constraints)&ndash; 在符合特定條件時，可能只需要執行作業。 例如，只有在裝置充電時才執行 `Job`。 
- [指定應該](#Setting_Job_Triggers)在 Firebase 作業發送器 &ndash; 執行 `Job` 的時機，讓應用程式指定應該執行作業的時間。  
- 宣告[失敗作業的重試策略](#Setting_a_RetryStrategy)&ndash;_重試策略_會針對無法完成的 `Jobs` 提供如何處理的 `FirebaseJobDispatcher` 指引。 

下列各節將詳細討論這些主題。

<a name="Passing_Parameters_to_a_Job" />

#### <a name="passing-parameters-to-a-job"></a>將參數傳遞至作業

藉由建立與 `Job.Builder.SetExtras` 方法一起傳遞的 `Bundle`，將參數傳遞至作業：

```csharp
Bundle jobParameters = new Bundle();
jobParameters.PutInt(FibonacciCalculatorJob.FibonacciPositionKey, 25);

Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetExtras(jobParameters)
                      .Build();

```

`Bundle` 是從 `OnStartJob` 方法上的 `IJobParameters.Extras` 屬性存取：

```csharp
public override bool OnStartJob(IJobParameters jobParameters)
{
    int position = jobParameters.Extras.GetInt(FibonacciPositionKey, DEFAULT_VALUE);
    
    // rest of code omitted
} 
```

<a name="Setting_Constraints" />

#### <a name="setting-constraints"></a>設定條件約束

條件約束有助於降低裝置上的成本或電池耗盡。 `Firebase.JobDispatcher.Constraint` 類別會將這些條件約束定義為整數值：

- `Constraint.OnUnmeteredNetwork` &ndash; 只會在裝置連線到未按時間的網路時執行作業。 這有助於防止使用者產生資料費用。
- `Constraint.OnAnyNetwork` &ndash; 在裝置所連線的任何網路上執行作業。 如果與 `Constraint.OnUnmeteredNetwork`一起指定，此值會優先使用。
- `Constraint.DeviceCharging` &ndash; 只有在裝置充電時才執行作業。

條件約束是使用 `Job.Builder.SetConstraint` 方法所設定： 

```csharp
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetConstraint(Constraint.DeviceCharging)
                      .Build();
```

<a name="Setting_Job_Triggers" />

`JobTrigger` 提供有關作業何時應該啟動的作業系統指引。 `JobTrigger` 有一個_執行視窗_，可定義應該執行 `Job` 的排程時間。 [執行] 視窗具有 [_開始] 視窗_值和 [_結束] 視窗_值。 [開始] 視窗是裝置執行作業前應等候的秒數，而 [結束時間] 視窗值是執行 `Job`前等待的最大秒數。 

`JobTrigger` 可以使用 `Firebase.Jobdispatcher.Trigger.ExecutionWindow` 方法來建立。  例如 `Trigger.ExecutionWindow(15,60)` 表示作業應該在排程的15到60秒之間執行。 `Job.Builder.SetTrigger` 方法是用來 

```csharp
JobTrigger myTrigger = Trigger.ExecutionWindow(15,60);
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetTrigger(myTrigger)
                      .Build();
```

作業的預設 `JobTrigger` 是以 `Trigger.Now`值表示，這會指定在排程後儘快執行工作。

<a name="Setting_a_RetryStrategy" />

#### <a name="setting-a-retrystrategy"></a>設定 RetryStrategy

`Firebase.JobDispatcher.RetryStrategy` 可用來指定裝置嘗試重新執行失敗的作業之前，應該使用的延遲程度。 `RetryStrategy` 具有_原則_，其會定義將用來重新排程失敗作業的時間基底演算法，以及指定應該排定工作之視窗的執行視窗。 此重新_排定時間範圍_是由兩個值所定義。 第一個值是重新排程作業前等待的秒數（_初始_輪詢值），而第二個數字是作業必須執行的最大秒數（_最大_的輪詢值）。 

這兩種類型的重試原則是由下列 int 值所識別：

- `RetryStrategy.RetryPolicyExponential` &ndash;_指數_輪詢原則會在每次失敗後，以指數方式增加初始輪詢值。 第一次作業失敗時，程式庫會等待重新排程作業之前指定的 _initial 間隔，&ndash; 範例30秒。 第二次作業失敗時，程式庫會等待至少60秒，然後才嘗試執行此作業。 第三次嘗試失敗之後，程式庫會等候120秒，依此類推。 Firebase 作業發送器程式庫的預設 `RetryStrategy` 會以 `RetryStrategy.DefaultExponential` 物件表示。 它的初始輪詢為30秒，而最大輪詢為3600秒。
- `RetryStrategy.RetryPolicyLinear` &ndash; 此策略是一種_線性_輪詢，應將作業重新排程為在設定的間隔執行（直到成功為止）。 線性輪詢最適用于必須儘快完成的工作，或可快速解決的問題。 Firebase 作業發送器程式庫會定義一個 `RetryStrategy.DefaultLinear`，其中的重新排定時間範圍至少為30秒，最多3600秒。

您可以使用 `FirebaseJobDispatcher.NewRetryStrategy` 方法來定義自訂的 `RetryStrategy`。 它接受三個參數：

1. `int policy` &ndash;_原則_是先前 `RetryStrategy` 的值之一、`RetryStrategy.RetryPolicyLinear`或 `RetryStrategy.RetryPolicyExponential`。
2. `int initialBackoffSeconds` &ndash;_初始_輪詢是在嘗試重新執行作業之前所需的延遲（以秒為單位）。 這個的預設值是30秒。 
3. `int maximumBackoffSeconds` &ndash; 「_最大_輪詢」值會宣告在嘗試再次執行作業之前，要延遲的最大秒數。 預設值為3600秒。 

```csharp
RetryStrategy retry = dispatcher.NewRetryStrategy(RetryStrategy.RetryPolicyLinear, initialBackoffSeconds, maximumBackoffSet);

// Create a Job and set the RetryStrategy via the Job.Builder
Job myJob = dispatcher.NewJobBuilder()
                      .SetService<DemoJob>("demo-job-tag")
                      .SetRetryStrategy(retry)
                      .Build();
```

### <a name="cancelling-a-job"></a>取消作業

您可以取消已排程的所有作業，或僅使用 `FirebaseJobDispatcher.CancelAll()` 方法或 `FirebaseJobDispatcher.Cancel(string)` 方法的單一作業：

```csharp
int cancelResult = dispatcher.CancelAll(); 

// to cancel a single job:

int cancelResult = dispatcher.Cancel("unique-tag-for-job");
```

任一種方法都會傳回整數值：

- 已成功取消作業 &ndash; `FirebaseJobDispatcher.CancelResultSuccess`。
- `FirebaseJobDispatcher.CancelResultUnknownError` &ndash; 錯誤，導致無法取消作業。
- `FirebaseJobDispatcher.CancelResult.NoDriverAvailable` &ndash; `FirebaseJobDispatcher` 無法取消作業，因為沒有有效的 `IDriver` 可用。

## <a name="summary"></a>總結

本指南將討論如何使用 Firebase 作業發送器，以智慧方式在背景中執行工作。 它討論了如何封裝要當做 `JobService` 執行的工作，以及如何使用 `FirebaseJobDispatcher` 來排程該工作、指定 `JobTrigger` 的準則，以及如何使用 `RetryStrategy`來處理失敗。

## <a name="related-links"></a>相關連結

- [NuGet 上的 Firebase JobDispatcher。](https://www.nuget.org/packages/Xamarin.Firebase.JobDispatcher)
- [firebase-GitHub 上的發送器](https://github.com/firebase/firebase-jobdispatcher-android)
- [Firebase. JobDispatcher 系結](https://github.com/xamarin/XamarinComponents/tree/master/Android/FirebaseJobDispatcher)
- [智慧型作業-排程](https://developer.android.com/topic/performance/scheduling.html)
- [Android 電池和記憶體優化-Google i/o 2016 （影片）](https://www.youtube.com/watch?v=VC2Hlb22mZM&feature=youtu.be)
