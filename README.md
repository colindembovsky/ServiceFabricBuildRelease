# Continuous Deployment of Service Fabric Apps using VSTS
For the full story, read [this post](http://colinsalmcorner.com/). The demo application is the Visual Objects
demo from [this repo](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).

## Using the Sample
There are a couple of files that you'll need to edit in order to get this to work:

1. Edit the ARM templates
    There are two templates in the ARM project: one for a test environment and one for prod. Currently
    these both set up an unsecured cluster of 3 nodes. The easiest way to get your ARM template is to 
    log on to [the Azure portal](http://portal.azure.com) and create a new Service Fabric Cluster. At
    the end of the journey, just before clicking create, there is an "Export Template" button which
    you can use to export the ARM template. I extracted some of the parameter values into the 
    `parameters.json` file, but that's just a personal preference.
    
    The templates are in `src/Environments/ClusterARM/Templates`.
2. Edit the Profiles
    There are three profiles: Local, TestCloud and ProdCloud (each with their associated ApplicationParameters).
    You'll need to edit the ProdCloud XML to make a secure connection (the sample uses an unsecure one) and
    you'll need to edit the ApplicationParameters files to suit your application.
    
    The profiles are in `src/VisualObjects/VisualObjects/PublishProfiles`.
    The app parameters files are in `src/VisualObjects/VisualObjects/ApplicationParameters`.

## Demo
To demo the pipeline, create the build and release as outlined in (this post)[http://colinsalmcorner.com/].

Run the build and release to Test. Browse to the test application endpoint:

http://_clustername_.eastus.cloudapp.azure.com:8082/VisualObjects (where _clustername_ is the name of your
cluster). You should see some bouncing triangles.

Then you can edit `src/VisualObjects/VisualObjects.ActorService/VisualObjectActor.cs` in Visual Studio or
in the Code hub in VSTS. Look around line 50 for

```csharp
visualObject.Move(false);
//visualObject.Move(true);
```

and change it to look like this:

```csharp
//visualObject.Move(false);
visualObject.Move(true);
```

Queue a new build (or look for the one that the CI triggered) and wait until it has released to Test.
Keep watching the triangles - you should see them start to rotate as the Actors are upgraded.