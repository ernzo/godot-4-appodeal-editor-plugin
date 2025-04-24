# Non Functional/Outdated Appodeal Plugin for Godot

This plugin was made for older Godot versions, and is built on the older v1 architecture.

## For this version of the plugin to "work":

*   Need to place the `Appodeal.gdap` file in the "android" folder of your project > `ProjectName/Android/`
*   Need to place `Appodeal-2.0.0-release.aar` in the "android/appodeal" folder of your project > `ProjectName/Android/appodeal`

This makes the plugin appear on Godot's Export menu for Android.

### This means:

*   The plugin will be included on Build.
*   You will be able to use the methods supported by `Appodeal-2.0.0-release.aar`.

This means you can use **some** of the functionality.

For instance I managed to make it display the Google GDPR Consent message,
but it could not finish the consent validation process, and so the process didnt continue and Test ADs were not displayed...


## Problems:

### Modern Appodeal SDK vs Monolithic

*   Modern Appodeal SDKs like `3.5.2.0` are **not** Monolithic files like `Appodeal-2.0.0-release.aar`.
*   They are **Modular SDKs** that use standard Android development via Maven/Gradle.
*   They consist of a lean Core SDK (`com.appodeal.ads:sdk:...`) and Separate Dependencies for each ad network adapter (`com.appodeal.ads.sdk.networks:admob:...`, etc.).
*   These are fetched automatically from Maven repositories by the Gradle build system.

### Godot Build System Limitations

*   Godot's build system for Android isn't set up like a standard Android Studio Gradle project.
*   It expects pre-compiled libraries (`.aar`, `.jar`) to be directly included or managed through its own plugin structure.

### Maven Requirement

*   The new Appodeal SDK **requires** a build system (Gradle) that can:
    *   Connect to Maven repositories online.
    *   Automatically download the specified core SDK and all requested network adapter `.aar` files and their own dependencies (**transitive dependencies**) during the build.

**IOW:** Godot's standard Android export process and plugin system seem to lack the built-in capability to perform this automatic Maven dependency resolution, required by the modern modular Appodeal SDK.

Integrating the new one requires actively pulling many pieces from the internet during the build, which Godot doesn't natively for plugins in this way.

## No Solution?

There's different approaches one can follow, but they end up **Not Working**.

 *  **For instance, you can try to make a Java Bridge plugin to fetch the New SDK from appodeal..**
    *   Create a `AppodealBridge.java` file with all the necesary elements, and modifying the `build.gradle`, and `settings.gradle` in `"android/plugins"` folder...
    *   You can create a plugin that will try to fetch the SDK from Appodeal servers.

*   **But this is set to fail for many reasons:**
    *   Not all SDK files may be accesible.
    *   Even if you had all files there will be conflicts on the build process.

**IOW:** Creating a Java bridge within the Godot plugin structure that tries to declare the new Appodeal dependencies in its own `build.gradle` fails because:

*   **Transitive Dependencies:** Manually downloading just the main Appodeal SDK and adapter `.aar` files is insufficient.
    *   We would also need to find and include all dependencies, which can be numerous and complex; which defeats the purpose of using Gradle/Maven.
 *  **Build Conflicts:**
    *   Managing these dependencies manually alongside Godot's own build process is prone to version conflicts and build errors.
 *  **Complexity:**
    *   It essentially requires replicating the functionality of Gradle's dependency management within Godot's more limited plugin build environment.
   And so on..

**So > there's basically No Solution!**
