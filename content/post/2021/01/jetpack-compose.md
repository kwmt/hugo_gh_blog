
+++
title= "Jetpack Composeを既存のアプリと共存するには"
date= 2021-01-16T22:50:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["Android", "Jetpack", "Compose"]
keywords = ["Android", "Jetpack", "Compose"]
+++


### 現象
次のエラーが出た。
```
Execution failed for task ':core:presentation:prepareDebugKotlinCompileTask'.
> Could not resolve all files for configuration ':core:presentation:kotlin-extension'.
   > Could not find androidx.compose:compose-compiler:1.0.0-alpha08.
     Searched in the following locations:
       - https://dl.google.com/dl/android/maven2/androidx/compose/compose-compiler/1.0.0-alpha08/compose-compiler-1.0.0-alpha08.pom
       - https://jcenter.bintray.com/androidx/compose/compose-compiler/1.0.0-alpha08/compose-compiler-1.0.0-alpha08.pom
       - https://www.jitpack.io/androidx/compose/compose-compiler/1.0.0-alpha08/compose-compiler-1.0.0-alpha08.pom
       - https://oss.sonatype.org/content/repositories/snapshots/androidx/compose/compose-compiler/1.0.0-alpha08/compose-compiler-1.0.0-alpha08.pom
       - https://kotlin.bintray.com/kotlinx/androidx/compose/compose-compiler/1.0.0-alpha08/compose-compiler-1.0.0-alpha08.pom
     Required by:
         project :core:presentation

Possible solution:
 - Declare repository providing the artifact, see the documentation at https://docs.gradle.org/current/userguide/declaring_repositories.html
```

### 原因
Android Gradle Pluginのバージョンが古かったため

### 対策

```
        classpath("com.android.tools.build:gradle:4.1.0")
+        classpath("com.android.tools.build:gradle:7.0.0-alpha02")
```

```
-distributionUrl=https\://services.gradle.org/distributions/gradle-6.7-all.zip
+distributionUrl=https://services.gradle.org/distributions/gradle-6.7.1-all.zip
```

### 現象
AGPのバージョンを7.0.0にするとGradle Syncで以下のエラーが出る
classpath("com.android.tools.build:gradle:7.0.0-alpha02")

```
Unable to find method ''void com.android.build.gradle.internal.dsl.BaseAppModuleExtension.onVariants(kotlin.jvm.functions.Function1)''
'void com.android.build.gradle.internal.dsl.BaseAppModuleExtension.onVariants(kotlin.jvm.functions.Function1)'

Gradle's dependency cache may be corrupt (this sometimes occurs after a network connection timeout.)

Re-download dependencies and sync project (requires network)
The state of a Gradle build process (daemon) may be corrupt. Stopping all Gradle daemons may solve this problem.

Stop Gradle build processes (requires restart)
Your project may be using a third-party plugin which is not compatible with the other plugins in the project or the version of Gradle requested by the project.

In the case of corrupt Gradle processes, you can also try closing the IDE and then killing all Java processes.
```

### 原因
おそらくgradle play publisherが対応できてないと思われる。

https://github.com/Triple-T/gradle-play-publisher/blob/master/play/plugin/src/main/kotlin/com/github/triplet/gradle/play/PlayPublisherPlugin.kt#L158

AGP4.2.0-beta01にしたら動いたため。

### 現象
AGP4.2.0-beta01
com.github.triplet.gradle:play-publisher:3.0.0

```
class com.android.build.api.component.analytics.AnalyticsEnabledApplicationVariantBuilder_Decorated cannot be cast to class com.android.build.api.variant.ApplicationVariant (com.android.build.api.component.analytics.AnalyticsEnabledApplicationVariantBuilder_Decorated and com.android.build.api.variant.ApplicationVariant are in unnamed module of loader org.gradle.internal.classloader.VisitableURLClassLoader @35d7dd7b)
class com.android.build.api.component.analytics.AnalyticsEnabledApplicationVariantBuilder_Decorated cannot be cast to class com.android.build.api.variant.ApplicationVariant (com.android.build.api.component.analytics.AnalyticsEnabledApplicationVariantBuilder_Decorated and com.android.build.api.variant.ApplicationVariant are in unnamed module of loader org.gradle.internal.classloader.VisitableURLClassLoader @35d7dd7b)

Gradle's dependency cache may be corrupt (this sometimes occurs after a network connection timeout.)

Re-download dependencies and sync project (requires network)
The state of a Gradle build process (daemon) may be corrupt. Stopping all Gradle daemons may solve this problem.

Stop Gradle build processes (requires restart)
Your project may be using a third-party plugin which is not compatible with the other plugins in the project or the version of Gradle requested by the project.

In the case of corrupt Gradle processes, you can also try closing the IDE and then killing all Java processes.

```

https://github.com/Triple-T/gradle-play-publisher/issues/864

### 対策
3.2.0-agp4.2-2 でうまくいった
https://github.com/Triple-T/gradle-play-publisher/issues/864#issuecomment-712198156