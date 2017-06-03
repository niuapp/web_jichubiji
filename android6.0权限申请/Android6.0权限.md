详细：<br/>
<a href="http://blog.csdn.net/lmj623565791/article/details/50709663">http://blog.csdn.net/lmj623565791/article/details/50709663</a><br/>
<a href="http://www.cnblogs.com/guanmanman/p/6197148.html">http://www.cnblogs.com/guanmanman/p/6197148.html</a><br/>

> Android 6.0有了新的权限机制，有些涉及用户隐私或比较危险的权限需要去向用户申请，使用户可以知情，做出同意或拒绝的操作。

> 新的权限机制把原来的权限分成两大类，正常的(Normal Permissions)和危险的(Dangerous Permission)，正常的不需要向用户申请，所有使用到的权限还是需要在AndroidManifest中声明，危险的再去向用户申请

### 两类权限
- Normal Permissions 只需要声明就可以，不需要申请
	- ACCESS_LOCATION_EXTRA_COMMANDS
	- ACCESS_NETWORK_STATE
	- ACCESS_NOTIFICATION_POLICY
	- ACCESS_WIFI_STATE
	- BLUETOOTH
	- BLUETOOTH_ADMIN
	- BROADCAST_STICKY
	- CHANGE_NETWORK_STATE
	- CHANGE_WIFI_MULTICAST_STATE
	- CHANGE_WIFI_STATE
	- DISABLE_KEYGUARD
	- EXPAND_STATUS_BAR
	- GET_PACKAGE_SIZE
	- INSTALL_SHORTCUT
	- INTERNET
	- KILL_BACKGROUND_PROCESSES
	- MODIFY_AUDIO_SETTINGS
	- NFC
	- READ_SYNC_SETTINGS
	- READ_SYNC_STATS
	- RECEIVE_BOOT_COMPLETED
	- REORDER_TASKS
	- REQUEST_INSTALL_PACKAGES
	- SET_ALARM
	- SET_TIME_ZONE
	- SET_WALLPAPER
	- SET_WALLPAPER_HINTS
	- TRANSMIT_IR
	- UNINSTALL_SHORTCUT
	- USE_FINGERPRINT
	- VIBRATE
	- WAKE_LOCK
	- WRITE_SYNC_SETTINGS
- **Dangerous Permission** 需要申请和声明
	- group:android.permission-group.CONTACTS
	  - permission:android.permission.WRITE_CONTACTS
  	  - permission:android.permission.GET_ACCOUNTS
  	  - permission:android.permission.READ_CONTACTS

	- group:android.permission-group.PHONE
  	  - permission:android.permission.READ_CALL_LOG
  	  - permission:android.permission.READ_PHONE_STATE
  	  - permission:android.permission.CALL_PHONE
  	  - permission:android.permission.WRITE_CALL_LOG
  	  - permission:android.permission.USE_SIP
  	  - permission:android.permission.PROCESS_OUTGOING_CALLS
  	  - permission:com.android.voicemail.permission.ADD_VOICEMAIL

	- group:android.permission-group.CALENDAR
  	  - permission:android.permission.READ_CALENDAR
  	  - permission:android.permission.WRITE_CALENDAR

	- group:android.permission-group.CAMERA
  	  - permission:android.permission.CAMERA

	- group:android.permission-group.SENSORS
  	  - permission:android.permission.BODY_SENSORS

	- group:android.permission-group.LOCATION
  	  - permission:android.permission.ACCESS_FINE_LOCATION
  	  - permission:android.permission.ACCESS_COARSE_LOCATION

	- group:android.permission-group.STORAGE
  	  - permission:android.permission.READ_EXTERNAL_STORAGE
  	  - permission:android.permission.WRITE_EXTERNAL_STORAGE

	- group:android.permission-group.MICROPHONE
  	  - permission:android.permission.RECORD_AUDIO

	- group:android.permission-group.SMS
  	  - permission:android.permission.READ_SMS
  	  - permission:android.permission.RECEIVE_WAP_PUSH
  	  - permission:android.permission.RECEIVE_MMS
  	  - permission:android.permission.RECEIVE_SMS
  	  - permission:android.permission.SEND_SMS
  	  - permission:android.permission.READ_CELL_BROADCASTS

> 危险权限是分组的，只要当前组中的某个权限申请成功，那么本组其他权限相当于都申请成功，但还是要对每个有需要的权限进行申请，防止之后分组有变

### 申请流程
1. 在AndroidManifest中声明相关权限
2. 检查权限 (没有权限就去申请)
	<pre>例：
	if (ContextCompat.checkSelfPermission(thisActivity,
	                Manifest.permission.READ_CONTACTS)
	        != PackageManager.PERMISSION_GRANTED) {
	}else{
	    //
	}
	</pre>
3. 申请授权 (可以同时申请多个，会通过对话框逐一申请)
	<pre>例：
	ActivityCompat.requestPermissions(thisActivity,
	                new String[]{Manifest.permission.READ_CONTACTS},
	                MY_PERMISSIONS_REQUEST_READ_CONTACTS);
	</pre>
4.申请结果 (在onRequestPermissionsResult方法中处理类似onActivityResult)
	<pre>例：
	@Override
	public void onRequestPermissionsResult(int requestCode,
	        String permissions[], int[] grantResults) {
	    switch (requestCode) {
	        case MY_PERMISSIONS_REQUEST_READ_CONTACTS: {
	            // If request is cancelled, the result arrays are empty.
	            if (grantResults.length > 0
	                && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
	
	                // permission was granted, yay! Do the
	                // contacts-related task you need to do.
	
	            } else {
	
	                // permission denied, boo! Disable the
	                // functionality that depends on this permission.
	            }
	            return;
	        }
	    }
	}
	</pre>

5. 被默认拒绝后重新申请
	<pre>例：
	// Should we show an explanation?
	if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
	        Manifest.permission.READ_CONTACTS)) 
	    // Show an expanation to the user *asynchronously* -- don't block
	    // this thread waiting for the user's response! After the user
	    // sees the explanation, try again to request the permission.
		在这里可以用对话框的形式提示后重新申请权限
	}
	</pre>


6. 请求权限综合模板
	<pre>模板：
	// Here, thisActivity is the current activity
	if (ContextCompat.checkSelfPermission(thisActivity,
	                Manifest.permission.READ_CONTACTS)
	        != PackageManager.PERMISSION_GRANTED) {
	
	    // Should we show an explanation?
	    if (ActivityCompat.shouldShowRequestPermissionRationale(thisActivity,
	            Manifest.permission.READ_CONTACTS)) {
	
	        // Show an expanation to the user *asynchronously* -- don't block
	        // this thread waiting for the user's response! After the user
	        // sees the explanation, try again to request the permission.
	
	    } else {
	
	        // No explanation needed, we can request the permission.
	
	        ActivityCompat.requestPermissions(thisActivity,
	                new String[]{Manifest.permission.READ_CONTACTS},
	                MY_PERMISSIONS_REQUEST_READ_CONTACTS);
	
	        // MY_PERMISSIONS_REQUEST_READ_CONTACTS is an
	        // app-defined int constant. The callback method gets the
	        // result of the request.
	    }
	}
	</pre>