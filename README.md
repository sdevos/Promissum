<img src="https://cloud.githubusercontent.com/assets/75655/5077599/2f2d9f8c-6ea5-11e4-98d2-cdb72f6686a8.png" width="170" alt="Promissum">
<hr>

Promissum is a promises library written in Swift. It features some known functions from Function Programming like, `map` and `flatMap`.

It has useful combinators for working with promises like; `whenAll` for doing something when multiple promises complete, and `whenAny` for doing something when a single one of a list of promises completes. As well as their binary counterparts: `whenBoth` and `whenEither`.

Promissum really shines when used to combine asynchronous operations from different libraries. There are currently some basic extensions to UIKit, Alamofire and CoreDataKit, and contributions for extensions to other libraries are very welcome.

Example
-------

This example demonstrates the [Alamofire+Promise](https://github.com/tomlokhorst/Promissum/blob/develop/extensions/PromissumExtensions/Alamofire%2BPromise.swift) and [CoreDataKit+Promise](https://github.com/tomlokhorst/Promissum/blob/develop/extensions/PromissumExtensions/CoreDataKit%2BPromise.swift) extensions.

In this example, JSON data is loaded from the Github API. It is then parsed, and stored into CoreData.
If both those succeed the result is shown to the user, if either of those fail, a description of the error is shown to the user.

    let url = "https://api.github.com/repos/tomlokhorst/Promissum"
    Alamofire.request(.GET, url).responseJSONPromise()
      .map(parseJson)
      .flatMap(storeInCoreData)
      .then { project in

        // Show project name and description
        self.nameLabel.text = project.name
        self.descriptionLabel.text = project.descr

        UIView.animateWithDuration(0.5) {
          self.detailsView.alpha = 1
        }
      }
      .catch { e in

        // Either an Alamofire error or a CoreData error occured
        self.errorLabel.text = e.localizedDescription
        self.errorView.alpha = 1
      }

See [FadeExample/ViewController.swift](https://github.com/tomlokhorst/Promissum/blob/develop/examples/FadeExample/FadeExample/ViewController.swift) for an extended version of this example.


Cancellation
------------

Promissum does not support cancellation, because cancellation does not work well with promises. Promises are future _values_, values can't be cancelled. If you do need cancellation (quite often useful), take a look at Tasks or Rx instead of promises. I don't have experience with any Swift Task/Rx libraries, so I can't recommend a specific one.

Although, if you're looking at adding cancellation to a _PromiseSource_, you could use the [swift-cancellationtoken](https://github.com/tomlokhorst/swift-cancellationtoken) library I wrote. This is orthogonal to promises, however.


Installation
------------

### CocoaPods

[CocoaPods](http://cocoapods.org) is a dependency manager for Cocoa projects.

CocoaPods 0.36 RC adds supports for Swift and embedded frameworks. You can install it with the following command:

```bash
$ gem install cocoapods --pre
```

To integrate Promissum into your Xcode project using CocoaPods, specify it in your `Podfile`:

```ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.0'

pod 'Promissum', '~> 0.2.2'
```

Then, run the following command:

```bash
$ pod install
```

(These installation instructions are based on the ones for Alamofire)


### Using git submodules

If, for some reason, using CocoaPods is not an option, Promissum can be added via a git submodule.
There are five steps, which I've also demonstrated in a [screencast](https://www.youtube.com/watch?v=ow1ZE7pfBH8):

1. Add Promissum as a submodule the terminal using the command: `git submodule add https://github.com/tomlokhorst/Promissum.git`
2. Open the `Promissum/src` folder, and drag `Promissum.xcodeproj` into the file navigator of your app project.
3. In Xcode, navigate to the target configuration window by clicking on the blue project icon, and selecting the application target under the "Targets" heading in the sidebar.
4. In the tab bar at the top of that window, open the "Build Phases" panel.
5. Click on the + button at the top left of the panel and select "New Copy Files Phase". Rename this new phase to "Copy Frameworks", set the "Destination" to "Frameworks", and add `Promissum.framework`.

(These installation instructions are based on the ones for Alamofire)


Licence & Credits
-----------------

Promissum is written by [Tom Lokhorst](https://twitter.com/tomlokhorst) and available under the [MIT license](https://github.com/tomlokhorst/promissum/blob/master/LICENSE), so feel free to use it in commercial and non-commercial projects.
