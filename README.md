# 18-648-Lab2
Documentation that I wrote that was incorporated for future use in Lab2. APK of example solution (without modified kernel) is available. Source code can be requested, but not shown in this repo due to CMU's academic integrity standards.

# 0. Pre-requisites
You will need to download `Android Studio Bumblebee 2022.1.1.8` from the [Android Studio Archives](https://developer.android.com/studio/archive). This is the last version of Android Studio to support `API 17` before deprecation.

For `.zip` files, you can run the application by running the `studio.sh` file from terminal, which is located in the `bin` directory.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/bc56803b-dccb-494b-8b29-12de28776876)

You will also need ~15GB of disk space.

I will be using `Windows 10` for this guide, but the same steps apply for any Linux distro.

# 1. Setup Android Studio

When first running the installer, make sure `Android Virtual Device` is checked. Defaults are acceptable. After proceeding through a couple of prompts, this should load after first launch.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/7b499a3f-b9e7-4c9d-9700-a52242d8cdfa)
![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/c31db593-8dad-4071-a993-f46d71814435)

You can choose custom, but those settings will be configured later in the project configuration.
Components will take a few minutes to download. Once complete, you are ready to build the first project.

# 2. First Project configuration

After launching, Android Studio will prompt you for a project. Select `New Project`.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/4192d9b8-39db-4248-bff8-471746b05f32)

Under `Phone and Tablet`, scroll down until you find the project template labeled, `Native C++`.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/c7b73675-e5d4-4714-a538-a93fba951229)

Creating the names for the app. The recommendations are:
- Name: `taskmon`
- Package Name: `com.{andrewid}.taskmon`
- Language: `Java`

It is REQUIRED to select `API 17` as the minimum SDK. It is also required to check `Use legacy android.support libraries`.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/2de9713a-8a06-4feb-b14f-cea486bca30f)

Make sure you select `C++11` as the standard.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/38f754aa-70ec-456d-ba74-60cb2949a980)

Hit `Finish`. You will need to wait for many things to download and index. Look at the progress bar at the bottom right to confirm Android Studio has done its sanity checks. It is okay if you got some warnings or errors.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/d43f4300-d203-4642-9156-b5057fcfdbaa)

After waiting for `Gradle` to build, you will need to download specific `SDKs` for android development. On the top left, click `File` -> `Settings` -> `Appearance & Behavior` -> `System Settings` -> `Android SDK`.

Under `SDK Platforms` - If you selected `API 17` when first creating the project, `Android 4.2 (Jelly Bean)` is installed. If it is not installed, download it here, then verify in your `build.gradle` file that the `minSdk` is `17`.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/32d44d22-b4f8-416c-a300-5622f5e08efc)

Under `SDK Tools`, make sure `Google Play Licensing Library` is installed. If you are using an `Intel` processor, install `HAXM` too.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/3ceb95cc-b84c-42da-a490-feb43774ef1d)

After letting those SDKs install, let's configure the `build.gradle` file. Select the `build.gradle (Module)`. Change the `compileSdk` to `33`. This is the last SDK that is still backwards compatible with `API 17`.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/a561b679-f921-48ac-bd51-9f0a1de37543)

In the `build.gradle (Module)` file, verify that `externalNativeBuild` contains a `CMakeLists.txt` file. When building the project, this will both link and compile the `JNI` code to the project.  

There is also a `dependencies` property. You can add other android packages dependencies here.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/7e665040-d405-4758-842b-7a058169aee5)

If you get a warning about `compileSdk = 33`, I recommend adding a suppress variable. Open up `gradle.properties`, and append `android.suppressUnsupportedCompileSdk=33` to the file. 

Let's now choose a compatible `JDK` for our target. Go to `File` -> `Project Structure` -> `SDK Location`. 

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/43c3f223-fd18-40dc-b14b-6c5fd40b5f5a)

While it is not necessary to build this app, you can add an `Android NDK` here. Click the `Gradle Settings` hyperlink below.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/db49cfb3-410d-424f-b89c-0483d5c1194f)

Where it says `Gradle JDK`, make sure you are using `JDK 11`. I would recommend downloading the latest.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/ba818055-d130-404e-a89a-20657ab7b66b)

You may use whichever vendor you choose.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/761610ad-c09b-47d4-a29c-aec463b64c8c)

You should have all the necessary dependencies to build an app. You verify that the template project can build by clicking `File` -> `Sync Project with Gradle Files`. You can view the status of this sync in the `Build Log` section at the bottom of the screen. A sucessful sync will display `BUILD SUCCESSFUL`.

# 3. Android Studio layout

Let's look at the basics of Android Studio. Please compare with figure below when you reading this.

- `Developer View` is your best friend. You can switch between `Android` mode and `Project` mode, which will give you a different level of depth into the files. If you want to edit a JNI function, you will need to use the `Project` view.
- `Build` is how you compile the files. Please note, if you make changes to dependencies, clicking this will not solve your issues.
- `Sync Gradle Files` is a convenient way to resync your dependencies.
- `Device Manager` allows you to create emulated devices to test against.
- `Emulator` gives a rich ecosystem of testing against said emulated devices you create.
- `Build Log` is a helpful tab to have open to see errors.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/9bc47820-243e-4409-9bc7-0c3e3b2f93ef)

The first important piece to building an app is the code. Navigate to `app` -> `java (or Kotlin)` -> `com.{andrewid}.taskmon [main]` to find `MainActivity.java (or .kt)` is where the main logic of all your code lies. If you want to call a `c function` or make a call to a database, it needs to come from here.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/6caf7f17-bfb1-41a1-aaee-c214cf1bbb64)

The next important piece is the `JNI` integration. You can find this auto-generated code by clicking `Developer View` and switching to `Project` view. From there, you can go to `app` -> `src` -> `main` -> `cpp` to find your boiler-plate code. There is a separate section to describe more in depth.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/2b41c0da-d6c2-482a-8c50-97d7a3faeb52)

The last section is the UI/UX design. You can find this in the `Android` view, under `app` -> `res` -> `layout`. In here you can create an interface and link it to the java code.   

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/18cdcf79-1fa1-43bb-a406-aabb2c4ff69b)


# 4. JNI integration
If you selected a `Native-c++` template when creating the project, you can find a `JNI` boiler-plate code already generated. It is recommended that you replace this `.cpp` file with a `.c` file, as it makes `syscall` handling much easier. You should not rename the existing file, but create a new one.

Right click the `cpp` folder, click `New` -> `File`. Name it `{file-name}.c`. I named it `native-lib.c` just for ease of use.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/c148b427-076e-4f70-bb07-9e913a1a5813)

Add your file to the `CMakeLists.txt`. Where it specifies `add_library` delete any existing mention of the `.cpp` file and put in your `.c` file.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/bd75a365-39bd-4bec-902f-f7da8818a297)

## JNI code paradigms
JNI has some interesting choices of syntax that you will need to watch out for.

  - functions in the `.c` file must start with `Java_`.
  - the following piece is the full name of your app. It should be `com_{andrewid}_taskmon` if you have followed the nomenclature of this guide.
  - `MainActivity` refers to the `Java` class it will be shared with.
  - the last piece is the function name. Please note, you cannot have functions with underscores in the name
  - All functions must include `JNIEnv*` and `jobject` as the first two parameters. Just having these two parameters is equivalent to no functional parameters in traditional `C`. You may add other parameters after these two mandatory parameters.
  - all types in the `JNI` version of `C` must start with `j`. For example, `jstring`, `jint`, `jobject`.

From a design perspective, you will either need to choose to make direct `syscall`s (by adding `#include <unistd.h>` in the `C` file) or choose to make a `fork` of the app program and call `execv`. That is up to you. 

I have added a sample function to get started. The code is also copy-able below. Please remember to replace the function name with your app name.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/bac03df6-1aa1-4f2b-b8b0-87d3a37e2760)

Sample code for starter function in JNI
```c
#include <string.h>
#include <jni.h>

jstring
Java_com_sspivey98_taskmon_MainActivity_stringFromJNI( JNIEnv* env,
                                                       jobject obj )
{
#if defined(__arm__)
    #if defined(__ARM_ARCH_7A__)
    #if defined(__ARM_NEON__)
      #if defined(__ARM_PCS_VFP)
        #define ABI "armeabi-v7a/NEON (hard-float)"
      #else
        #define ABI "armeabi-v7a/NEON"
      #endif
    #else
      #if defined(__ARM_PCS_VFP)
        #define ABI "armeabi-v7a (hard-float)"
      #else
        #define ABI "armeabi-v7a"
      #endif
    #endif
  #else
   #define ABI "armeabi"
  #endif
#elif defined(__i386__)
#define ABI "x86"
#elif defined(__x86_64__)
#define ABI "x86_64"
#else
#define ABI "unknown"
#endif

    return (*env)->NewStringUTF(env, "Compiled with ABI: " ABI ".");
}
```

The last piece is integrating your code with `Java`.
  - all invocations of `C` functions require a `public` scope.
  - `native` is a required keyword.
  - return datatype of cuntion declaration in `Java` should match the return datatype in the `C` file. Please remember that in `Java` this might be `String` but in `C` it is `jstring`.

If your function linked successfully, you will see an `C` file icon appear on that row with the function declaration. You may now invoke this function in your java code.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/1e47b9a7-97d2-4a67-9145-c777f9af057d)

# 5. Emulated tablet setup & tablet configuration

Let's create virtual tablet to mimic your physical tablet. On the right side, click on `Device Manager`.
- If you haven't created a device yet, then select the `Create virtual device` hyperlink.
- If you have created a device, then select `Create device` button near the top of `Device Manager`.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/bb6a6c57-0d67-48a4-b770-588d8fad534c)

Under `Category` select `Tablet`. To have an exact copy of your physical tablet, select `Nexus 7 (2012)`.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/61b796fe-4629-4d6d-8db6-09c7900e0c26)

I recommend creating two tablets - the first one using `x86` which will very responsive and the other one using `armeabi-v7a`, which is a 1-to-1 copy of your tablet but will be much slower. Because `Google APIs` are deprecated, I would recommend against using a `system image` with it.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/7ff7e7de-caaa-49cb-b80d-937b26607111)
![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/38b96aae-e6e1-4ec8-a033-b5689a0a468b)

After creation, in the `Device Manager` you can power on by clicking the play button.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/9f7a2f76-2bdb-407e-a85e-bc0473e9f903)

Once the tablet is booted, you may have to jump through a few prompts. There are a couple of important tools to note:
- `Choose Device Target` allows you to pick the device you are running against. This can even be your physical tablet!
- `Start on target` builds and deploys the application onto the target device. It will then launch the program.
- `Attach Debugger` allows you to remote debug your code. You need to `Start on target` before clicking this button. Please note, you cannot set breakpoints in your `C` code.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/89e7467d-0abd-4b4f-965a-7e352ee6a790)


# 6. Linking the interface (Getting started)

The purpose of this section is to help you get started. Navigate to `activity_main.xml`. You can add elements to the screen by dragging from the `Palette`. When a new element is created, you will notice that a property called `android:id` is created. This `id` is how you reference in your `Java` code.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/4eb4ce66-3e7a-43b6-b71c-484c1703c3da)

In the `MainActivity.java` code, you can reference that element you created in the `Design`. See the code below to get an understanding of how you should reference.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/602fbdc1-ac3a-40e9-95d0-6413cffbbd35)

You can also add `actions` or `event listeners` to the elements. I will show an example of a `onClick` event for a `button`.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/ff35d0da-aab7-442d-8ef6-63b02e304c31)
![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/709d25a6-d5b2-422d-9a52-6846cccf1d06)


# 7. Installing on tablet

To run a `release` build on your tablet, there are a couple of different steps. Navigate to `Build` -> `Build Bundle(s) / APK(s)` -> `Build APK(s)`.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/0cc98999-3cda-45c7-98db-26837686a046)

In the `notifications` bar, you can select the `locate` hyperlink to quickly navigate to the `.apk` file just created.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/81554cf8-8a24-4e14-8bba-77a4afe24d25)
![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/526e1569-f64c-4b26-8e96-b643adbe249a)

Once created, you can install the `.apk` on the device:
- to install `.apk` file on target run: `adb install app-debug.apk`
- to update existing installation run: `adb install -r app-debug.apk`

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/c7479125-6069-4479-a7ee-e2367440c556)


# 8. GitHub

There are some helpful GitHub tools to help speed up your development. You can login to `GitHub` using `File` -> `Settings` -> `Version Control` -> `GitHub`. After adding your account, you can easily do source control within the application.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/7ae8c673-7df8-4e0d-b148-1973ef17a2b3)

Please make sure to click `Build` -> `Clean Project` before making any commits to the repository! You will add `~500MB` of unncessary, host-specific, cache files that will make your collaborators upset! 

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/09821e05-0ccd-44c3-90e0-5c9c9090223f)

After you have created a stable build, you should create `Release` in `GitHub`. Navigate to the `GitHub` page, and select `Releases` on the right side.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/1bbe5eba-a573-4796-96b7-135d66f3afea)

From there, you can drag-and-drop your `.apk` file and attach it to a release. This will allow for easy deployment for when you present to the TA.

![image](https://github.com/18648CMUFall23/taskmon-sspivey98/assets/144056454/a49905a6-f4ee-425f-8537-838d17e24a3d)
