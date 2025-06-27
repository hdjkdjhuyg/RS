<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.gesturetransfer">

    <uses-permission android:name="android.permission.BLUETOOTH" />
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
    <uses-permission android:name="android.permission.BLUETOOTH_SCAN" />
    <uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
    <uses-permission android:name="android.permission.BLUETOOTH_ADVERTISE" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
    <uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/Theme.GestureTransfer">
        
        <activity
            android:name=".ui.MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity android:name=".ui.ApprovalActivity" />
        <activity android:name=".ui.PermissionActivity" />
        <activity android:name=".ui.FilePreviewActivity" />

        <service
            android:name=".services.GestureService"
            android:enabled="true"
            android:exported="false"
            android:foregroundServiceType="specialUse" />

        <receiver
            android:name=".services.BootReceiver"
            android:enabled="true"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
            </intent-filter>
        </receiver>

        <provider
            android:name="androidx.core.content.FileProvider"
            android:authorities="com.example.gesturetransfer.provider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_paths" />
        </provider>
    </application>
</manifest>

plugins {
    id 'com.android.application'
    id 'kotlin-android'
}

android {
    compileSdk 34
    defaultConfig {
        applicationId "com.example.gesturetransfer"
        minSdk 23
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'
    }
    buildFeatures {
        viewBinding true
    }
}

dependencies {
    implementation 'androidx.core:core-ktx:1.12.0'
    implementation 'androidx.appcompat:appcompat:1.6.1'
    implementation 'com.google.android.material:material:1.12.0'
    implementation 'androidx.recyclerview:recyclerview:1.3.2'
    implementation 'com.google.android.gms:play-services-nearby:19.0.0'
    implementation 'com.google.mlkit:text-recognition:16.0.0'
    implementation 'androidx.activity:activity-ktx:1.8.0'
    implementation 'androidx.core:core-splashscreen:1.0.1'
    implementation 'com.google.code.gson:gson:2.10.1'
}

<?xml version="1.0" encoding="utf-8"?>
<paths xmlns:android="http://schemas.android.com/apk/res/android">
    <external-path name="external_files" path="." />
    <cache-path name="cache_files" path="." />
</paths>

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/root_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".ui.MainActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/selected_files_list"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_above="@id/fab_send"
        android:padding="8dp" />

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:id="@+id/fab_send"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_alignParentEnd="true"
        android:layout_margin="16dp"
        android:contentDescription="Send Files"
        android:src="@drawable/ic_send" />

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:id="@+id/fab_receive"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_alignParentStart="true"
        android:layout_margin="16dp"
        android:contentDescription="Receive Files"
        android:src="@drawable/ic_receive" />

    <ProgressBar
        android:id="@+id/progress_bar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:visibility="gone" />

    <TextView
        android:id="@+id/tv_connection_status"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:layout_marginTop="16dp"
        android:text="غير متصل"
        android:textColor="@android:color/holo_red_dark"
        android:textSize="16sp" />
</RelativeLayout>

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="24dp">

    <TextView
        android:id="@+id/tv_request_message"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="24dp"
        android:text="هل تريد قبول الاتصال من هذا الجهاز؟"
        android:textSize="18sp" />

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btn_accept"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginEnd="16dp"
            android:text="قبول" />

        <Button
            android:id="@+id/btn_reject"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="رفض" />
    </LinearLayout>
</LinearLayout>

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="24dp">

    <TextView
        android:id="@+id/tv_permission_message"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="24dp"
        android:text="التطبيق يحتاج صلاحيات البلوتوث والموقع والتخزين ليعمل"
        android:textSize="18sp" />

    <Button
        android:id="@+id/btn_request_permissions"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="منح الصلاحيات" />
</LinearLayout>

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="16dp">

    <ImageView
        android:id="@+id/iv_file_preview"
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:scaleType="centerCrop"
        android:visibility="gone" />

    <TextView
        android:id="@+id/tv_file_content"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="8dp"
        android:textSize="16sp"
        android:visibility="gone" />

    <Button
        android:id="@+id/btn_send_file"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_marginTop="16dp"
        android:text="إرسال الملف" />
</LinearLayout>

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:padding="8dp">

    <TextView
        android:id="@+id/tv_file_name"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:ellipsize="end"
        android:maxLines="1"
        android:textSize="16sp" />

    <TextView
        android:id="@+id/tv_file_size"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="14sp" />
</LinearLayout>

<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/transparent" />

package com.example.gesturetransfer.ui

import android.app.Activity
import android.content.Intent
import android.net.Uri
import android.os.Bundle
import androidx.activity.result.contract.ActivityResultContracts
import androidx.appcompat.app.AppCompatActivity
import androidx.core.content.ContextCompat
import androidx.core.content.FileProvider
import com.example.gesturetransfer.R
import com.example.gesturetransfer.databinding.ActivityMainBinding
import com.example.gesturetransfer.managers.NearbyManager
import com.example.gesturetransfer.managers.PermissionUtils
import com.example.gesturetransfer.managers.TrustedDevicesManager
import com.example.gesturetransfer.models.FileModel
import com.example.gesturetransfer.services.GestureService
import com.google.android.material.snackbar.Snackbar
import java.io.File

class MainActivity : AppCompatActivity() {

    private lateinit var binding: ActivityMainBinding
    private lateinit var nearbyManager: NearbyManager
    private val filesAdapter = SelectedFilesAdapter()
    private var connectedEndpointId: String? = null

    private val filePickerLauncher = registerForActivityResult(ActivityResultContracts.GetContent()) { uri: Uri? ->
        uri?.let {
            val intent = Intent(this, FilePreviewActivity::class.java).apply {
                putExtra("file_uri", it.toString())
            }
            startActivityForResult(intent, REQUEST_PREVIEW)
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        nearbyManager = NearbyManager(this)
        setupRecyclerView()
        setupFabListeners()

        if (!GestureService.isRunning(this)) {
            startService(Intent(this, GestureService::class.java))
        }

        if (!PermissionUtils.hasAllPermissions(this)) {
            startActivity(Intent(this, PermissionActivity::class.java))
        }
    }

    private fun setupRecyclerView() {
        binding.selectedFilesList.layoutManager = androidx.recyclerview.widget.LinearLayoutManager(this)
        binding.selectedFilesList.adapter = filesAdapter
    }

    private fun setupFabListeners() {
        binding.fabSend.setOnClickListener {
            if (PermissionUtils.hasAllPermissions(this)) {
                filePickerLauncher.launch("*/*")
            } else {
                startActivity(Intent(this, PermissionActivity::class.java))
            }
        }

        binding.fabReceive.setOnClickListener {
            if (PermissionUtils.hasAllPermissions(this)) {
                nearbyManager.startDiscovery({ endpointId, endpointName ->
                    if (TrustedDevicesManager.isTrustedDevice(this, endpointId)) {
                        nearbyManager.acceptConnection(endpointId, payloadCallback)
                        connectedEndpointId = endpointId
                        updateConnectionStatus(true)
                    } else {
                        val intent = Intent(this, ApprovalActivity::class.java).apply {
                            putExtra("device_name", endpointName)
                            putExtra("endpoint_id", endpointId)
                        }
                        startActivityForResult(intent, REQUEST_APPROVAL)
                    }
                }, { error ->
                    Snackbar.make(binding.root, "فشل البحث: ${error.message}", Snackbar.LENGTH_LONG).show()
                })
            } else {
                startActivity(Intent(this, PermissionActivity::class.java))
            }
        }
    }

    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        if (requestCode == REQUEST_APPROVAL && resultCode == RESULT_OK) {
            val endpointId = data?.getStringExtra("endpoint_id")
            endpointId?.let {
                connectedEndpointId = it
                nearbyManager.acceptConnection(it, payloadCallback)
                TrustedDevicesManager.saveTrustedDevice(this, it)
                updateConnectionStatus(true)
            }
        } else if (requestCode == REQUEST_PREVIEW && resultCode == RESULT_OK) {
            val uriString = data?.getStringExtra("file_uri")
            uriString?.let {
                val uri = Uri.parse(it)
                sendFile(uri)
            }
        }
    }

    private fun updateConnectionStatus(isConnected: Boolean) {
        binding.tvConnectionStatus.text = if (isConnected) "متصل" else "غير متصل"
        binding.tvConnectionStatus.setTextColor(
            ContextCompat.getColor(this, if (isConnected) android.R.color.holo_green_dark else android.R.color.holo_red_dark)
        )
    }

    private fun sendFile(uri: Uri) {
        connectedEndpointId?.let {
            binding.progressBar.visibility = android.view.View.VISIBLE
            nearbyManager.sendFile(it, uri)
        } ?: Snackbar.make(binding.root, "لم يتم الاتصال بجهاز", Snackbar.LENGTH_LONG).show()
    }

    private val payloadCallback = object : com.google.android.gms.nearby.connection.PayloadCallback() {
        override fun onPayloadReceived(endpointId: String, payload: com.google.android.gms.nearby.connection.Payload) {
            if (payload.type == com.google.android.gms.nearby.connection.Payload.Type.FILE) {
                val filePayload = payload.asFile()?.asJavaFile()
                filePayload?.let {
                    val uri = FileProvider.getUriForFile(this@MainActivity, "${packageName}.provider", it)
                    val fileName = it.name
                    val fileSize = it.length()
                    runOnUiThread {
                        filesAdapter.addFile(FileModel(uri, fileName, fileSize))
                        Snackbar.make(binding.root, "تم استقبال ملف: $fileName", Snackbar.LENGTH_LONG).show()
                    }
                }
            }
        }

        override fun onPayloadTransferUpdate(endpointId: String, update: com.google.android.gms.nearby.connection.PayloadTransferUpdate) {
            when (update.status) {
                com.google.android.gms.nearby.connection.PayloadTransferUpdate.Status.IN_PROGRESS -> {
                    runOnUiThread { binding.progressBar.visibility = android.view.View.VISIBLE }
                }
                com.google.android.gms.nearby.connection.PayloadTransferUpdate.Status.SUCCESS -> {
                    runOnUiThread {
                        binding.progressBar.visibility = android.view.View.GONE
                        Snackbar.make(binding.root, "تم نقل الملف بنجاح", Snackbar.LENGTH_LONG).show()
                    }
                }
                com.google.android.gms.nearby.connection.PayloadTransferUpdate.Status.FAILURE -> {
                    runOnUiThread {
                        binding.progressBar.visibility = android.view.View.GONE
                        Snackbar.make(binding.root, "فشل نقل الملف", Snackbar.LENGTH_LONG).show()
                    }
                }
            }
        }
    }

    companion object {
        const val REQUEST_APPROVAL = 100
        const val REQUEST_PREVIEW = 101
    }
}

package com.example.gesturetransfer.ui

import android.view.LayoutInflater
import android.view.ViewGroup
import androidx.recyclerview.widget.RecyclerView
import com.example.gesturetransfer.databinding.ItemFileBinding
import com.example.gesturetransfer.models.FileModel

class SelectedFilesAdapter : RecyclerView.Adapter<SelectedFilesAdapter.FileViewHolder>() {

    private val files = mutableListOf<FileModel>()

    class FileViewHolder(private val binding: ItemFileBinding) : RecyclerView.ViewHolder(binding.root) {
        fun bind(file: FileModel) {
            binding.tvFileName.text = file.name
            binding.tvFileSize.text = "${file.size / 1024} KB"
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): FileViewHolder {
        val binding = ItemFileBinding.inflate(LayoutInflater.from(parent.context), parent, false)
        return FileViewHolder(binding)
    }

    override fun onBindViewHolder(holder: FileViewHolder, position: Int) {
        holder.bind(files[position])
    }

    override fun getItemCount(): Int = files.size

    fun addFile(file: FileModel) {
        files.add(file)
        notifyItemInserted(files.size - 1)
    }
}

package com.example.gesturetransfer.ui

import android.app.Activity
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity
import com.example.gesturetransfer.databinding.ActivityApprovalBinding
import com.example.gesturetransfer.managers.TrustedDevicesManager

class ApprovalActivity : AppCompatActivity() {

    private lateinit var binding: ActivityApprovalBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityApprovalBinding.inflate(layoutInflater)
        setContentView(binding.root)

        val deviceName = intent.getStringExtra("device_name") ?: "جهاز غير معروف"
        val endpointId = intent.getStringExtra("endpoint_id")

        binding.tvRequestMessage.text = "هل تريد قبول الاتصال من $deviceName؟"

        binding.btnAccept.setOnClickListener {
            endpointId?.let {
                TrustedDevicesManager.saveTrustedDevice(this, it)
                setResult(Activity.RESULT_OK, Intent().putExtra("endpoint_id", it))
            }
            finish()
        }

        binding.btnReject.setOnClickListener {
            setResult(Activity.RESULT_CANCELED)
            finish()
        }
    }
}

package com.example.gesturetransfer.ui

import android.Manifest
import android.content.Intent
import android.os.Build
import android.os.Bundle
import androidx.activity.result.contract.ActivityResultContracts
import androidx.appcompat.app.AppCompatActivity
import com.example.gesturetransfer.databinding.ActivityPermissionBinding
import com.example.gesturetransfer.managers.PermissionUtils
import com.google.android.material.snackbar.Snackbar

class PermissionActivity : AppCompatActivity() {

    private lateinit var binding: ActivityPermissionBinding

    private val requestPermissionsLauncher =
        registerForActivityResult(ActivityResultContracts.RequestMultiplePermissions()) { permissions ->
            val allGranted = permissions.all { it.value }
            if (allGranted && PermissionUtils.hasOverlayPermission(this)) {
                finish()
            } else {
                Snackbar.make(binding.root, "يرجى منح جميع الصلاحيات", Snackbar.LENGTH_LONG).show()
            }
        }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityPermissionBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.btnRequestPermissions.setOnClickListener {
            requestNecessaryPermissions()
        }
    }

    private fun requestNecessaryPermissions() {
        val permissionsList = mutableListOf(
            Manifest.permission.ACCESS_FINE_LOCATION,
            Manifest.permission.ACCESS_COARSE_LOCATION,
            Manifest.permission.READ_EXTERNAL_STORAGE,
            Manifest.permission.WRITE_EXTERNAL_STORAGE
        )

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
            permissionsList.add(Manifest.permission.BLUETOOTH_SCAN)
            permissionsList.add(Manifest.permission.BLUETOOTH_CONNECT)
            permissionsList.add(Manifest.permission.BLUETOOTH_ADVERTISE)
        } else {
            permissionsList.add(Manifest.permission.BLUETOOTH)
            permissionsList.add(Manifest.permission.BLUETOOTH_ADMIN)
        }

        if (!PermissionUtils.hasOverlayPermission(this)) {
            val intent = Intent(android.provider.Settings.ACTION_MANAGE_OVERLAY_PERMISSION, android.net.Uri.parse("package:$packageName"))
            startActivity(intent)
        }

        requestPermissionsLauncher.launch(permissionsList.toTypedArray())
    }
}

package com.example.gesturetransfer.ui

import android.content.Intent
import android.net.Uri
import android.os.Bundle
import android.view.View
import androidx.appcompat.app.AppCompatActivity
import com.example.gesturetransfer.databinding.ActivityFilePreviewBinding
import com.google.mlkit.vision.text.TextRecognition
import com.google.mlkit.vision.text.latin.TextRecognizerOptions

class FilePreviewActivity : AppCompatActivity() {

    private lateinit var binding: ActivityFilePreviewBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = ActivityFilePreviewBinding.inflate(layoutInflater)
        setContentView(binding.root)

        val uriString = intent.getStringExtra("file_uri")
        uriString?.let {
            val uri = Uri.parse(it)
            previewFile(uri)
            binding.btnSendFile.setOnClickListener {
                setResult(RESULT_OK, Intent().putExtra("file_uri", uriString))
                finish()
            }
        }
    }

    private fun previewFile(uri: Uri) {
        if (uri.toString().contains("image")) {
            binding.ivFilePreview.visibility = View.VISIBLE
            binding.ivFilePreview.setImageURI(uri)
            extractTextFromImage(uri)
        } else {
            binding.tvFileContent.visibility = View.VISIBLE
            binding.tvFileContent.text = "معاينة غير متاحة لهذا النوع من الملفات"
        }
    }

    private fun extractTextFromImage(uri: Uri) {
        val recognizer = TextRecognition.getClient(TextRecognizerOptions.DEFAULT_OPTIONS)
        val image = com.google.mlkit.vision.common.InputImage.fromFilePath(this, uri)
        recognizer.process(image)
            .addOnSuccessListener { visionText ->
                binding.tvFileContent.visibility = View.VISIBLE
                binding.tvFileContent.text = visionText.text
            }
            .addOnFailureListener {
                binding.tvFileContent.visibility = View.VISIBLE
                binding.tvFileContent.text = "فشل استخراج النص"
            }
    }
}

package com.example.gesturetransfer.services

import android.app.Notification
import android.app.PendingIntent
import android.app.Service
import android.content.Context
import android.content.Intent
import android.graphics.PixelFormat
import android.os.Build
import android.os.IBinder
import android.os.PowerManager
import android.view.LayoutInflater
import android.view.MotionEvent
import android.view.View
import android.view.WindowManager
import androidx.core.app.NotificationCompat
import com.example.gesturetransfer.R
import com.example.gesturetransfer.managers.NearbyManager
import com.example.gesturetransfer.managers.PermissionUtils
import com.example.gesturetransfer.ui.ApprovalActivity
import com.example.gesturetransfer.utils.GestureDetector
import com.google.android.gms.nearby.connection.ConnectionLifecycleCallback
import com.google.android.gms.nearby.connection.ConnectionsStatusCodes

class GestureService : Service() {

    private lateinit var windowManager: WindowManager
    private lateinit var overlayView: View
    private lateinit var gestureDetector: GestureDetector
    private lateinit var nearbyManager: NearbyManager
    private lateinit var wakeLock: PowerManager.WakeLock

    companion object {
        const val CHANNEL_ID = "GestureServiceChannel"
        const val NOTIFICATION_ID = 1

        fun isRunning(context: Context): Boolean {
            val manager = context.getSystemService(Context.ACTIVITY_SERVICE) as android.app.ActivityManager
            for (service in manager.getRunningServices(Int.MAX_VALUE)) {
                if (GestureService::class.java.name == service.service.className) {
                    return true
                }
            }
            return false
        }
    }

    override fun onCreate() {
        super.onCreate()
        createNotificationChannel()
        startForeground(NOTIFICATION_ID, createNotification())
        setupGestureOverlay()
        setupWakeLock()
        nearbyManager = NearbyManager(this)
    }

    private fun createNotificationChannel() {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = android.app.NotificationChannel(
                CHANNEL_ID,
                "Gesture Service",
                android.app.NotificationManager.IMPORTANCE_LOW
            )
            val manager = getSystemService(android.app.NotificationManager::class.java)
            manager.createNotificationChannel(channel)
        }
    }

    private fun createNotification(): Notification {
        val notificationIntent = Intent(this, com.example.gesturetransfer.ui.MainActivity::class.java)
        val pendingIntent = PendingIntent.getActivity(this, 0, notificationIntent, PendingIntent.FLAG_IMMUTABLE)

        return NotificationCompat.Builder(this, CHANNEL_ID)
            .setContentTitle("خدمة الإيماءات")
            .setContentText("جارٍ مراقبة إيماءات الأصابع...")
            .setSmallIcon(R.drawable.ic_notification)
            .setContentIntent(pendingIntent)
            .setOngoing(true)
            .build()
    }

    private fun setupWakeLock() {
        val powerManager = getSystemService(Context.POWER_SERVICE) as PowerManager
        wakeLock = powerManager.newWakeLock(PowerManager.PARTIAL_WAKE_LOCK, "GestureService::Lock")
        wakeLock.acquire(10 * 60 * 1000L /* 10 minutes */)
    }

    private fun setupGestureOverlay() {
        val layoutParams = WindowManager.LayoutParams(
            WindowManager.LayoutParams.MATCH_PARENT,
            WindowManager.LayoutParams.MATCH_PARENT,
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O)
                WindowManager.LayoutParams.TYPE_APPLICATION_OVERLAY
            else
                WindowManager.LayoutParams.TYPE_PHONE,
            WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE or WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN,
            PixelFormat.TRANSLUCENT
        )

        windowManager = getSystemService(WINDOW_SERVICE) as WindowManager
        overlayView = LayoutInflater.from(this).inflate(R.layout.overlay_layout, null)
        gestureDetector = GestureDetector()

        overlayView.setOnTouchListener { _, event ->
            if (gestureDetector.isThreeFingersClosed(event)) {
                onThreeFingerGesture()
            } else if (gestureDetector.isOpenHand(event)) {
                onOpenHandGesture()
            }
            true
        }

        windowManager.addView(overlayView, layoutParams)
    }

    private fun onThreeFingerGesture() {
        if (!PermissionUtils.hasAllPermissions(this)) {
            val intent = Intent(this, com.example.gesturetransfer.ui.PermissionActivity::class.java)
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
            startActivity(intent)
            return
        }

        nearbyManager.startAdvertising("جهازي", connectionLifecycleCallback, {
            android.widget.Toast.makeText(this, "فشل الإعلان: ${it.message}", android.widget.Toast.LENGTH_LONG).show()
        })
        android.widget.Toast.makeText(this, "جاهز للإرسال", android.widget.Toast.LENGTH_SHORT).show()
    }

    private fun onOpenHandGesture() {
        if (!PermissionUtils.hasAllPermissions(this)) {
            val intent = Intent(this, com.example.gesturetransfer.ui.PermissionActivity::class.java)
            intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
            startActivity(intent)
            return
        }

        nearbyManager.startDiscovery({ endpointId, endpointName ->
            val intent = Intent(this, ApprovalActivity::class.java).apply {
                putExtra("device_name", endpointName)
                putExtra("endpoint_id", endpointId)
                addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
            }
            startActivity(intent)
        }, {
            android.widget.Toast.makeText(this, "فشل البحث: ${it.message}", android.widget.Toast.LENGTH_LONG).show()
        })
        android.widget.Toast.makeText(this, "جاهز للاستقبال", android.widget.Toast.LENGTH_SHORT).show()
    }

    private val connectionLifecycleCallback = object : ConnectionLifecycleCallback() {
        override fun onConnectionInitiated(endpointId: String, connectionInfo: com.google.android.gms.nearby.connection.ConnectionInfo) {
            val intent = Intent(this@GestureService, ApprovalActivity::class.java).apply {
                putExtra("device_name", connectionInfo.endpointName)
                putExtra("endpoint_id", endpointId)
                addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
            }
            startActivity(intent)
        }

        override fun onConnectionResult(endpointId: String, result: com.google.android.gms.nearby.connection.ConnectionResolution) {
            when (result.status.statusCode) {
                ConnectionsStatusCodes.STATUS_OK -> {
                    android.widget.Toast.makeText(this@GestureService, "تم الاتصال بنجاح", android.widget.Toast.LENGTH_SHORT).show()
                }
                else -> {
                    android.widget.Toast.makeText(this@GestureService, "فشل الاتصال", android.widget.Toast.LENGTH_LONG).show()
                }
            }
        }

        override fun onDisconnected(endpointId: String) {
            android.widget.Toast.makeText(this@GestureService, "تم قطع الاتصال", android.widget.Toast.LENGTH_SHORT).show()
        }
    }

    override fun onBind(intent: Intent?): IBinder? = null

    override fun onDestroy() {
        super.onDestroy()
        if (::windowManager.isInitialized && ::overlayView.isInitialized) {
            windowManager.removeView(overlayView)
        }
        if (::wakeLock.isInitialized && wakeLock.isHeld) {
            wakeLock.release()
        }
        nearbyManager.stopAllEndpoints()
    }
}

package com.example.gesturetransfer.managers

import android.content.Context
import android.net.Uri
import android.util.Log
import com.google.android.gms.nearby.Nearby
import com.google.android.gms.nearby.connection.*

class NearbyManager(private val context: Context) {

    private val connectionsClient = Nearby.getConnectionsClient(context)
    private val strategy = Strategy.P2P_CLUSTER
    private val serviceId = "com.example.gesturetransfer"

    companion object {
        private const val TAG = "NearbyManager"
    }

    fun startAdvertising(endpointName: String, connectionCallback: ConnectionLifecycleCallback, onFailure: (Exception) -> Unit) {
        val advertisingOptions = AdvertisingOptions.Builder().setStrategy(strategy).build()
        connectionsClient.startAdvertising(endpointName, serviceId, connectionCallback, advertisingOptions)
            .addOnFailureListener { e ->
                Log.e(TAG, "فشل الإعلان: ${e.message}")
                onFailure(e)
            }
    }

    fun startDiscovery(endpointFoundCallback: (String, String) -> Unit, onFailure: (Exception) -> Unit) {
        val discoveryOptions = DiscoveryOptions.Builder().setStrategy(strategy).build()
        connectionsClient.startDiscovery(serviceId, object : EndpointDiscoveryCallback() {
            override fun onEndpointFound(endpointId: String, info: DiscoveredEndpointInfo) {
                Log.d(TAG, "تم العثور على جهاز: ${info.endpointName}")
                endpointFoundCallback(endpointId, info.endpointName)
            }

            override fun onEndpointLost(endpointId: String) {
                Log.d(TAG, "تم فقدان جهاز: $endpointId")
            }
        }, discoveryOptions).addOnFailureListener { e ->
            Log.e(TAG, "فشل البحث: ${e.message}")
            onFailure(e)
        }
    }

    fun acceptConnection(endpointId: String, payloadCallback: PayloadCallback) {
        connectionsClient.acceptConnection(endpointId, payloadCallback)
    }

    fun rejectConnection(endpointId: String) {
        connectionsClient.rejectConnection(endpointId)
    }

    fun sendFile(endpointId: String, fileUri: Uri) {
        val payload = Payload.fromFile(fileUri)
        connectionsClient.sendPayload(endpointId, payload)
        Log.d(TAG, "تم إرسال ملف إلى $endpointId")
    }

    fun stopAllEndpoints() {
        connectionsClient.stopAllEndpoints()
        Log.d(TAG, "تم إيقاف جميع الاتصالات")
    }
}

package com.example.gesturetransfer.managers

import android.Manifest
import android.content.Context
import android.os.Build
import androidx.core.content.ContextCompat
import android.provider.Settings

object PermissionUtils {

    fun hasAllPermissions(context: Context): Boolean {
        val permissions = mutableListOf(
            Manifest.permission.ACCESS_FINE_LOCATION,
            Manifest.permission.ACCESS_COARSE_LOCATION,
            Manifest.permission.READ_EXTERNAL_STORAGE,
            Manifest.permission.WRITE_EXTERNAL_STORAGE
        )

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
            permissions.add(Manifest.permission.BLUETOOTH_SCAN)
            permissions.add(Manifest.permission.BLUETOOTH_CONNECT)
            permissions.add(Manifest.permission.BLUETOOTH_ADVERTISE)
        } else {
            permissions.add(Manifest.permission.BLUETOOTH)
            permissions.add(Manifest.permission.BLUETOOTH_ADMIN)
        }

        return permissions.all {
            ContextCompat.checkSelfPermission(context, it) == PackageManager.PERMISSION_GRANTED
        } && hasOverlayPermission(context)
    }

    fun hasOverlayPermission(context: Context): Boolean {
        return if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            Settings.canDrawOverlays(context)
        } else {
            true
        }
    }
}

package com.example.gesturetransfer.managers

import android.content.Context
import com.google.gson.Gson
import com.google.gson.reflect.TypeToken

object TrustedDevicesManager {

    private const val PREFS_NAME = "TrustedDevices"
    private const val DEVICES_KEY = "trusted_list"

    fun saveTrustedDevice(context: Context, device: DeviceInfo) {
        val prefs = context.getSharedPreferences(PREFS_NAME, Context.MODE_PRIVATE)
        val devices = getTrustedDevices(context).toMutableSet()
        devices.add(device)
        val gson = Gson()
        prefs.edit().putString(DEVICES_KEY, gson.toJson(devices)).apply()
    }

    fun getTrustedDevices(context: Context): Set<DeviceInfo> {
        val prefs = context.getSharedPreferences(PREFS_NAME, Context.MODE_PRIVATE)
        val json = prefs.getString(DEVICES_KEY, null)
        return if (json != null) {
            val gson = Gson()
            val type = object : TypeToken<Set<DeviceInfo>>() {}.type
            gson.fromJson(json, type) ?: emptySet()
        } else {
            emptySet()
        }
    }

    fun isTrustedDevice(context: Context, deviceId: String): Boolean {
        return getTrustedDevices(context).any { it.id == deviceId }
    }

    fun removeTrustedDevice(context: Context, deviceId: String) {
        val prefs = context.getSharedPreferences(PREFS_NAME, Context.MODE_PRIVATE)
        val devices = getTrustedDevices(context).toMutableSet()
        devices.removeIf { it.id == deviceId }
        val gson = Gson()
        prefs.edit().putString(DEVICES_KEY, gson.toJson(devices)).apply()
    }
}

package com.example.gesturetransfer.models

import android.net.Uri

data class FileModel(
    val uri: Uri,
    val name: String,
    val size: Long
)

data class DeviceInfo(
    val id: String,
    val name: String,
    val lastConnected: Long = System.currentTimeMillis()
)

package com.example.gesturetransfer.utils

import android.content.Context
import android.hardware.Sensor
import android.hardware.SensorEvent
import android.hardware.SensorEventListener
import android.hardware.SensorManager
import android.view.MotionEvent
import kotlin.math.sqrt

class GestureDetector(private val context: Context? = null) {

    private var sensorManager: SensorManager? = null
    private var accelerometer: Sensor? = null
    private var isShaking = false

    init {
        context?.let {
            sensorManager = it.getSystemService(Context.SENSOR_SERVICE) as SensorManager
            accelerometer = sensorManager?.getDefaultSensor(Sensor.TYPE_ACCELEROMETER)
        }
    }

    fun isThreeFingersClosed(event: MotionEvent): Boolean {
        if (event.pointerCount == 3) {
            val dist01 = distance(event.getX(0), event.getY(0), event.getX(1), event.getY(1))
            val dist12 = distance(event.getX(1), event.getY(1), event.getX(2), event.getY(2))
            val dist20 = distance(event.getX(2), event.getY(2), event.getX(0), event.getY(0))

            val threshold = 100f // يمكن تعديل القيمة حسب حجم الشاشة

            return dist01 < threshold && dist12 < threshold && dist20 < threshold
        }
        return false
    }

    fun isOpenHand(event: MotionEvent): Boolean {
        if (event.pointerCount >= 3) {
            val dist01 = distance(event.getX(0), event.getY(0), event.getX(1), event.getY(1))
            val dist12 = distance(event.getX(1), event.getY(1), event.getX(2), event.getY(2))

            val threshold = 150f // مسافة أكبر لفتح اليد

            return dist01 > threshold || dist12 > threshold
        }
        return false
    }

    private fun distance(x1: Float, y1: Float, x2: Float, y2: Float): Float {
        return sqrt((x1 - x2) * (x1 - x2) + (y1 - y2) * (y1 - y2))
    }

    fun registerShakeListener(onShake: () -> Unit) {
        sensorManager?.registerListener(object : SensorEventListener {
            override fun onSensorChanged(event: SensorEvent?) {
                event?.let {
                    val x = it.values[0]
                    val y = it.values[1]
                    val z = it.values[2]
                    val acceleration = sqrt(x * x + y * y + z * z)
                    if (acceleration > 20) { // تعديل العتبة حسب الحاجة
                        isShaking = true
                        onShake()
                    } else {
                        isShaking = false
                    }
                }
            }

            override fun onAccuracyChanged(sensor: Sensor?, accuracy: Int) {}
        }, accelerometer, SensorManager.SENSOR_DELAY_NORMAL)
    }

    fun unregisterShakeListener() {
        sensorManager?.unregisterListener(null)
    }

    fun isDeviceShaking(): Boolean = isShaking
}

package com.example.gesturetransfer.services

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import androidx.core.content.ContextCompat

class BootReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        if (intent?.action == Intent.ACTION_BOOT_COMPLETED) {
            context?.let {
                val serviceIntent = Intent(it, GestureService::class.java)
                ContextCompat.startForegroundService(it, serviceIntent)
            }
        }
    }
}

<string name="app_name">Gesture Transfer</string>
