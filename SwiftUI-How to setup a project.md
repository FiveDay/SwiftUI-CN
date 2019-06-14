# SwiftUI-How to setup a project

![](https://cdn-images-1.medium.com/max/1600/1*hFW9aXVc-yTIHusTSzpwXQ.png)

在这个指引里，我们将一步步理解App的进入点。理解所有views的彼此连接到code，和SwiftUI如何工作.

## 必须
You need macOS Catalina and Xcode 11 Beta in order to have SwiftUI render the canvas (simulator) properly.

## 写第一个SwiftUI Code
如何用SwfitUI实现一个Tabview

    import SwiftUI
    struct Hero: Identifiable {
        let id: UUID = UUID()
        let name: String
    }
    struct ContentView: View {
        let heros = [
            Hero(name: "Iron Man"),
            Hero(name: "Thor")
            Hero(name: "Spider-Man")
        ]
        var body: some View {
            List(heros) { hero in
                Text(hero.name)
            }
        }
    }
如图
![](https://cdn-images-1.medium.com/max/1600/1*dZrLXqV-CV7mvDR59LzODw.png)

## Index

1. 创建一个工程
2. 理解App进入点
3. 理解Canvas to code
4. 定义SwiftUI View作为App启动第一个View

### 1. 创建一个工程
![](https://cdn-images-1.medium.com/max/1600/1*kGOFPgfxTPAxsADyT1evCg.png)

Next make sure that Use SwiftUI is checked before you finally determine where to create the project

![](https://cdn-images-1.medium.com/max/1600/1*hDZovfixUT7xNTUuuynjyw.png)

### 2. 理解App进入点
我们可以尽可能删除文件，保证App可运行.这样一步步可以理解他们之间的关心.

![](https://cdn-images-1.medium.com/max/2400/1*M05tj9ycF_YBhd-H-6Ao9w.png)

删除AppDelegate.swift和ContentView.swift文件.然后添加@UIApplicationMain到SceneDelegate Class的顶部.让SceneDelegate去实现UIApplicationDelegate协议.

如图
![](https://cdn-images-1.medium.com/max/2400/1*Azji9c9KTMhEiZvYfQ2rOw.png)

No fear — go and select for example the iPhone XR as a simulator and hit run!
It will launch your project and greet you with a working app! Yes it is a black screen hahah but what else should it show if we haven’t coded anything yet

### 3. 理解Canvas to code

### 4. 定义SwiftUI View作为App启动第一个View
So inside SceneDelegate.swift add the following code:

    import UIKit
    import SwiftUI
    @UIApplicationMain
    class SceneDelegate: UIResponder, UIWindowSceneDelegate, UIApplicationDelegate {
        var window: UIWindow?
        func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
            // (1)
            window = UIWindow(frame: UIScreen.main.bounds)
            // (2)
            let vc = UIHostingController(rootView: AwesomeView())
            window?.rootViewController = vc
            // (3)
            window?.makeKeyAndVisible()
        }
    }

Run App
![](https://cdn-images-1.medium.com/max/1600/1*KiuBQ_slkcKZP4X4VpCGYQ.png)

Question: How does Xcode know that the class SceneDelegate inside SceneDelegate.swift is the class to ask for the initial (root) View that your App should show on launch?
I’m glad you asked Watson! This is defined in the info.plist have a look:

![](https://cdn-images-1.medium.com/max/2400/1*-ixSTMhXReZ_cqMXiQyjKg.png)