+++
authors = ["Quentin Fasquel"]
title = "From SPI to Package Traits"
date = "2025-03-10"
tags = [ "Swift", "Swift Evolution" ]
+++

# Swift – From SPI to Package Traits

## 1. Specialized Programing Interface 🥷

Specialized Programming Interface (`@_spi`) is a feature introduced in **Swift 5.3** that allows you to restrict access to certain parts of your API. Unlike traditional access levels such as `public` or `internal`, **`@_spi`** enables you to make parts of your API accessible only to a specific group of users, such as specific internal modules or packages, by specifying the SPI name.

#### **`@_spi(CaptureUI) import Capture`**
Let see an example with package `CaptureSDK`, check the following files:

<details>
  <summary>`Package.swift`</summary>
  
```
// swift-tools-version: 5.9
import PackageDescription

let package = Package(
    name: "CaptureSDK",
    products: [
        .library(name: "Capture", targets: ["Capture"]),
        .library(name: "CaptureUI", targets: ["CaptureUI"]),
    ],
    targets: [
        .target(name: "Capture"),
        .target(name: "CaptureUI", dependencies: ["Capture"]),
    ]
)
```
</details>
<details>
  <summary>`Sources/Capture/Capture.swift`</summary>

```
//
//  Capture.swift
//  CaptureSDK/Capture
//

public import AVFoundation

@_spi(CaptureUI)
public class CameraService {
    public init(session: AVCaptureSession) {

    }
}
```
</details>
<details>
  <summary>`Sources/CaptureUI/CaptureUI.swift`</summary>

```
//
//  CaptureUI.swift
//  CaptureSDK/CaptureUI
//

@_spi(CaptureUI) import Capture

// Can now use CameraService() ...
```
</details>

In this example, the `CaptureUI` library imports an SPI called **`CaptureUI`** from the `Capture` library. By marking `CameraService` with **`@_spi(CaptureUI)`**, we indicate that this class is only intended for use by the CaptureUI library (or whoever else knows about the SPI name). This ensures that the internals of the Capture library aren't exposed to the outside world, maintaining a level of secrecy and control over experimental or incomplete features until they are ready for broader use.


And that's essentially what `SPI` is. Apple likely uses this feature within their own projects. You and your team can leverage it in similar ways: for example, when you introduce a new capability in your library that isn't yet ready for public release. Wrapping that feature in an SPI allows only those on your team — those who know the SPI name — to access it. This ensures the feature remains private until it's fully polished and ready for a broader release.

#### Working with xcframework (binaries)

When working with closed-source libraries or distributing Swift packages as binaries (such as `xcframework`), SPI can be particularly useful. During the build process, the SPI module interface is properly exported in the binary, ensuring that only those who know the SPI can access these internal features, even when the code isn't publicly available.



## 2. Access-Level Package 📦

An access-level keyword defines the visibility of your types and members (such as structs, classes, enums, properties, and functions). Prior to Swift 6.0, the most common access levels were `public`, `internal`, `fileprivate` and `private`, ordered from most to least visible.

> Let's keep the `open` keyword off topic. It is similar to `public` in visibility, but it has one key difference: it's reserved for classes and allows them to be subclassed or extended outside the module.

**Swift 6.0** introduced a **new access-level `package`** that provides a more granular approach to controlling visibility within a package. The access-level `package` works between `public` and `internal` access, making it ideal for cases where you want to expose some functionality within the same package but restrict it from external users.

#### Access-level on imports, `package`

Swift 6.0 also introduced **access-level on imports**. But there's  a notable distinction: the default access-level for imports is `public`, which is different from the usual default of internal for other members. This change was made to maintain compatibility with previous Swift versions, where the imports behavior was public by default.

> This behavior may change in the future, and it can already be adjusted if needed through the compiler directive `-enable-upcoming-feature` `InternalImportsByDefault` or in Xcode: `SWIFT_UPCOMING_FEATURE_INTERNAL_IMPORTS_BY_DEFAULTS=YES`

With this change, when applying the new access-level `package` on imports, you can avoid the need for SPIs in certain cases. In the previous example, since the `CaptureUI` and `Capture` libraries belong to the same Swift package, they can take advantage of the `package` access-level and share functionality without needing SPI to control visibility.

#### ⚠️ Working with xcframework

Since building an `xcframework` for Apple platforms requires the use of **Xcode** with a library or framework target, you may wonder how the `package` access-level knows which package to refer to when building a binary. This is made possible through the `SWIFT_PACKAGE_NAME` build setting in Xcode. This build setting ensures that symbols with the `package` access-level are correctly visible across different libraries or frameworks that belong to the same package, as specified by the `SWIFT_PACKAGE_NAME` value.


--


# 3. Package Traits 🧪

**Swift 6.1** introduced a new feature to SPM (_Swift Package Manager_) called **Package Traits**. I initially thought it would be a stable replacement for `@_spi` (which, as an underscore feature, is considered "non-stable")... it' not.

While you could use *traits* to divide your package features and specify which subset of your team or collaborators can access them, they are not intended for controlling visibility. Instead, **Package Traits** enable conditional compilation, allowing specific code paths to be included or excluded based on the traits specified in the consuming package.

> Swift 6.1 is available with Xcode 16.3, but as of the first beta, there is currently no way to add a package product dependency with specific traits.

Let see an example of how it works, and how to describe **Package Traits**  in a `Package.swift`, check the following files:

<details>
  <summary>`CaptureSDK/Package.swift`</summary>
  
```
// swift-tools-version: 6.1
import PackageDescription

let package = Package(
    name: "CaptureSDK",
    products: [
        .library(name: "Capture", targets: ["Capture"]),
        .library(name: "CaptureUI", targets: ["CaptureUI"]),
    ],
    traits: [
        .trait(name: "experimental")
    ],
    targets: [
        .target(name: "Capture"),
        .target(name: "CaptureUI", dependencies: ["Capture"]),
    ]
)
```
</details>

<details>
  <summary>`CaptureSDK/Sources/Capture/Capture.swift`</summary>

```
//
//  Capture.swift
//  CaptureSDK/Capture
//

public import AVFoundation

public class CameraService {
    public init(session: AVCaptureSession) {

    }

#if experimental
    public func someExperimentalFeature() {
        //
    }
#endif
}
```
</details>

<details>
  <summary>`ConsumerPackage/Package.swift`</summary>
  
```
// swift-tools-version: 6.1
import PackageDescription

let package = Package(
    name: "ConsumerPackage",
    products: [
        .library(name: "ConsumerLibrary", targets: ["ConsumerLibrary"]),
    ],
    dependencies: [
        .package(
            url: "http://github.com/quentinfasquel/Capture",
            from: "0.1",
            traits: ["experimental"]
        ),
    ],
    targets: [
        .target(
            name: "ConsumerLibrary",
            dependencies: [
                .product(name: "Capture", package: "CaptureSDK")
            ]
        ),
    ]
)
```
</details>

As you can see, they offer a flexible mechanism for enabling or disabling code based on specific traits. There's more to it, with _conditional traits_, I invite you to read the [original swift-evolution proposal](https://github.com/swiftlang/swift-evolution/blob/main/proposals/0450-swiftpm-package-traits.md) to understand the motivation for this new feature.

--

In this article, we've explored three related novelties in Swift — Specialized Programming Interface (SPI), Access-Level Package, and Package Traits. While SPI itself isn’t new, each of these features offers a distinct way to manage visibility and structure dependencies within Swift packages. Together, they provide developers with more flexible tools for managing complex packages and workflows in modern Swift development.