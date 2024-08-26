# Real-Time Object Detectior

## Project Overview

This Android app utilizes Kotlin, YoloV8, and TensorFlow Lite for real-time object detection and tracking. It analyzes the live camera feed to overlay bounding boxes on detected objects, including cars, keyboards, and other real-time objects.

## Requirements

- **Minimum SDK Version**: 21
- **Target SDK Version**: 34

## Tech Stack

- **Language**: Kotlin
- **Model**: TensorFlow Lite (`ssd_mobilenet_v1_1_metadata_1.tflite` created using YoloV8)
- **UI**: XML

## Setup and Installation

1. **Clone the Repository**

   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. **Open the Project**

   Open the project in Android Studio.

3. **Add Dependencies**

   Ensure you have the following dependencies in your `build.gradle`:

   ```groovy
   implementation 'org.tensorflow:tensorflow-lite:2.10.0'
   implementation 'org.tensorflow:tensorflow-lite-support:0.4.0'
   ```

4. **Add TensorFlow Lite Model**

   Place your TensorFlow Lite model (`ssd_mobilenet_v1_1_metadata_1.tflite`) in the `assets` directory.

5. **Add Labels**

   Place the labels file (`labels.txt`) in the `assets` directory.

## Code Overview

The main components of the app include:

### `MainActivity.kt`

- **Permissions**: The app requests camera permissions if not already granted.
- **Camera Initialization**: Opens the camera and sets up a preview using `TextureView`.
- **Model Loading**: Loads the TensorFlow Lite model (`SsdMobilenetV11Metadata1`) and processes images.
- **Image Processing**: Captures frames from the camera feed, processes them using the model, and overlays bounding boxes and labels on detected objects.

### Key Sections in the Code

- **Permission Handling**

   ```kotlin
   @SuppressLint("MissingPermission")
   private fun getPermission() {
       if (ContextCompat.checkSelfPermission(
               this,
               Manifest.permission.CAMERA
           ) != PackageManager.PERMISSION_GRANTED
       ) {
           requestPermissions(arrayOf(Manifest.permission.CAMERA), 101)
       }
   }
   ```

- **Camera Setup**

   ```kotlin
   @SuppressLint("MissingPermission")
   private fun cameraOpen() {
       cameraManager.openCamera("0", object : CameraDevice.StateCallback() {
           // Implementation for camera opening and preview setup
       }, handler)
   }
   ```

- **Object Detection and Drawing**

   ```kotlin
   override fun onSurfaceTextureUpdated(p0: SurfaceTexture) {
       bitmap = binding.textureView.bitmap!!
       val image = TensorImage.fromBitmap(bitmap)
       imageProcessor.process(image)

       val outputs = model.process(image)
       val locations = outputs.locationsAsTensorBuffer.floatArray
       val classes = outputs.classesAsTensorBuffer.floatArray
       val scores = outputs.scoresAsTensorBuffer.floatArray
       val numberOfDetections = outputs.numberOfDetectionsAsTensorBuffer.floatArray

       val mutable = bitmap.copy(Bitmap.Config.ARGB_8888, true)
       val canvas = Canvas(mutable)

       scores.forEachIndexed { index, score ->
           if (score > 0.5) {
               paint.color = colors[index]
               paint.style = Paint.Style.STROKE
               canvas.drawRect(RectF(locations[index * 4 + 1] * bitmap.width,
                                      locations[index * 4] * bitmap.height,
                                      locations[index * 4 + 3] * bitmap.width,
                                      locations[index * 4 + 2] * bitmap.height), paint)
               paint.style = Paint.Style.FILL
               canvas.drawText(labels[classes[index].toInt()] + " " + score.toString(),
                               locations[index * 4 + 1] * bitmap.width,
                               locations[index * 4] * bitmap.height, paint)
           }
       }
       binding.imageView.setImageBitmap(mutable)
   }
   ```

## Contribution

Contributions are welcome! Please fork the repository and submit a pull request with your changes. Ensure your code adheres to the project's coding standards and includes relevant tests.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.

---

Feel free to adjust any parts of this README according to your specific needs or preferences.
