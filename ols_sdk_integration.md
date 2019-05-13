## Project location
* OLS SDK: `/somepath/ols-sdk`
* iOs project: `/somepath/iOSBankApp`
* Android project: `/somepath/android-bank-app`

## Prerequisite
`Flutter` for both iOS & Android
`CocoaPods ` for iOS
* Install Flutter:
[https://flutter.dev/docs/get-started/install](https://flutter.dev/docs/get-started/install)
* Install CocoaPods for OSX
[https://guides.cocoapods.org/using/getting-started.html#toc_3](https://guides.cocoapods.org/using/getting-started.html#toc_3)

## SDK Structure:

![](https://snag.gy/2cTYza.jpg)

* **.ios**: library for ios app, contains some Cocoapods and a helper Ruby script
* **.android**: library for android app

## iOS Integration

### <a name="fenced-code-block"> Project configuration </a>

##### if current project is not using CocoaPods, install CocoaPods:
* Open terminal, change to `somepath/iOSBankApp`
* Run command: `pod init` to creates a `Podfile` for the current project 

![https://snag.gy/f1eZWw.jpg](https://snag.gy/f1eZWw.jpg)

##### 1.Add the following lines to `Podfile`:
	
~~~~ 
flutter_application_path = '/somepath/ols-sdk/'
eval(File.read(File.join(flutter_application_path, '.ios', 'Flutter', 'podhelper.rb')), binding) 	
~~~~

##### 2. Run `pod install` in the terminal.

##### 3. Open file `iOSBankApp.xcworkspace`

![https://snag.gy/06Ugmv.jpg](https://snag.gy/06Ugmv.jpg)

##### 4. Disable Bitcode for target:

Disable `ENABLE_BITCODE` flag located in target's `Build Settings->Build Options->Enable Bitcode `
 
![ https://snag.gy/4tnD9g.jpg
](https://snag.gy/4tnD9g.jpg) 

##### 5. Add a build phase for building the Dart code

Select the top-level `iOSBankApp` project in the Project navigator. Select **TARGET** `iOSBankApp` in the left part of the main view, and then select the `Build Phases` tab. Add a new build phase by clicking the + towards the top left of the main view. Select `New Run Script Phase`. Expand the new `Run Script`, just appended to the list of phases.

Paste the following into the text area just below the `Shell field`:

~~~~
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" build
"$FLUTTER_ROOT/packages/flutter_tools/bin/xcode_backend.sh" embed
~~~~

Finally, drag the new build phase to just after the `Target Dependencies` phase.


![https://snag.gy/ZQqaFH.jpg
](https://snag.gy/ZQqaFH.jpg) 

Build the project using `⌘B`

### Write code to use FlutterViewController from bank app
##### 1. In `AppDelegate.h`: 
* Declare app delegate to be a subclass of `FlutterAppDelegate`. 
* `FlutterEngine` property, which help to register a plugin without a `FlutterViewController`.

~~~~
#import <UIKit/UIKit.h>
#import <Flutter/Flutter.h>

@interface AppDelegate : FlutterAppDelegate
@property (nonatomic,strong) FlutterEngine *flutterEngine;
@end

~~~~

![https://snag.gy/Dy3QxT.jpg](https://snag.gy/Dy3QxT.jpg)

##### 2. In `AppDelegate.m`:
~~~~
#import <FlutterPluginRegistrant/GeneratedPluginRegistrant.h> // Only if you have Flutter Plugins

#include "AppDelegate.h"

@implementation AppDelegate

// This override can be omitted if you do not have any Flutter Plugins.
- (BOOL)application:(UIApplication *)application
    didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  self.flutterEngine = [[FlutterEngine alloc] initWithName:@"io.flutter" project:nil];
  [self.flutterEngine runWithEntrypoint:nil];
  [GeneratedPluginRegistrant registerWithRegistry:self.flutterEngine];
  return [super application:application didFinishLaunchingWithOptions:launchOptions];
}

@end
~~~~

![https://snag.gy/3F7W1Q.jpg
](https://snag.gy/3F7W1Q.jpg)

##### 3. Create new Objective-C class name `LoyaltyViewController` extend `FlutterViewController`

![https://snag.gy/FPa3LK.jpg](https://snag.gy/FPa3LK.jpg)

##### 4. In Main.storyboard create new ViewController
* Class: `LoyaltyViewController`
* Storyboard ID: `LoyaltyViewController`

![https://snag.gy/KJer06.jpg](https://snag.gy/KJer06.jpg)

#### 5. Handle click event from button bank app

* Open bank app: `ViewController.m`

~~~~
#import "ViewController.h"
#import <Flutter/Flutter.h>
#import "AppDelegate.h"
#import "ViewController.h"
#import "LoyaltyViewController.h"
#import <FlutterPluginRegistrant/GeneratedPluginRegistrant.h> // Only if you have Flutter Plugins

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
}

- (IBAction)openLoyaltyApp:(id)sender {
    UIStoryboard *sb = [UIStoryboard storyboardWithName:@"Main" bundle:nil];
    LoyaltyViewController *loyaltyViewController = [sb instantiateViewControllerWithIdentifier:@"loyaltyViewController"];
    [[[[UIApplication sharedApplication] delegate] window] setRootViewController:loyaltyViewController];
    [GeneratedPluginRegistrant registerWithRegistry:loyaltyViewController];
    [self presentViewController:loyaltyViewController animated:true completion:nil];
}
    
@end
~~~~

##### 6.Building and running bank app
### <a name="fenced-code-block"> Done iOS </a>



