# SUNTEK 统一 SDK 使用指南

## 目录

- [简介](#简介)
- [快速开始](#快速开始)
- [集成方式](#集成方式)
- [API 参考](#api-参考)
- [使用示例](#使用示例)
- [设备支持](#设备支持)
- [常见问题](#常见问题)
- [注意事项](#注意事项)

---

## 简介

SUNTEK 统一 SDK 是一个封装了多种设备 API 的统一接口库，开发者只需使用一套 API 即可适配不同的设备型号。SDK 会根据当前设备型号自动选择对应的底层实现，无需开发者关心设备差异。

### 主要特性

- **统一接口**：一套 API 适配所有支持的设备
- **自动适配**：根据设备型号自动选择底层实现
- **功能丰富**：支持设备信息获取、相机控制、二维码扫描等功能
- **易于集成**：简单的初始化流程，清晰的 API 设计

### 支持的设备

  
- SK8 (3576SE)
- FS8 / FS10
- SC1 / 
- FA8 / FA10

---

## 快速开始

### 1. 获取 SDK 实例

```java
import com.suntek.mylibrary.InitSDK;
import com.suntek.mylibrary.IsdkAPI;

// 在 Activity 或 Application 中初始化
public class MainActivity extends AppCompatActivity {
    private IsdkAPI sdk;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        
        // 自动检测设备并获取对应的 SDK 实例
        sdk = InitSDK.getSDK(this);
        
        if (sdk != null) {
            // SDK 已自动初始化，可以直接使用
            String deviceInfo = sdk.getDeviceInfo();
            Log.d("SDK", "设备信息: " + deviceInfo);
        } else {
            Log.e("SDK", "当前设备不支持或初始化失败");
        }
    }
}
```

### 2. 检查设备支持

```java
// 检查当前设备是否受支持
if (InitSDK.isCurrentDeviceSupported()) {
    // 设备受支持，可以使用 SDK
    DeviceType deviceType = InitSDK.getCurrentDeviceType();
    Log.d("SDK", "当前设备类型: " + deviceType.getDescription());
} else {
    // 设备不受支持
    Log.w("SDK", "当前设备不受支持");
}
```

---

## 集成方式

### Gradle 依赖

在项目的 `build.gradle` 文件中添加依赖：

```gradle
dependencies {
    // 添加统一 SDK AAR
    api implementation('com.github.SUNTEK-DEV:SUNTEKCustomerAPISDK:4.0')
    
    // 或者使用 Maven 仓库（如果已配置）
    // implementation 'com.suntek:unified-sdk:1.0.0'
}
```

### 权限配置

在 `AndroidManifest.xml` 中添加必要的权限：

```xml
<manifest>
    <!-- 相机权限 -->
    <uses-permission android:name="android.permission.CAMERA" />
    
    <!-- 存储权限（用于保存照片） -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    
    <!-- 网络权限（如果需要） -->
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```

---

## API 参考

### InitSDK 类

SDK 的入口类，提供获取 SDK 实例的静态方法。

#### 方法列表

| 方法 | 说明 | 返回值 |
|------|------|--------|
| `getSDK(Context context)` | 自动检测设备并获取 SDK 实例 | `IsdkAPI` |
| `getSDK(Context context, DeviceType deviceType)` | 根据指定设备类型获取 SDK 实例 | `IsdkAPI` |
| `isCurrentDeviceSupported()` | 检查当前设备是否受支持 | `boolean` |
| `getCurrentDeviceType()` | 获取当前设备类型 | `DeviceType` |
| `getSupportedDevices()` | 获取所有支持的设备类型 | `DeviceType[]` |

### IsdkAPI 接口

统一 SDK API 接口，所有设备实现都遵循此接口。

#### 初始化与状态

##### `void init(Context context)`
初始化 SDK。

**参数：**
- `context`: Android 上下文对象

**说明：** SDK 通过 `InitSDK.getSDK()` 获取时会自动调用此方法，通常无需手动调用。

---

##### `boolean checkSystemStatus()`
检查系统状态。

**返回值：** `true` 表示系统正常，`false` 表示系统异常

**示例：**
```java
if (sdk.checkSystemStatus()) {
    Log.d("SDK", "系统状态正常");
} else {
    Log.w("SDK", "系统状态异常");
}
```

---

#### 设备信息

##### `String getDeviceInfo()`
获取设备信息（包含序列号等）。

**返回值：** 设备信息字符串

**示例：**
```java
String deviceInfo = sdk.getDeviceInfo();
Log.d("SDK", "设备信息: " + deviceInfo);
```

---

##### `String getDeviceModel()`
获取设备型号。

**返回值：** 设备型号字符串（如 , "SK8" 等）

**示例：**
```java
String model = sdk.getDeviceModel();
Log.d("SDK", "设备型号: " + model);
```

---

##### `String getSerialNumber()`
获取设备序列号。

**返回值：** 设备序列号字符串

**示例：**
```java
String serialNumber = sdk.getSerialNumber();
Log.d("SDK", "序列号: " + serialNumber);
```

---

##### `String getFirmwareVersion()`
获取固件版本。

**返回值：** 固件版本字符串

**示例：**
```java
String version = sdk.getFirmwareVersion();
Log.d("SDK", "固件版本: " + version);
```

---

##### `String getMACAddress()`
获取 MAC 地址。

**返回值：** MAC 地址字符串

**示例：**
```java
String macAddress = sdk.getMACAddress();
Log.d("SDK", "MAC地址: " + macAddress);
```

---

#### 相机控制

##### `boolean isCameraAvailable()`
检查相机是否可用。

**返回值：** `true` 表示相机可用，`false` 表示不可用

**示例：**
```java
if (sdk.isCameraAvailable()) {
    // 相机可用，可以进行相机操作
    sdk.openCamera();
}
```

---

##### `boolean openCamera()`
开启相机。

**返回值：** `true` 表示开启成功，`false` 表示开启失败

**示例：**
```java
if (sdk.openCamera()) {
    Log.d("SDK", "相机开启成功");
} else {
    Log.e("SDK", "相机开启失败");
}
```

---

##### `boolean closeCamera()`
关闭相机。

**返回值：** `true` 表示关闭成功，`false` 表示关闭失败

**示例：**
```java
if (sdk.closeCamera()) {
    Log.d("SDK", "相机关闭成功");
}
```

---

##### `void takePicture(CaptureCallback callback)`
拍照。

**参数：**
- `callback`: 拍照结果回调接口

**回调接口：**
```java
interface CaptureCallback {
    void onCaptureSuccess(String imagePath);  // 拍照成功，返回图片路径
    void onCaptureFailure(String error);      // 拍照失败，返回错误信息
}
```

**示例：**
```java
sdk.takePicture(new IsdkAPI.CaptureCallback() {
    @Override
    public void onCaptureSuccess(String imagePath) {
        Log.d("SDK", "拍照成功，图片路径: " + imagePath);
        // 处理图片
        Bitmap bitmap = BitmapFactory.decodeFile(imagePath);
        imageView.setImageBitmap(bitmap);
    }
    
    @Override
    public void onCaptureFailure(String error) {
        Log.e("SDK", "拍照失败: " + error);
        Toast.makeText(context, "拍照失败: " + error, Toast.LENGTH_SHORT).show();
    }
});
```

---

##### `boolean setAutoFocus(boolean enable)`
设置自动对焦。

**参数：**
- `enable`: `true` 启用自动对焦，`false` 禁用

**返回值：** `true` 表示设置成功，`false` 表示设置失败

**示例：**
```java
if (sdk.setAutoFocus(true)) {
    Log.d("SDK", "自动对焦已启用");
}
```

---

#### 二维码扫描

##### `void startQRCodeScan(QRCodeCallback callback)`
开始二维码扫描。

**参数：**
- `callback`: 扫描结果回调接口

**回调接口：**
```java
interface QRCodeCallback {
    void onQRCodeDetected(String result);  // 扫描到二维码，返回结果
    void onScanError(String error);         // 扫描出错，返回错误信息
}
```

**示例：**
```java
sdk.startQRCodeScan(new IsdkAPI.QRCodeCallback() {
    @Override
    public void onQRCodeDetected(String result) {
        Log.d("SDK", "扫描到二维码: " + result);
        // 处理扫描结果
        textView.setText(result);
        // 停止扫描
        sdk.stopQRCodeScan();
    }
    
    @Override
    public void onScanError(String error) {
        Log.e("SDK", "扫描出错: " + error);
        Toast.makeText(context, "扫描出错: " + error, Toast.LENGTH_SHORT).show();
    }
});
```

---

##### `void stopQRCodeScan()`
停止二维码扫描。

**示例：**
```java
sdk.stopQRCodeScan();
Log.d("SDK", "已停止二维码扫描");
```

---

#### 屏幕控制

##### `boolean setBrightness(int brightness)`
设置屏幕亮度。

**参数：**
- `brightness`: 亮度值（通常范围 0-255）

**返回值：** `true` 表示设置成功，`false` 表示设置失败

**示例：**
```java
// 设置亮度为 50%
if (sdk.setBrightness(128)) {
    Log.d("SDK", "屏幕亮度设置成功");
}
```

---

#### 功能查询

##### `String[] getSupportedFeatures()`
获取当前设备支持的功能列表。

**返回值：** 功能名称数组

**示例：**
```java
String[] features = sdk.getSupportedFeatures();
Log.d("SDK", "支持的功能:");
for (String feature : features) {
    Log.d("SDK", "  - " + feature);
}
```

---

#### 资源释放

##### `void release()`
释放 SDK 资源。在不再使用 SDK 时调用，通常在 Activity 的 `onDestroy()` 中调用。

**示例：**
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

## 使用示例

### 完整示例：设备信息获取

```java
public class DeviceInfoActivity extends AppCompatActivity {
    private IsdkAPI sdk;
    private TextView infoTextView;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_device_info);
        
        infoTextView = findViewById(R.id.info_text);
        
        // 获取 SDK 实例
        sdk = InitSDK.getSDK(this);
        
        if (sdk != null) {
            displayDeviceInfo();
        } else {
            infoTextView.setText("设备不支持或初始化失败");
        }
    }
    
    private void displayDeviceInfo() {
        StringBuilder info = new StringBuilder();
        info.append("设备型号: ").append(sdk.getDeviceModel()).append("\n");
        info.append("序列号: ").append(sdk.getSerialNumber()).append("\n");
        info.append("固件版本: ").append(sdk.getFirmwareVersion()).append("\n");
        info.append("MAC地址: ").append(sdk.getMACAddress()).append("\n");
        info.append("系统状态: ").append(sdk.checkSystemStatus() ? "正常" : "异常").append("\n");
        
        info.append("\n支持的功能:\n");
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

### 完整示例：相机拍照

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
        
        // 获取 SDK 实例
        sdk = InitSDK.getSDK(this);
        
        if (sdk == null || !sdk.isCameraAvailable()) {
            Toast.makeText(this, "相机不可用", Toast.LENGTH_SHORT).show();
            finish();
            return;
        }
        
        // 开启相机
        if (!sdk.openCamera()) {
            Toast.makeText(this, "相机开启失败", Toast.LENGTH_SHORT).show();
            finish();
            return;
        }
        
        // 启用自动对焦
        sdk.setAutoFocus(true);
        
        // 设置拍照按钮点击事件
        captureButton.setOnClickListener(v -> takePicture());
    }
    
    private void takePicture() {
        captureButton.setEnabled(false);
        captureButton.setText("拍照中...");
        
        sdk.takePicture(new IsdkAPI.CaptureCallback() {
            @Override
            public void onCaptureSuccess(String imagePath) {
                runOnUiThread(() -> {
                    // 显示拍摄的照片
                    Bitmap bitmap = BitmapFactory.decodeFile(imagePath);
                    previewImageView.setImageBitmap(bitmap);
                    
                    captureButton.setEnabled(true);
                    captureButton.setText("拍照");
                    
                    Toast.makeText(CameraActivity.this, 
                        "拍照成功: " + imagePath, Toast.LENGTH_SHORT).show();
                });
            }
            
            @Override
            public void onCaptureFailure(String error) {
                runOnUiThread(() -> {
                    captureButton.setEnabled(true);
                    captureButton.setText("拍照");
                    
                    Toast.makeText(CameraActivity.this, 
                        "拍照失败: " + error, Toast.LENGTH_SHORT).show();
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

### 完整示例：二维码扫描

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
        
        // 获取 SDK 实例
        sdk = InitSDK.getSDK(this);
        
        if (sdk == null) {
            Toast.makeText(this, "SDK 初始化失败", Toast.LENGTH_SHORT).show();
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
        resultTextView.setText("正在扫描...");
        
        sdk.startQRCodeScan(new IsdkAPI.QRCodeCallback() {
            @Override
            public void onQRCodeDetected(String result) {
                runOnUiThread(() -> {
                    resultTextView.setText("扫描结果: " + result);
                    stopScan();
                    
                    // 可以在这里处理扫描结果
                    handleScanResult(result);
                });
            }
            
            @Override
            public void onScanError(String error) {
                runOnUiThread(() -> {
                    resultTextView.setText("扫描出错: " + error);
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
        // 处理扫描结果，例如打开 URL、解析数据等
        Log.d("QRCode", "扫描结果: " + result);
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

## 设备支持

### 设备类型枚举

SDK 通过 `DeviceType` 枚举定义支持的设备类型：

```java
public enum DeviceType {
    FS8("FS8", "FS8 device"),
    FS10("FS10", "FS10 device"),
    SK8("3576SE", "SK8设备"),
    SC1("SC1", "SC1 device"),
    FA8("FA8", "FA8 device"),
    FA10("FA10", "FA10 device"),
    LP("", "SC1设备"),
    UNKNOWN("UNKNOWN", "未知设备");
}
```

### 设备检测机制

SDK 通过 `Build.MODEL` 自动检测设备型号，并选择对应的实现：

1. **自动检测**：调用 `InitSDK.getSDK(context)` 时，SDK 会自动读取 `Build.MODEL`
2. **设备匹配**：根据设备型号匹配 `DeviceType` 枚举
3. **实现选择**：`SDKFactory` 根据设备类型创建对应的 SDK 实现实例
4. **自动初始化**：创建实例后自动调用 `init()` 方法

### 手动指定设备类型

如果需要手动指定设备类型（例如用于测试），可以使用：

```java
// 手动指定设备类型
IsdkAPI sdk = InitSDK.getSDK(context, DeviceType.SK8);
```

---

## 常见问题

### Q1: SDK 初始化返回 null

**可能原因：**
1. 当前设备型号不在支持列表中
2. 设备型号检测失败
3. 底层 SDK 初始化失败

**解决方案：**
```java
// 检查设备是否受支持
if (!InitSDK.isCurrentDeviceSupported()) {
    Log.e("SDK", "当前设备不受支持");
    DeviceType currentType = InitSDK.getCurrentDeviceType();
    Log.d("SDK", "检测到的设备类型: " + currentType);
    return;
}

// 获取 SDK 实例
IsdkAPI sdk = InitSDK.getSDK(context);
if (sdk == null) {
    Log.e("SDK", "SDK 初始化失败，请检查日志");
}
```

---

### Q2: 相机功能不可用

**可能原因：**
1. 设备没有相机硬件
2. 相机权限未授予
3. 相机被其他应用占用

**解决方案：**
```java
// 检查相机是否可用
if (!sdk.isCameraAvailable()) {
    Log.w("SDK", "相机不可用");
    return;
}

// 检查权限
if (ContextCompat.checkSelfPermission(context, Manifest.permission.CAMERA) 
    != PackageManager.PERMISSION_GRANTED) {
    // 请求权限
    ActivityCompat.requestPermissions(activity, 
        new String[]{Manifest.permission.CAMERA}, REQUEST_CAMERA_PERMISSION);
    return;
}

// 开启相机
if (!sdk.openCamera()) {
    Log.e("SDK", "相机开启失败");
}
```

---

### Q3: 如何判断当前使用的是哪个设备的实现？

**解决方案：**
```java
// 方法1: 通过设备型号判断
String model = sdk.getDeviceModel();
Log.d("SDK", "当前设备型号: " + model);

// 方法2: 通过设备类型判断
DeviceType deviceType = InitSDK.getCurrentDeviceType();
Log.d("SDK", "当前设备类型: " + deviceType.getDescription());
```

---

### Q4: 拍照回调没有触发

**可能原因：**
1. 回调对象为 null
2. 相机未正确开启
3. 底层 SDK 异常

**解决方案：**
```java
// 确保相机已开启
if (!sdk.isCameraAvailable() || !sdk.openCamera()) {
    Log.e("SDK", "相机不可用或开启失败");
    return;
}

// 确保回调不为 null
IsdkAPI.CaptureCallback callback = new IsdkAPI.CaptureCallback() {
    @Override
    public void onCaptureSuccess(String imagePath) {
        // 处理成功
    }
    
    @Override
    public void onCaptureFailure(String error) {
        // 处理失败
    }
};

// 执行拍照
sdk.takePicture(callback);
```

---

### Q5: 二维码扫描一直扫描不到

**可能原因：**
1. 二维码不在相机视野内
2. 光线不足
3. 二维码模糊或损坏

**解决方案：**
```java
// 确保相机已开启
if (!sdk.openCamera()) {
    Log.e("SDK", "相机开启失败");
    return;
}

// 开始扫描
sdk.startQRCodeScan(new IsdkAPI.QRCodeCallback() {
    @Override
    public void onQRCodeDetected(String result) {
        // 扫描成功，自动停止
        sdk.stopQRCodeScan();
    }
    
    @Override
    public void onScanError(String error) {
        Log.e("SDK", "扫描错误: " + error);
    }
});
```

---

## 注意事项

### 1. 生命周期管理

- **初始化时机**：建议在 `onCreate()` 中初始化 SDK
- **资源释放**：务必在 `onDestroy()` 中调用 `release()` 释放资源
- **相机管理**：使用完相机后及时调用 `closeCamera()` 关闭

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

### 2. 线程安全

- SDK 的回调方法可能在工作线程中执行，更新 UI 时需要使用 `runOnUiThread()` 或 Handler
- 所有 SDK 方法调用建议在主线程中进行

```java
sdk.takePicture(new IsdkAPI.CaptureCallback() {
    @Override
    public void onCaptureSuccess(String imagePath) {
        // 回调可能在非主线程，需要切换到主线程更新 UI
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

### 3. 权限处理

- 确保在 `AndroidManifest.xml` 中声明所需权限
- 运行时权限（Android 6.0+）需要在代码中动态请求

```java
// 检查并请求相机权限
if (ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA) 
    != PackageManager.PERMISSION_GRANTED) {
    ActivityCompat.requestPermissions(this, 
        new String[]{Manifest.permission.CAMERA}, 
        REQUEST_CAMERA_PERMISSION);
}
```

---

### 4. 错误处理

- 所有 SDK 方法调用都应该进行错误检查
- 使用 try-catch 捕获可能的异常
- 记录日志以便调试

```java
try {
    if (sdk != null && sdk.checkSystemStatus()) {
        String info = sdk.getDeviceInfo();
        // 处理设备信息
    }
} catch (Exception e) {
    Log.e("SDK", "获取设备信息失败", e);
    Toast.makeText(context, "操作失败: " + e.getMessage(), Toast.LENGTH_SHORT).show();
}
```

---

### 5. 设备兼容性

- 不同设备的功能支持可能不同，使用前建议调用 `getSupportedFeatures()` 查询
- 某些设备可能不支持某些功能，需要做兼容性处理

```java
String[] features = sdk.getSupportedFeatures();
boolean hasQRCode = Arrays.asList(features).contains("二维码扫描");

if (hasQRCode) {
    // 使用二维码扫描功能
    sdk.startQRCodeScan(callback);
} else {
    Toast.makeText(context, "当前设备不支持二维码扫描", Toast.LENGTH_SHORT).show();
}
```

---

### 6. 性能优化

- SDK 实例建议作为单例使用，避免重复创建
- 相机和扫描功能使用完毕后及时释放资源
- 避免在频繁调用的方法中创建 SDK 实例

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

## 版本历史

### v1.0.0 (2024-12-06)
- 初始版本发布
- 支持 SK8 等设备
- 实现设备信息获取、相机控制、二维码扫描等基础功能

---

## 技术支持

如有问题或建议，请联系：

- **技术支持邮箱**: xdn@suntek.cloud
- **文档版本**: v1.0.0
- **最后更新**: 2025-12-27

---

## 许可证

本文档及 SDK 的使用需遵循相关许可协议。

---

*本文档基于 SUNTEK 统一 SDK v1.0.0 编写*

