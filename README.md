# TuistLongGoogleMapResources
This repo reproducing an issue in Tuist when trying to add resources and frameworks from the path.

This example project was generated with the `tuist init` command

The layout of the xframeworks and resources

In this repo I have a copy of the Google folder in two subfolders - `libs` and `Resources`
The Google folder holds some beta frameworks of Google maps - the same copy is present in both `libs` and `Resource`

Now, that does not makes sense - is it? To have a copy of the same code/resources in two folders. But it seems like a Tuist bug.

Take a look at `Tuist/ProjectDescriptionHelpers/Project+Templates`, at the `makeAppTargets` function. One will notice that we added a bundle from the GoogleMaps framework to the app resources, and dependencies of GoogleMaps as xcframework.
You will notice that the resources are pointing to the copy in the `Resources` folder and that the frameworks are pointing to the frameworks in the `libs` folder. As I mentioned before those two folders contain the same files - exactly!

Before reproducing the issue run `tuist generate` to see that you can generate the project.
You will notice that the app is linked against the xcframeworks and that in the copy resources build phase the bundle is copied - see attached images

Resources - ![Resources](ScreenShots/Resources)
Frameworks - ![Frameworks](ScreenShots/Frameworks) 


To reproduce the issue, either change line 62 to fetch the resources from the `libs` folder:
```
                .glob(pattern: "libs/Google/GoogleMaps/GoogleMaps-6.0.1-beta/GoogleMaps.xcframework/ios-arm64/GoogleMaps.framework/Resources/GoogleMaps.bundle"),
```

Run `tuist generate` - you shall see a similar error:
```
Couldn't find a reference for the file at path /Users/danielbac/Documents/TuistLongGoogleMapResources/libs/Google/GoogleMaps/GoogleMaps-6.0.1-beta/GoogleMaps.xcframework.
```

Or change any or all the lines 65-67 to fetch the framework from the Resources folder:
```
                .xcframework(path: "Resources/Google/GoogleMaps/GoogleMaps-6.0.1-beta/GoogleMaps.xcframework"),
                .xcframework(path: "Resources/Google/GoogleMaps/GoogleMaps-6.0.1-beta/GoogleMapsBase.xcframework"),
                .xcframework(path: "Resources/Google/GoogleMaps/GoogleMaps-6.0.1-beta/GoogleMapsCore.xcframework"),
```
 
Run `tuist generate` - you shall see a similar error:
```
Couldn't find a reference for the file at path /Users/danielbac/Documents/TuistLongGoogleMapResources/Resources/Google/GoogleMaps/GoogleMaps-6.0.1-beta/GoogleMaps.xcframework.
```
