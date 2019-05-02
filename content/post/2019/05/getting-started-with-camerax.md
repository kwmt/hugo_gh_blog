+++
title= "Getting Started with CameraXというCodelabをやってみた"
date= 2019-05-02T23:50:00+09:00
draft = true
toc = true
slug = ""
author = "kwmt27"
categories = ["Android"]
tags = ["CameraX"]
keywords = [""]
+++

<!--more-->


# はじめに


2019/05/01にCameraXなるCodelabが公開されてたのでやってみました。
https://codelabs.developers.google.com/codelabs/camerax-getting-started/index.html?index=..%2F..index#0

※2019/05/02時点でまだmavenで公開されていないため、動きません。

## 1,2

1は概要、2は新規プロジェクトを作成します。


## 3. Add the Gradle dependencies

minsdkversionを21以上でempty projectを作成したら、app/build.gradleに下記を追加します。



```groovy
def camerax_version = "1.0.0-alpha01"
implementation "androidx.camera:camera-core:${camerax_version}"
implementation "androidx.camera:camera-camera2:${camerax_version}"
```

## 4. Create the viewfinder layout

次にレイアウトを編集します。
固定したスクエアのビューファインダーを作成します。



```xml
<TextureView
            android:id="@+id/view_finder"
            android:layout_width="640px"
            android:layout_height="640px"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintEnd_toEndOf="parent" />
```

## 5. Request camera permissions


AndroidManifest.xmlにapplicationタグの前にCameraパーミッションを追加します。

```xml
<uses-permission android:name="android.permission.CAMERA" />
```

カメラを使うのでランタイムパーミッションの実装をします。


```kotlin
private const val REQUEST_CODE_PERMISSIONS = 10

private val REQUIRED_PERMISSIONS = arrayOf(Manifest.permission.CAMERA)

class MainActivity : AppCompatActivity() {

    private lateinit var viewFinder: TextureView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        viewFinder = findViewById(R.id.view_finder)
        if (allPermissionsGranted()) {
            viewFinder.post { startCamera() }
        } else {
            ActivityCompat.requestPermissions(this, REQUIRED_PERMISSIONS, REQUEST_CODE_PERMISSIONS)
        }
        viewFinder.addOnLayoutChangeListener { _, _, _, _, _, _, _, _, _ ->
            updateTransform()
        }
    }

    private fun startCamera() {
        // TODO
    }

    private fun updateTransform() {
        // TODO
    }

    override fun onRequestPermissionsResult(requestCode: Int, permissions: Array<out String>, grantResults: IntArray) {
        if (requestCode == REQUEST_CODE_PERMISSIONS) {
            if (allPermissionsGranted()) {
                viewFinder.post { startCamera() }
            } else {
                Toast.makeText(this, "Permissions not grated by the user.", Toast.LENGTH_SHORT).show()
                finish()
            }
        }
    }

    private fun allPermissionsGranted(): Boolean {
        for (permission in REQUIRED_PERMISSIONS) {
            if (ContextCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
                return false
            }
        }
        return true
    }
}

```

## 6. Implement view finder

さていよいよCmaeraXを使ってViewFinderを実装します。

> A viewfinder can be implemented by using the CameraX `Preview` class.

`Preview`というクラスを使って実装するようです。

Previewクラスを使うためには、まずconfigを定義する必要があります。
そのconfigからpreviewインスタンスを生成し、CameraXのlifecycleにpreviewインスタンスをバインドする必要があります。

```kotlin
private fun startCamera() {
    val previewConfig = PreviewConfig.Builder().apply {
        setTargetAspectRatio(Rationl(1, 1))
        setTargetResolution(Size(640, 640))
    }.build()

    val preview = Preview(previewConfig)

    preview.setOnPreviewOutputUpdateListener {
        viewFinder.surfaceTexture = it.surfaceTexture
        updateTransform()
    }

    CameraX.bindToLifecycle(this, preview)
}
```

注意)
`Type mismatch: inferred type is MainActivity but LifecycleOwner! was expected`
というエラーが出るかもしれないが、そのときはclean・rebuildをやってみてとのこと。またappcompatのバージョンが最新かどうか確認してねとのこと。

次にupdateTranform()についてです。
ここではViewFinderを縦でちゃんとみれるようにデバイスの向きを補正してあげます。

実装は次の通り

```kotlin
private fun updateTransform() {
    val matrix = Matrix()
    val centerX = viewFinder.width / 2f
    val centerY = viewFinder.height / 2f
    val rotationDegrees = when (viewFinder.display.rotation) {
        Surface.ROTATION_0 -> 0
        Surface.ROTATION_90 -> 90
        Surface.ROTATION_180 -> 180
        Surface.ROTATION_270 -> 270
        else -> return
    }
    matrix.postRotate(-rotationDegrees.toFloat(), centerX, centerY)
    viewFinder.setTransform(matrix)
}
```

プロダクションレベルの実装は[official sample] (https://github.com/googlesamples/cameraxbasic)をちゃんと見てねと。

ここで実行します。

## 7. Implement image capture use case


カメラ撮影するために、ボタンを用意します。

```xml
ImageButton
        android:id="@+id/capture_button"
        android:layout_width="72dp"
        android:layout_height="72dp"
        android:layout_margin="24dp"
        app:srcCompat="@android:drawable/ic_menu_camera"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />
```

次にカメラボタンが押されたファイルに保存する処理を実装しますが、Previewのときと同じような感じで、Configを作り `ImaageCapture`インスタンス(`imageCapture`)を生成します。
これをCameraXのlifecycleにバインドします。

残りは、カメラボタンが押されたときカメラ撮影するという処理ですが、`imageCapture`に`takePicture`というメソッドがあるのでそれを呼ぶだけす。

```kotlin
val imageCaptureConfig = ImageCaptuureConfig.Builder().apply {
    setTargetAspectRatio(Rationl(1, 1))
    setCaptureMode(ImageCapture.CaptureMode.MIN_LATENCY)
}.build()
val imageCapture = ImageCapture(imageCaptureConfig)
findViewById<ImageButton>(R.id.capture_button).setOnClickListener {
    val file = File(externalCacheDirs.first(), "${System.currentTimeMill
    imageCapture.takePicture(file,
        object : ImageCapture.OnImageSavaedListener {
            override fun onError(
                error: ImageCapture.UseCaseError,
                message: String, exc: Throwable?
            ) {
                val msg = "Photo capture failed: $message"
                Toast.makeText(baseContext, msg, Toast.LENGTH_SHORT).sho
                Log.e("CameraXApp", msg)
                exc?.printStackTrace()
            }
            override fun onImageSaved(file: File) {
                val msg = "Photo capture succeeded: ${file.absolutePath}
                Toast.makeText(baseContext, msg, Toast.LENGTH_SHORT).sho
                Log.d("CameraXApp", msg)
            }
        })
}
CameraX.bindToLifecycle(this, preview, imageCapture)
```

## 8. Implement image analysis use case

`ImageAnalysis.Analyzer`インターフェースを実装するカスタムクラスを定義することができます。カメラセッションの状態や画像を破棄することを心配する必要はありません。

`ImageAnalysis.Analyzer` を実装したクラスを作成


```kotlin
private class LuminosityAnalyzer : ImageAnalysis.Analyzer {
    private var lastAnalyzedTimestamp = 0L

    /**
     * Helper extension function used to extract a byte array from an
     * image plane buffer
     */
    private fun ByteBuffer.toByteArray(): ByteArray {
        rewind()    // Rewind the buffer to zero
        val data = ByteArray(remaining())
        get(data)   // Copy the buffer into a byte array
        return data // Return the byte array
    }

    override fun analyze(image: ImageProxy, rotationDegrees: Int) {
        val currentTimestamp = System.currentTimeMillis()
        // Calculate the average luma no more often than every second
        if (currentTimestamp - lastAnalyzedTimestamp >=
            TimeUnit.SECONDS.toMillis(1)) {
            // Since format in ImageAnalysis is YUV, image.planes[0]
            // contains the Y (luminance) plane
            val buffer = image.planes[0].buffer
            // Extract image data from callback object
            val data = buffer.toByteArray()
            // Convert the data into an array of pixel values
            val pixels = data.map { it.toInt() and 0xFF }
            // Compute average luminance for the image
            val luma = pixels.average()
            // Log the new luma value
            Log.d("CameraXApp", "Average luminosity: $luma")
            // Update timestamp of last analyzed frame
            lastAnalyzedTimestamp = currentTimestamp
        }
    }
}
```

このクラスを使って他のusecaseと同様にインスタンス化します。

```kotlin
val analyzerConfig = ImageAnalysisConfig.Builder().apply {
    val anaalyzerThread = HandlerThread(
        "LuminosityAnalysis").apply { start() }
    setCallbackHandler(Handler(anaalyzerThread.looper))
    setImageReaaderMode(
        ImageAnalysis.ImageReaderMode.ACQIRE_LATEST_IMAGE
    )
}.build()
val analyzerUseCase = ImageAnalysis(analyzerConfig).apply {
    analyzer = LuminosityAnalyzer()
}
CameraX.bindToLifecycle(this, preview, imageCapture, analyzerUseCase)
```

これを実行すると、毎秒、次が出力されます。

```
D/CameraXApp: Average luminosity: ...
```

