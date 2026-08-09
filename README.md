========== build.gradle ==========

plugins {
    id 'com.android.application' version '8.9.2' apply false
    id 'org.jetbrains.kotlin.android' version '2.1.10' apply false
}


========== settings.gradle ==========

pluginManagement {
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
    }
}

dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)

    repositories {
        google()
        mavenCentral()
    }
}

rootProject.name = "VikramApp"
include(":app")


========== app/build.gradle ==========

plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
}

android {
    namespace 'com.vikram.ai'
    compileSdk 34

    defaultConfig {
        applicationId 'com.vikram.ai'
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName '1.0'
    }
}

dependencies {
    implementation 'androidx.core:core-ktx:1.16.0'
    implementation 'androidx.activity:activity-compose:1.10.1'

    implementation 'androidx.compose.ui:ui:1.8.2'
    implementation 'androidx.compose.ui:ui-tooling-preview:1.8.2'
    implementation 'androidx.compose.material3:material3:1.3.2'
}


========== app/src/main/AndroidManifest.xml ==========

<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <application
        android:theme="@style/Theme.VikramApp"
        android:label="Vikram AI"
        android:allowBackup="true"
        android:supportsRtl="true">

        <activity
            android:name=".MainActivity"
            android:exported="true">

            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

        </activity>

    </application>

</manifest>


========== app/src/main/java/com/vikram/ai/MainActivity.kt ==========

package com.vikram.ai

import android.os.Bundle

import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent

import androidx.compose.foundation.layout.Arrangement
import androidx.compose.foundation.layout.Column
import androidx.compose.foundation.layout.fillMaxSize
import androidx.compose.foundation.layout.padding

import androidx.compose.material3.Button
import androidx.compose.material3.MaterialTheme
import androidx.compose.material3.Text

import androidx.compose.runtime.Composable

import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp


class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        setContent {
            VikramApp()
        }
    }
}


@Composable
fun VikramApp() {

    MaterialTheme {

        Column(

            modifier = Modifier
                .fillMaxSize()
                .padding(24.dp),

            horizontalAlignment = Alignment.CenterHorizontally,

            verticalArrangement = Arrangement.Center

        ) {

            Text(
                text = "🤖 VIKRAM AI",
                style = MaterialTheme.typography.headlineLarge
            )

            Text(
                text = "V2000 Android App",
                modifier = Modifier.padding(8.dp)
            )

            Button(
                onClick = {
                    // Start Vikram
                }
            ) {
                Text("Start Vikram")
            }
        }
    }
}


========== app/src/main/res/values/styles.xml ==========

<resources>

    <style
        name="Theme.VikramApp"
        parent="android:style/Theme.Material.Light.NoActionBar">

        <item name="android:fontFamily">sans</item>
        <item name="android:windowLightStatusBar">true</item>
        <item name="android:statusBarColor">#FFFFFF</item>
