# SUNTEK Unified SDK User Guide

## Table of Contents

- [Introduction](#introduction)
- [Quick Start](#quick-start)
- [Integration](#integration)
- [API Reference](#api-reference)
- [Usage Examples](#usage-examples)
- [Device Support](#device-support)
- [FAQ](#faq)
- [Notes](#notes)

---

## Introduction

SUNTEK Unified SDK is a unified interface library that encapsulates APIs for multiple device types. Developers only need to use one set of APIs to adapt to different device models. The SDK automatically selects the corresponding underlying implementation based on the current device model, without requiring developers to worry about device differences.

### Key Features

- **Unified Interface**: One set of APIs adapts to all supported devices
- **Auto Adaptation**: Automatically selects underlying implementation based on device model
- **Rich Features**: Supports device information retrieval, camera control, QR code scanning, and more
- **Easy Integration**: Simple initialization process, clear API design

### Supported Devices

  
- SK8 (3576SE)
- FS8 / FS10
- SC1 / 
- FA8 / FA10

---

## Quick Start

### 1. Get SDK Instance

```java
import com.suntek.mylibrary.InitSDK;
import com.suntek.mylibrary.IsdkAPI;

// Initialize in Activity or Application
public class MainActivity extends AppCompatActivity {
    private IsdkAPI sdk;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        // Automatically detect device and get corresponding SDK instance
        sdk = InitSDK.getSDK(this);
        
        if (sdk != null) {
            // SDK is automatically initialized, ready to use
            String deviceInfo = sdk.getDeviceInfo();
            Log.d("SDK", "Device Info: " + deviceInfo);
        } else {
            Log.e("SDK", "Current device is not supported or initialization failed");
        }
    }
}
```

### 2. Check Device Support

```java
// Check if current device is supported
if (InitSDK.isCurrentDeviceSupported()) {
    // Device is supported, can use SDK
    DeviceType deviceType = InitSDK.getCurrentDeviceType();
    Log.d("SDK", "Current device type: " + deviceType.getDescription());
} else {
    // Device is not supported
    Log.w("SDK", "Current device is not supported");
}
```

---

## Integration

### Gradle Dependency

Add dependency in your project's `build.gradle` file:

```gradle
dependencies {
    // Add unified SDK AAR
    implementation('com.github.SUNTEK-DEV:SUNTEKCustomerAPISDK:4.0')
    
    // Or use Maven repository (if configured)
    // implementation 'com.suntek:unified-sdk:1.0.0'
}
```

### Permission Configuration

Add necessary permissions in `AndroidManifest.xml`:

```xml
<manifest>
    <!-- Camera permission -->
    <uses-permission android:name="android.permission.CAMERA" />
    
    <!-- Storage permission (for saving photos) -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    
    <!-- Network permission (if needed) -->
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```

---

## API Reference

### InitSDK Class

Entry class for SDK, provides static methods to get SDK instances.

#### Method List

| Method | Description | Return Value |
|--------|-------------|--------------|
| `getSDK(Context context)` | Automatically detect device and get SDK instance | `IsdkAPI` |
| `getSDK(Context context, DeviceType deviceType)` | Get SDK instance based on specified device type | `IsdkAPI` |
| `isCurrentDeviceSupported()` | Check if current device is supported | `boolean` |
| `getCurrentDeviceType()` | Get current device type | `DeviceType` |
| `getSupportedDevices()` | Get all supported device types | `DeviceType[]` |

### IsdkAPI Interface

Unified SDK API interface, all device implementations follow this interface.

#### Initialization and Status

##### `void init(Context context)`
Initialize SDK.

**Parameters:**
- `context`: Android context object

**Note:** This method is automatically called when SDK is obtained through `InitSDK.getSDK()`, usually no need to call manually.

---

##### `boolean checkSystemStatus()`
Check system status.

**Return Value:** `true` means system is normal, `false` means system is abnormal

**Example:**
```java
if (sdk.checkSystemStatus()) {
    Log.d("SDK", "System status is normal");
} else {
    Log.w("SDK", "System status is abnormal");
}
```

---

#### Device Information

##### `String getDeviceInfo()`
Get device information (including serial number, etc.).

**Return Value:** Device information string

**Example:**
```java
String deviceInfo = sdk.getDeviceInfo();
Log.d("SDK", "Device Info: " + deviceInfo);
```

---

##### `String getDeviceModel()`
Get device model.

**Return Value:** Device model string (e.g., , "SK8", etc.)

**Example:**
```java
String model = sdk.getDeviceModel();
Log.d("SDK", "Device Model: " + model);
```

---

##### `String getSerialNumber()`
Get device serial number.

**Return Value:** Device serial number string

**Example:**
```java
String serialNumber = sdk.getSerialNumber();
Log.d("SDK", "Serial Number: " + serialNumber);
```

---

##### `String getFirmwareVersion()`
Get firmware version.

**Return Value:** Firmware version string

**Example:**
```java
String version = sdk.getFirmwareVersion();
Log.d("SDK", "Firmware Version: " + version);
```

---

##### `String getMACAddress()`
Get MAC address.

**Return Value:** MAC address string

**Example:**
```java
String macAddress = sdk.getMACAddress();
Log.d("SDK", "MAC Address: " + macAddress);
```

---

#### Camera Control

##### `boolean isCameraAvailable()`
Check if camera is available.

**Return Value:** `true` means camera is available, `false` means unavailable

**Example:**
```java
if (sdk.isCameraAvailable()) {
    // Camera is available, can perform camera operations
    sdk.openCamera();
}
```

---

##### `boolean openCamera()`
Open camera.

**Return Value:** `true` means open succeeded, `false` means open failed

**Example:**
```java
if (sdk.openCamera()) {
    Log.d("SDK", "Camera opened successfully");
} else {
    Log.e("SDK", "Camera open failed");
}
```

---

##### `boolean closeCamera()`
Close camera.

**Return Value:** `true` means close succeeded, `false` means close failed

**Example:**
```java
if (sdk.closeCamera()) {
    Log.d("SDK", "Camera closed successfully");
}
```

---

##### `void takePicture(CaptureCallback callback)`
Take a picture.

**Parameters:**
- `callback`: Picture capture result callback interface

**Callback Interface:**
```java
interface CaptureCallback {
    void onCaptureSuccess(String imagePath);  // Picture taken successfully, returns image path
    void onCaptureFailure(String error);      // Picture taking failed, returns error message
}
```

**Example:**
```java
sdk.takePicture(new IsdkAPI.CaptureCallback() {
    @Override
    public void onCaptureSuccess(String imagePath) {
        Log.d("SDK", "Picture taken successfully, image path: " + imagePath);
        // Process image
        Bitmap bitmap = BitmapFactory.decodeFile(imagePath);
        imageView.setImageBitmap(bitmap);
    }
    
    @Override
    public void onCaptureFailure(String error) {
        Log.e("SDK", "Picture taking failed: " + error);
        Toast.makeText(context, "Picture taking failed: " + error, Toast.LENGTH_SHORT).show();
    }
});
```

---

##### `boolean setAutoFocus(boolean enable)`
Set auto focus.

**Parameters:**
- `enable`: `true` to enable auto focus, `false` to disable

**Return Value:** `true` means setting succeeded, `false` means setting failed

**Example:**
```java
if (sdk.setAutoFocus(true)) {
    Log.d("SDK", "Auto focus enabled");
}
```

---

#### QR Code Scanning

##### `void startQRCodeScan(QRCodeCallback callback)`
Start QR code scanning.

**Parameters:**
- `callback`: Scan result callback interface

**Callback Interface:**
```java
interface QRCodeCallback {
    void onQRCodeDetected(String result);  // QR code detected, returns result
    void onScanError(String error);         // Scan error, returns error message
}
```

**Example:**
```java
sdk.startQRCodeScan(new IsdkAPI.QRCodeCallback() {
    @Override
    public void onQRCodeDetected(String result) {
        Log.d("SDK", "QR code detected: " + result);
        // Process scan result
        textView.setText(result);
        // Stop scanning
        sdk.stopQRCodeScan();
    }
    
    @Override
    public void onScanError(String error) {
        Log.e("SDK", "Scan error: " + error);
        Toast.makeText(context, "Scan error: " + error, Toast.LENGTH_SHORT).show();
    }
});
```

---

##### `void stopQRCodeScan()`
Stop QR code scanning.

**Example:**
```java
sdk.stopQRCodeScan();
Log.d("SDK", "QR code scanning stopped");
```

---

#### Screen Control

##### `boolean setBrightness(int brightness)`
Set screen brightness.

**Parameters:**
- `brightness`: Brightness value (usually range 0-255)

**Return Value:** `true` means setting succeeded, `false` means setting failed

**Example:**
```java
// Set brightness to 50%
if (sdk.setBrightness(128)) {
    Log.d("SDK", "Screen brightness set successfully");
}
```

---

#### Feature Query

##### `String[] getSupportedFeatures()`
Get list of features supported by current device.

**Return Value:** Array of feature names

**Example:**
```java
String[] features = sdk.getSupportedFeatures();
Log.d("SDK", "Supported features:");
for (String feature : features) {
    Log.d("SDK", "  - " + feature);
}
```

---

#### Resource Release

##### `void release()`
Release SDK resources. Call this when SDK is no longer needed, usually in Activity's `onDestroy()`.

**Example:**
```java
@Override
protected void onDestroy() {
    super.onDestroy();
    if (sdk != null) {
        sdk.release();
        sdk = null;
    }
}
```

---

## Usage Examples

### Complete Example: Device Information Retrieval

```java
public class DeviceInfoActivity extends AppCompatActivity {
    private IsdkAPI sdk;
    private TextView infoTextView;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_device_info);
        
        infoTextView = findViewById(R.id.info_text);
        
        // Get SDK instance
        sdk = InitSDK.getSDK(this);
        
        if (sdk != null) {
            displayDeviceInfo();
        } else {
            infoTextView.setText("Device not supported or initialization failed");
        }
    }
    
    private void displayDeviceInfo() {
        StringBuilder info = new StringBuilder();
        info.append("Device Model: ").append(sdk.getDeviceModel()).append("\n");
        info.append("Serial Number: ").append(sdk.getSerialNumber()).append("\n");
        info.append("Firmware Version: ").append(sdk.getFirmwareVersion()).append("\n");
        info.append("MAC Address: ").append(sdk.getMACAddress()).append("\n");
        info.append("System Status: ").append(sdk.checkSystemStatus() ? "Normal" : "Abnormal").append("\n");
        
        info.append("\nSupported Features:\n");
        String[] features = sdk.getSupportedFeatures();
        for (String feature : features) {
            info.append("  - ").append(feature).append("\n");
        }
        
        infoTextView.setText(info.toString());
    }
    
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (sdk != null) {
            sdk.release();
        }
    }
}
```

---

### Complete Example: Camera Capture

```java
public class CameraActivity extends AppCompatActivity {
    private IsdkAPI sdk;
    private ImageView previewImageView;
    private Button captureButton;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_camera);
        
        previewImageView = findViewById(R.id.preview_image);
        captureButton = findViewById(R.id.capture_button);
        
        // Get SDK instance
        sdk = InitSDK.getSDK(this);
        
        if (sdk == null || !sdk.isCameraAvailable()) {
            Toast.makeText(this, "Camera unavailable", Toast.LENGTH_SHORT).show();
            finish();
            return;
        }
        
        // Open camera
        if (!sdk.openCamera()) {
            Toast.makeText(this, "Camera open failed", Toast.LENGTH_SHORT).show();
            finish();
            return;
        }
        
        // Enable auto focus
        sdk.setAutoFocus(true);
        
        // Set capture button click event
        captureButton.setOnClickListener(v -> takePicture());
    }
    
    private void takePicture() {
        captureButton.setEnabled(false);
        captureButton.setText("Capturing...");
        
        sdk.takePicture(new IsdkAPI.CaptureCallback() {
            @Override
            public void onCaptureSuccess(String imagePath) {
                runOnUiThread(() -> {
                    // Display captured photo
                    Bitmap bitmap = BitmapFactory.decodeFile(imagePath);
                    previewImageView.setImageBitmap(bitmap);
                    
                    captureButton.setEnabled(true);
                    captureButton.setText("Capture");
                    
                    Toast.makeText(CameraActivity.this, 
                        "Picture taken successfully: " + imagePath, Toast.LENGTH_SHORT).show();
                });
            }
            
            @Override
            public void onCaptureFailure(String error) {
                runOnUiThread(() -> {
                    captureButton.setEnabled(true);
                    captureButton.setText("Capture");
                    
                    Toast.makeText(CameraActivity.this, 
                        "Picture taking failed: " + error, Toast.LENGTH_SHORT).show();
                });
            }
        });
    }
    
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (sdk != null) {
            sdk.closeCamera();
            sdk.release();
        }
    }
}
```

---

### Complete Example: QR Code Scanning

```java
public class QRCodeScanActivity extends AppCompatActivity {
    private IsdkAPI sdk;
    private TextView resultTextView;
    private Button startScanButton;
    private Button stopScanButton;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_qrcode_scan);
        
        resultTextView = findViewById(R.id.result_text);
        startScanButton = findViewById(R.id.start_scan_button);
        stopScanButton = findViewById(R.id.stop_scan_button);
        
        // Get SDK instance
        sdk = InitSDK.getSDK(this);
        
        if (sdk == null) {
            Toast.makeText(this, "SDK initialization failed", Toast.LENGTH_SHORT).show();
            finish();
            return;
        }
        
        startScanButton.setOnClickListener(v -> startScan());
        stopScanButton.setOnClickListener(v -> stopScan());
        
        stopScanButton.setEnabled(false);
    }
    
    private void startScan() {
        startScanButton.setEnabled(false);
        stopScanButton.setEnabled(true);
        resultTextView.setText("Scanning...");
        
        sdk.startQRCodeScan(new IsdkAPI.QRCodeCallback() {
            @Override
            public void onQRCodeDetected(String result) {
                runOnUiThread(() -> {
                    resultTextView.setText("Scan Result: " + result);
                    stopScan();
                    
                    // Process scan result here
                    handleScanResult(result);
                });
            }
            
            @Override
            public void onScanError(String error) {
                runOnUiThread(() -> {
                    resultTextView.setText("Scan Error: " + error);
                    stopScan();
                });
            }
        });
    }
    
    private void stopScan() {
        sdk.stopQRCodeScan();
        startScanButton.setEnabled(true);
        stopScanButton.setEnabled(false);
    }
    
    private void handleScanResult(String result) {
        // Process scan result, e.g., open URL, parse data, etc.
        Log.d("QRCode", "Scan Result: " + result);
    }
    
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (sdk != null) {
            sdk.stopQRCodeScan();
            sdk.release();
        }
    }
}
```

---

## Device Support

### Device Type Enumeration

SDK defines supported device types through `DeviceType` enumeration:

```java
public enum DeviceType {
    FS8("FS8", "FS8 device"),
    FS10("FS10", "FS10 device"),
    SK8("3576SE", "SK8 device"),
    SC1("SC1", "SC1 device"),
    FA8("FA8", "FA8 device"),
    FA10("FA10", "FA10 device"),
    LP("", "SC1 device"),
    UNKNOWN("UNKNOWN", "Unknown device");
}
```

### Device Detection Mechanism

SDK automatically detects device model through `Build.MODEL` and selects corresponding implementation:

1. **Auto Detection**: When calling `InitSDK.getSDK(context)`, SDK automatically reads `Build.MODEL`
2. **Device Matching**: Match device model with `DeviceType` enumeration
3. **Implementation Selection**: `SDKFactory` creates corresponding SDK implementation instance based on device type
4. **Auto Initialization**: Automatically calls `init()` method after creating instance

### Manually Specify Device Type

If you need to manually specify device type (e.g., for testing), you can use:

```java
// Manually specify device type
IsdkAPI sdk = InitSDK.getSDK(context, DeviceType.SK8);
```

---

## FAQ

### Q1: SDK initialization returns null

**Possible Causes:**
1. Current device model is not in the support list
2. Device model detection failed
3. Underlying SDK initialization failed

**Solution:**
```java
// Check if device is supported
if (!InitSDK.isCurrentDeviceSupported()) {
    Log.e("SDK", "Current device is not supported");
    DeviceType currentType = InitSDK.getCurrentDeviceType();
    Log.d("SDK", "Detected device type: " + currentType);
    return;
}

// Get SDK instance
IsdkAPI sdk = InitSDK.getSDK(context);
if (sdk == null) {
    Log.e("SDK", "SDK initialization failed, please check logs");
}
```

---

### Q2: Camera function unavailable

**Possible Causes:**
1. Device has no camera hardware
2. Camera permission not granted
3. Camera is occupied by another app

**Solution:**
```java
// Check if camera is available
if (!sdk.isCameraAvailable()) {
    Log.w("SDK", "Camera unavailable");
    return;
}

// Check permission
if (ContextCompat.checkSelfPermission(context, Manifest.permission.CAMERA) 
    != PackageManager.PERMISSION_GRANTED) {
    // Request permission
    ActivityCompat.requestPermissions(activity, 
        new String[]{Manifest.permission.CAMERA}, REQUEST_CAMERA_PERMISSION);
    return;
}

// Open camera
if (!sdk.openCamera()) {
    Log.e("SDK", "Camera open failed");
}
```

---

### Q3: How to determine which device implementation is currently being used?

**Solution:**
```java
// Method 1: Determine through device model
String model = sdk.getDeviceModel();
Log.d("SDK", "Current device model: " + model);

// Method 2: Determine through device type
DeviceType deviceType = InitSDK.getCurrentDeviceType();
Log.d("SDK", "Current device type: " + deviceType.getDescription());
```

---

### Q4: Capture callback not triggered

**Possible Causes:**
1. Callback object is null
2. Camera not properly opened
3. Underlying SDK exception

**Solution:**
```java
// Ensure camera is opened
if (!sdk.isCameraAvailable() || !sdk.openCamera()) {
    Log.e("SDK", "Camera unavailable or open failed");
    return;
}

// Ensure callback is not null
IsdkAPI.CaptureCallback callback = new IsdkAPI.CaptureCallback() {
    @Override
    public void onCaptureSuccess(String imagePath) {
        // Handle success
    }
    
    @Override
    public void onCaptureFailure(String error) {
        // Handle failure
    }
};

// Take picture
sdk.takePicture(callback);
```

---

### Q5: QR code scanning cannot detect anything

**Possible Causes:**
1. QR code not in camera view
2. Insufficient light
3. QR code is blurry or damaged

**Solution:**
```java
// Ensure camera is opened
if (!sdk.openCamera()) {
    Log.e("SDK", "Camera open failed");
    return;
}

// Start scanning
sdk.startQRCodeScan(new IsdkAPI.QRCodeCallback() {
    @Override
    public void onQRCodeDetected(String result) {
        // Scan successful, automatically stop
        sdk.stopQRCodeScan();
    }
    
    @Override
    public void onScanError(String error) {
        Log.e("SDK", "Scan error: " + error);
    }
});
```

---

## Notes

### 1. Lifecycle Management

- **Initialization Timing**: Recommend initializing SDK in `onCreate()`
- **Resource Release**: Must call `release()` in `onDestroy()` to release resources
- **Camera Management**: Call `closeCamera()` promptly after using camera

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    sdk = InitSDK.getSDK(this);
}

@Override
protected void onDestroy() {
    super.onDestroy();
    if (sdk != null) {
        sdk.closeCamera();
        sdk.stopQRCodeScan();
        sdk.release();
        sdk = null;
    }
}
```

---

### 2. Thread Safety

- SDK callback methods may execute in worker threads, use `runOnUiThread()` or Handler when updating UI
- All SDK method calls are recommended to be performed on the main thread

```java
sdk.takePicture(new IsdkAPI.CaptureCallback() {
    @Override
    public void onCaptureSuccess(String imagePath) {
        // Callback may be in non-main thread, need to switch to main thread to update UI
        runOnUiThread(() -> {
            imageView.setImageBitmap(BitmapFactory.decodeFile(imagePath));
        });
    }
    
    @Override
    public void onCaptureFailure(String error) {
        runOnUiThread(() -> {
            Toast.makeText(context, error, Toast.LENGTH_SHORT).show();
        });
    }
});
```

---

### 3. Permission Handling

- Ensure required permissions are declared in `AndroidManifest.xml`
- Runtime permissions (Android 6.0+) need to be dynamically requested in code

```java
// Check and request camera permission
if (ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA) 
    != PackageManager.PERMISSION_GRANTED) {
    ActivityCompat.requestPermissions(this, 
        new String[]{Manifest.permission.CAMERA}, 
        REQUEST_CAMERA_PERMISSION);
}
```

---

### 4. Error Handling

- All SDK method calls should perform error checking
- Use try-catch to catch possible exceptions
- Log for debugging purposes

```java
try {
    if (sdk != null && sdk.checkSystemStatus()) {
        String info = sdk.getDeviceInfo();
        // Process device information
    }
} catch (Exception e) {
    Log.e("SDK", "Failed to get device information", e);
    Toast.makeText(context, "Operation failed: " + e.getMessage(), Toast.LENGTH_SHORT).show();
}
```

---

### 5. Device Compatibility

- Different devices may have different feature support, recommend calling `getSupportedFeatures()` to query before use
- Some devices may not support certain features, compatibility handling is needed

```java
String[] features = sdk.getSupportedFeatures();
boolean hasQRCode = Arrays.asList(features).contains("QR Code Scanning");

if (hasQRCode) {
    // Use QR code scanning feature
    sdk.startQRCodeScan(callback);
} else {
    Toast.makeText(context, "Current device does not support QR code scanning", Toast.LENGTH_SHORT).show();
}
```

---

### 6. Performance Optimization

- SDK instances are recommended to be used as singletons, avoid repeated creation
- Release resources promptly after using camera and scanning features
- Avoid creating SDK instances in frequently called methods

```java
public class MyApplication extends Application {
    private static IsdkAPI sdkInstance;
    
    @Override
    public void onCreate() {
        super.onCreate();
        sdkInstance = InitSDK.getSDK(this);
    }
    
    public static IsdkAPI getSDK() {
        return sdkInstance;
    }
}
```

---

## Version History

### v1.0.0 (2025-12-27)
- Initial version release
- Support for  SK8 and other devices
- Implementation of basic features such as device information retrieval, camera control, QR code scanning

---

## Technical Support

For questions or suggestions, please contact:

- **Technical Support Email**: xdn@suntek.cloud
- **Document Version**: v1.0.0
- **Last Updated**: 2025-12-27

---

## License

Use of this document and SDK is subject to the relevant license agreement.

---

*This document is based on SUNTEK Unified SDK v1.0.0*

