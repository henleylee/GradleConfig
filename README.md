# GradleConfig-master —— Gradle统一依赖管理

## 1. 前言 ##
    目前的Android开发为了减少编译时间，开发效率，大多都采用模块化，组件化的开发方式。 采用这种方式不可避免的将会用到多个Library。 那么当我们协同开发时，如何处理每个人的版本统一呢？
    我想大部分人应该都在使用Gradle来依赖管理，还没有使用的去面壁思过，Gradle使用起来简直太好用了，举个例子，我们想依赖个support-v4包，直接一句话：
```java
    compile 'com.android.support:support-v4:25.1.0'
```
    但是不知道你们发现一些问题没有？比如以后v4版本号如果升级了怎么办？你可能会说直接手动改下版本号就好了，又不费多少精力。是的，只有这一个地方当然很简单，假设你项目里引用了三四个module，而这些module里也引用了v4包，那就要把所有依赖v4的包全部手动更改，这是一个项目，而如果你又是多个项目呢？这还只是一个v4包，假设又有多处地方依赖okhttp呢？而且下次所有的compileSdkVersion跟buildToolsVersion都要跟着升级呢？每次版本更新都要手动更改简直太麻烦了，而且还容易遗漏某些地方。
    下面我们看看常规的方式：
![](/screenshots/常规方式.png)
    此项目有主项目app和一个模块modulebase，对于个module目录下的build.gradle文件：
```java
    apply plugin: 'com.android.application'

    android {
        compileSdkVersion 25
        buildToolsVersion "25.0.0"
        defaultConfig {
            applicationId "com.liyunlong.gradleconfig"
            minSdkVersion 14
            targetSdkVersion 25
            versionCode 1
            versionName "1.0"
        }
        buildTypes {
            release {
                minifyEnabled false
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            }
        }
    }

    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        compile 'com.android.support:appcompat-v7:25.1.0'
        compile 'com.android.support:design:25.1.0'
        compile 'com.android.support:recyclerview-v7:25.1.0'
    }
```
    这一段重复的配置，难道每新增一个libs，我们都需要去手动配置成一样的？有没有更好的方式呢？那么今天就来给大家提供一种解决方案完全解决这种问题。我们根本性的解决这个问题就是通过一个配置文件来管理所有的Gradle依赖。那么具体怎么实现呢？
* **第一步**：在主项目的根目录下创建config.gradle文件
* **第二步**：将我们需要的配置信息写入config.gradle文件，配置信息如下：
![](/screenshots/config.gradle.png)
    注意：我们此处对应的版本号和之前主项目与libs的一样。
* **第三步**：在主项目根目录下的build.gradle文件中加入一句代码：
```java
    apply from: "config.gradle"
```
* **第四步**：在项目的所有module中引用我们的路径配置。如下图：
![](/screenshots/module.png)
    至此，大功告成，我们所有的libs均可采用此种配置，一次配置，终身享受。是不是很简单方便呢？以后所有的版本号或者配置的更改直接更改这个文件就好了，而且可以把所有项目中使用到或者可能使用到的第三方库都塞到这里管理，用到了就依赖，用不到就放里面就好了。



