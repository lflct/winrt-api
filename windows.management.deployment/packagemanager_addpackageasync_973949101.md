---
-api-id: M:Windows.Management.Deployment.PackageManager.AddPackageAsync(Windows.Foundation.Uri,Windows.Foundation.Collections.IIterable{Windows.Foundation.Uri},Windows.Management.Deployment.DeploymentOptions)
-api-type: winrt method
---

<!-- Method syntax
public Windows.Foundation.IAsyncOperationWithProgress<Windows.Management.Deployment.DeploymentResult, Windows.Management.Deployment.DeploymentProgress> AddPackageAsync(Windows.Foundation.Uri packageUri, Windows.Foundation.Collections.IIterable<Windows.Foundation.Uri> dependencyPackageUris, Windows.Management.Deployment.DeploymentOptions deploymentOptions)
-->

# Windows.Management.Deployment.PackageManager.AddPackageAsync

## -description
Adds a package (the main package) and its dependency packages for the current user, using the specified deployment options.

## -parameters
### -param packageUri
The Uri of the package to add. The URI must follow the file URI scheme (file://) since the only supported URI schemes are local file paths and local network paths.

### -param dependencyPackageUris
The Uris of the dependency packages to add. If there are no dependency packages or if the dependency packages are already registered, this parameter can be null.

### -param deploymentOptions
A bitwise combination of enumeration values from the [DeploymentOptions](deploymentoptions.md) enumeration. **ForceApplicationShutdown** and **None** are the only valid options for this method. Specifying any other option results in an E_INVALIDARG return value.

## -returns
The status of the deployment request. The [DeploymentResult](deploymentresult.md) contains the final returned value of the deployment operation, once it is completed. The [DeploymentProgress](deploymentprogress.md) can be used to obtain the percentage of completion over the entire course of the deployment operation.

## -remarks

## -examples
The following example uses the [PackageManager.AddPackageAsync](packagemanager_addpackageasync.md) method to install a package that has no dependencies or whose dependencies are already installed. Note that the path of the main package is passed as an argument in the example. [AddPackageAsync](packagemanager_addpackageasync.md) returns an object that can be used to manage the asynchronous operation. The example uses the [Completed](../windows.foundation/iasyncoperationwithprogress_2_completed.md) property to set the [delegate](../windows.foundation/asyncoperationwithprogresscompletedhandler_2.md) and checks the [Status](../windows.foundation/iasyncinfo_status.md) property to determine the status of the deployment operation. If the status is **Error**, the example calls the [GetResults](../windows.foundation/iasyncoperationwithprogress_2_getresults.md) method to get additional error information.

```csharp
using Windows.Foundation;
using Windows.Management.Deployment;

public static int Main(string[] args)
{
    string inputPackageUri = args[0];
    int returnValue=0;

    Uri packageUri = new Uri(inputPackageUri);            

    PackageManager packageManager = new PackageManager();

    IAsyncOperationWithProgress<DeploymentResult, DeploymentProgress> deploymentOperation = 
        packageManager.AddPackageAsync(
            packageUri, 
            null, 
            DeploymentOptions.None);

    // This event is signaled when the operation completes
    ManualResetEvent opCompletedEvent = new ManualResetEvent(false); 

    // Define the delegate using a statement lambda
    deploymentOperation.Completed = (depProgress, status) => { opCompletedEvent.Set(); };

    // Wait until the operation completes
    opCompletedEvent.WaitOne();

    // Check the status of the operation
    if (deploymentOperation.Status == AsyncStatus.Error)
    {
        DeploymentResult deploymentResult = deploymentOperation.GetResults();
        Console.WriteLine("Error code: {0}", deploymentOperation.ErrorCode);
        Console.WriteLine("Error text: {0}", deploymentResult.ErrorText);
        returnValue = 1;
    }
    else if (deploymentOperation.Status == AsyncStatus.Canceled)
    {
        Console.WriteLine("Installation canceled");
    }
    else if (deploymentOperation.Status == AsyncStatus.Completed)
    {
        Console.WriteLine("Installation succeeded");
    }
    else
    {
        returnValue = 1;
        Console.WriteLine("Installation status unknown");
    }

    return returnValue;
}
```

```cpp

using namespace Windows::Foundation;
using namespace Windows::Management::Deployment;

[STAThread]
int __cdecl main(Platform::Array<String^>^ args)
{  
    String^ inputPackageUri = args[1];
    int returnValue=0;

    Uri^ packageUri = ref new Uri(inputPackageUri);

    PackageManager^ packageManager = ref new PackageManager();

    auto deploymentOperation = packageManager->AddPackageAsync(
        packageUri, 
        nullptr, 
        DeploymentOptions::None);

    DeploymentResult^ deploymentOperationResult;

    // This event is signaled when the operation completes
    opCompletedEvent = CreateEvent(NULL, TRUE, FALSE, NULL);

    // Define the delegate
    deploymentOperation->Completed = 
        ref new AsyncOperationWithProgressCompletedHandler<DeploymentResult^, DeploymentProgress>(
        [&](IAsyncOperationWithProgress<DeploymentResult^, DeploymentProgress>^ operation, AsyncStatus)
    {
        SetEvent(opCompletedEvent);
    });

    // Wait until the operation completes
    WaitForSingleObject(opCompletedEvent, INFINITE);

    // Check the status of the operation
    if ( deploymentOperation->Status == AsyncStatus::Error )
    {
        auto deploymentResult = deploymentOperation->GetResults();
        wcout << L"Error code: " << deploymentOperation->ErrorCode.Value << endl;
        wcout << L"Error text: " << deploymentResult->ErrorText->Data() << endl;
        returnValue = 1;
    }
    else if ( deploymentOperation->Status == AsyncStatus::Canceled )
    {
        wcout << L"Installation canceled" << endl;
    }
    else if ( deploymentOperation->Status == AsyncStatus::Completed )
    {
        wcout << L"Installation succeeded" << endl;
    }
    else
    {
        wcout << L"Installation status unknown" << endl;
        returnValue = 1;
    }
    return returnValue;
}
```



## -see-also
[Add app package sample](http://go.microsoft.com/fwlink/p/?linkid=236968), [AddPackageAsync(Uri, IIterable(Uri), DeploymentOptions, PackageVolume)](packagemanager_addpackageasync_2048203939.md)

## -capabilities
packageManagement
