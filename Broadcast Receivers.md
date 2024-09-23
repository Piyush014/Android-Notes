# Broadcast Receivers:

Broadcast in android is the system-wide events that can occur when the device starts, when a message is received on the device or when incoming calls are received, or when a device goes to airplane mode, etc. 
Broadcast Receivers are used to respond to these system-wide events. Broadcast Receivers allow us to register for the system and application events, and when that event happens, then the register receivers get notified. 

## There are mainly two types of Broadcast Receivers:
  ### Static Broadcast Receivers: These types of Receivers are declared in the manifest file and works even if the app is closed.
  ### Dynamic Broadcast Receivers: These types of receivers work only if the app is active or minimized.

Since from API Level 26, most of the broadcast can only be caught by the dynamic receiver, so we have implemented dynamic receivers in our sample project given below. There are some static fields defined in the Intent class which can be used to broadcast different events. We have taken a change of airplane mode as a broadcast event, but there are many events for which broadcast register can be used.

Following are some of the important system-wide generated intents:
![image](https://github.com/user-attachments/assets/b62cc55b-be77-4676-a38a-a97d5c6c3129)

## The two main things that we have to do in order to use the broadcast receiver in our application are:
### Creating the Broadcast Receiver:
class AirplaneModeChangeReceiver:BroadcastReceiver() {
override fun onReceive(context: Context?, intent: Intent?) {
// logic of the code needs to be written here
}
}

### Registering a BroadcastReceiver:
IntentFilter(Intent.ACTION_AIRPLANE_MODE_CHANGED).also {
// receiver is the broadcast receiver that we have registered
// and it is the intent filter that we have created
registerReceiver(receiver,it)
}



Below is the sample project showing how to create the broadcast Receiver and how to register them for a particular event and how to use them in the application.

### Step 1: Create a New Project

### Step 2: Working with the activity_main.xml file
Go to the activity_main.xml file and refer to the following code. Below is the code for the activity_main.xml file.

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">
</androidx.constraintlayout.widget.ConstraintLayout>


### Step 3: Working with the MainActivity file
Go to the MainActivity file and refer to the following code. Below is the code for the MainActivity file. Comments are added inside the code to understand the code in more detail.

import android.content.Intent
import android.content.IntentFilter
import android.os.Bundle
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    // register the receiver in the main activity in order
    // to receive updates of broadcasts events if they occur
    lateinit var receiver: AirplaneModeChangeReceiver
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        receiver = AirplaneModeChangeReceiver()

        // Intent Filter is useful to determine which apps wants to receive
        // which intents,since here we want to respond to change of
        // airplane mode
        IntentFilter(Intent.ACTION_AIRPLANE_MODE_CHANGED).also {
            // registering the receiver
            // it parameter which is passed in  registerReceiver() function
            // is the intent filter that we have just created
            registerReceiver(receiver, it)
        }
    }

    // since AirplaneModeChangeReceiver class holds a instance of Context
    // and that context is actually the activity context in which
    // the receiver has been created
    override fun onStop() {
        super.onStop()
        unregisterReceiver(receiver)
    }
}


### Step 4: Create a new class

Go to app > java > your package name(in which the MainActicity is present) > right-click > New > Kotlin File/Class and name the files as AirplaneModeChangeReceiver . Below is the code for the AirplaneModeChangeReceiver file. Comments are added inside the code to understand the code in more detail.

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.widget.Toast

// AirplaneModeChangeReceiver class extending BroadcastReceiver class
class AirplaneModeChangeReceiver : BroadcastReceiver() {

    // this function will be executed when the user changes his
    // airplane mode
    override fun onReceive(context: Context?, intent: Intent?) {
        
        // intent contains the information about the broadcast
        // in our case broadcast is change of airplane mode

        // if getBooleanExtra contains null value,it will directly return back
        val isAirplaneModeEnabled = intent?.getBooleanExtra("state", false) ?: return
        
        // checking whether airplane mode is enabled or not
        if (isAirplaneModeEnabled) {
            // showing the toast message if airplane mode is enabled
            Toast.makeText(context, "Airplane Mode Enabled", Toast.LENGTH_LONG).show()
        } else {
            // showing the toast message if airplane mode is disabled
            Toast.makeText(context, "Airplane Mode Disabled", Toast.LENGTH_LONG).show()
        }
    }
}


## Static Broadcast Receiver: 

### Step - 1: Create a new file for Broadcast Receiver: "OnBootComplete"
package com.piyush.demobroadcast

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.widget.Toast

class OnBootComplete:BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        if (intent?.action == Intent.ACTION_BOOT_COMPLETED){
            Log.d("BOOT_COMPLETED", "Boot Successfully Completed")
            Toast.makeText(context,"Boot Complete",Toast.LENGTH_SHORT).show()
        }
    }
}

### Step - 2: Declare the receiver in manifest:
        <receiver
            android:name=".OnBootComplete"
            android:enabled="true"
            android:directBootAware="true"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
            </intent-filter>
        </receiver>


### Step - 3: Run the app and it will show the Toast When you will open the app again.


## Dynamic Broadcast:

### Step - Add Text view in the main activity.

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="312dp"
        android:text="Dynamic Broadcast Receiver"
        android:textSize="21sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.496"
        app:layout_constraintStart_toStartOf="parent" />

    <TextView
        android:id="@+id/textView2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Turn On/Off Flight Mode"
        android:textSize="21sp"
        android:textStyle="bold"
        app:layout_constraintBottom_toTopOf="@+id/textView"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.496"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.79" />
</androidx.constraintlayout.widget.ConstraintLayout>


### Step - 2: Register Receiver Dynamically in the MainActivity using the following code.
package com.piyush.a40_dynamic_broadcast

import android.annotation.SuppressLint
import android.content.Intent
import android.content.IntentFilter
import android.os.Bundle
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class MainActivity : AppCompatActivity() {
    lateinit var Air:Airmode
    @SuppressLint("InlinedApi")
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_main)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
        Air = Airmode()
        IntentFilter(Intent.ACTION_AIRPLANE_MODE_CHANGED).also {
            registerReceiver(Air, it, RECEIVER_EXPORTED)
        }
    }

    override fun onDestroy() {
        super.onDestroy()
        unregisterReceiver(Air)
    }
}


### Step - 3:  Create a new Class name Airmode and extend it with Broadcast Receiver.

package com.piyush.a40_dynamic_broadcast

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent

class Airmode:BroadcastReceiver() {
    override fun onReceive(p0: Context?, p1: Intent?) {
        val state:Boolean = p1?.getBooleanExtra("state", false) ?: return
        if (state) {
            t(p0, "Airmode Enabled")
        } else {
            t(p0, "Airmode Disabled")
        }
    }

    fun t(p0: Context?, msg:Any)
    {
        android.widget.Toast.makeText(p0, msg.toString(), android.widget.Toast.LENGTH_SHORT).show()
    }
}



## Custom Broadcast Receiver:



## Sender App:
### Step - 1: Design the activity_main.xml layout file.

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Sender App!"
        android:textSize="24sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.3" />

    <EditText
        android:id="@+id/editText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:hint="Enter data to broadcast"
        app:layout_constraintTop_toBottomOf="@+id/textView"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_marginTop="16dp"/>

    <Button
        android:id="@+id/btnSend"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="Send Broadcast"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/editText"
        android:onClick="sendBroadcast" />

</androidx.constraintlayout.widget.ConstraintLayout>


### Step - 2: Create a broadcast receiver and sendBroadcast function.

package com.piyush.a30_custombroadcastsender;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
import android.os.Bundle;
import android.view.View;
import android.widget.EditText;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    private TextView mTextView;
    private EditText mEditText;
    private BroadcastReceiver mInnerReceiver = new BroadcastReceiver() {
        @Override
        public void onReceive(Context context, Intent intent) {
            if ("com.piyush.a31_custombroadcastreceiver.ACTION_SEND".equals(intent.getAction())) {
                String intentExtra = intent.getStringExtra("com.piyush.EXTRA_DATA");
                mTextView.setText("Inner Broadcast Receiver: " + intentExtra);
            }
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mTextView = findViewById(R.id.textView);
        mEditText = findViewById(R.id.editText);

        // Register the inner receiver for specific custom broadcast action
        IntentFilter intentFilter = new IntentFilter("com.piyush.a31_custombroadcastreceiver.ACTION_SEND");
        registerReceiver(mInnerReceiver, intentFilter);
    }

    public void sendBroadcast(View view) {
        // Get the data from the EditText
        String dataToSend = mEditText.getText().toString();

        // Send a custom broadcast with some extra data
        Intent intent = new Intent("com.piyush.a31_custombroadcastreceiver.ACTION_SEND");
        intent.putExtra("com.piyush.EXTRA_DATA", dataToSend);
        sendBroadcast(intent);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        // Unregister the inner receiver to avoid memory leaks
        unregisterReceiver(mInnerReceiver);
    }
}


### Step - 3: Write the code for MyBroadcastReceiver.kt 

package com.piyush.a30_custombroadcastsender;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.net.ConnectivityManager;
import android.widget.Toast;

public class MyBroadcastReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (ConnectivityManager.CONNECTIVITY_ACTION.equals(intent.getAction())) {
            boolean noConnectivity = intent.getBooleanExtra(ConnectivityManager.EXTRA_NO_CONNECTIVITY, false);
            if (!noConnectivity) {
                Toast.makeText(context, "Connected", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(context, "Disconnected", Toast.LENGTH_SHORT).show();
            }
        } else if (Intent.ACTION_TIME_TICK.equals(intent.getAction())) {
            Toast.makeText(context, "Time incremented", Toast.LENGTH_SHORT).show();
        }
    }
}


### Step - 4: Declare the receiver in the manifest file.

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.piyush.a30_custombroadcastsender">

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme._30_CustomBroadcastSender">

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <receiver
            android:name=".MyBroadcastReceiver"
            android:exported="true">
            <intent-filter>
                <action android:name="com.piyush.a31_custombroadcastreceiver.ACTION_SEND" />
            </intent-filter>
        </receiver>

    </application>

</manifest>


## Receiver App:

### Step - 1: activity_main.xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Receiver App!"
        android:textSize="24sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintHorizontal_bias="0.5"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_bias="0.3" />

    <EditText
        android:id="@+id/editText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:hint="Received data will appear here"
        app:layout_constraintTop_toBottomOf="@+id/textView"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_marginTop="16dp"/>

    <Button
        android:id="@+id/btnReceive"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:text="Receive Broadcast"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/editText"
        android:onClick="receiveBroadcast" />
</androidx.constraintlayout.widget.ConstraintLayout>


### Step - 2: Write code for MainActivity.kt

package com.piyush.a31_custombroadcastreceiver;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
import android.os.Bundle;
import android.view.View;
import android.widget.EditText;
import android.widget.Toast;

import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {

    private EditText mEditText;
    private MyDemoReceiver myDemoReceiver = new MyDemoReceiver();
    private String receivedData = "";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mEditText = findViewById(R.id.editText);

        IntentFilter intentFilter = new IntentFilter("com.piyush.a31_custombroadcastreceiver.ACTION_SEND");
        registerReceiver(myDemoReceiver, intentFilter);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        unregisterReceiver(myDemoReceiver);
    }

    public class MyDemoReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            if ("com.piyush.a31_custombroadcastreceiver.ACTION_SEND".equals(intent.getAction())) {
                receivedData = intent.getStringExtra("com.piyush.EXTRA_DATA");
                Toast.makeText(context, "Broadcast Received", Toast.LENGTH_LONG).show();
            }
        }
    }

    public void receiveBroadcast(View view) {
        mEditText.setText(receivedData);
    }
}


### Step - 3: Write code for MyDemoReceiver.kt class.

package com.piyush.a31_custombroadcastreceiver;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.widget.Toast;

public class MyDemoReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if("com.piyush.a31_custombroadcastreceiver.ACTION_SEND".equals(intent.getAction())){
            String extraData = intent.getStringExtra("com.piyush.EXTRA_DATA");
            Toast.makeText(context, "From Receiver: "+extraData, Toast.LENGTH_LONG).show();
        }
    }


}

### Step - 4: Ass <uses-permission> tag in manifest.

<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.piyush.a31_custombroadcastreceiver">

    <uses-permission android:name="android.permission.BROADCAST_PACKAGE_REMOVED"
        tools:ignore="ProtectedPermissions" />
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.AppCompat.DayNight.NoActionBar">
        <activity android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>



## Ordered Broadcast Receiver:

## Sender App:

### Step - 1:  Add a button in the main_activity.xml

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/sendBroadcast"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Send Broadcast"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>

### Step - 2: Create a sender Receiver

package com.piyush.a54_orderedbroadcast_senderreceiver

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.widget.Toast

class SenderReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        var resultCode = resultCode
        var resultData = resultData
        val resultExtras = getResultExtras(true)
        var stringExtra = resultExtras.getString("stringExtra")

        resultCode++
        stringExtra += " ->SenderReceiver"

        val toast = "Sender " +
                "resultCode = $resultCode " +
                "resultData = $resultData " +
                "stringExtra = $stringExtra "
        Toast.makeText(context, toast, Toast.LENGTH_LONG).show()

        resultData = "Sender"
        resultExtras.putString("stringExtra", stringExtra)
        setResult(resultCode, resultData, resultExtras)
    }
}


### Step - 3:  Write code for MainActivity.kt

package com.piyush.a54_orderedbroadcast_senderreceiver

import android.content.Intent
import android.os.Bundle
import android.widget.Button
import androidx.appcompat.app.AppCompatActivity
import android.Manifest

class MainActivity : AppCompatActivity() {

    private val senderReceiver = SenderReceiver()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        findViewById<Button>(R.id.sendBroadcast).setOnClickListener {
            val intent = Intent("com.piyush.EXAMPLE_ACTION")
            intent.setPackage("com.piyush.a53_ordered_broadcast") // Ensure package is correct
            val extras = Bundle()
            extras.putString("stringExtra", "Start")

            // Send the ordered broadcast, specifying SenderReceiver as the last receiver
            sendOrderedBroadcast(intent, Manifest.permission.WAKE_LOCK, senderReceiver, null, 0, "Start", extras)
        }
    }
}


### Step - 4 Write the receiver and uses-permission tags code as follows:

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">


    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="com.piyush.EXAMPLE_PERMISSION"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme._54_OrderedBroadcast_SenderReceiver"
        tools:targetApi="31">

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <receiver android:name=".SenderReceiver"
            android:exported="true" />

    </application>

</manifest>



## Receiver App

The Receiver app will have three ordered broadcast classes and the code of the receiver app is as follows:


### Step - 1: Write code for activity_main.xml.

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>

### Step - 2: Create three kotlin classes:
            - OrderedBroadcastReceiver1
            - OrderedBroadcastReceiver2
            - OrderedBroadcastReceiver3

### Step - 3: Write Code for OrderedBroadcastReceiver1:

package com.piyush.a53_ordered_broadcast

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.widget.Toast

class OrderedBroadcastReceiver1 : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        var resultCode = resultCode
        var resultData = resultData
        val resultExtras = getResultExtras(true)
        var stringExtra = resultExtras.getString("stringExtra")

        resultCode++
        stringExtra += "->OBR1"

        val toast = "OBR1\n" +
                "resultCode = $resultCode\n" +
                "resultData = $resultData\n" +
                "stringExtra = $stringExtra"
        Toast.makeText(context, toast, Toast.LENGTH_SHORT).show()

        resultData = "OBR1"
        resultExtras.putString("stringExtra", stringExtra)
        setResult(resultCode, resultData, resultExtras)
    }
}


### Step - 4: Similarly, Write code for OrderedBroadcastReceiver2.

package com.piyush.a53_ordered_broadcast

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.widget.Toast

class OrderedBroadcastReceiver2 : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        var resultCode = resultCode
        var resultData = resultData
        val resultExtras = getResultExtras(true)
        var stringExtra = resultExtras.getString("stringExtra")

        resultCode++
        stringExtra += "->OBR2"

        val toast = "OBR2\n" +
                "resultCode = $resultCode\n" +
                "resultData = $resultData\n" +
                "stringExtra = $stringExtra"
        resultData = "OBR2"
        resultExtras.putString("stringExtra", stringExtra)
        setResult(resultCode, resultData, resultExtras)
        Toast.makeText(context, toast, Toast.LENGTH_SHORT).show()
    }
}

### Step - 5: Similarly, write code for OrderedBroadcastReceiver3.

package com.piyush.a53_ordered_broadcast

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.widget.Toast

class OrderedBroadcastReceiver3 : BroadcastReceiver() {
    override fun onReceive(context: Context?, intent: Intent?) {
        var resultCode = resultCode
        var resultData = resultData
        val resultExtras = getResultExtras(true)
        var stringExtra = resultExtras.getString("stringExtra")

        resultCode++
        stringExtra += "->OBR3"

        val toast = "OBR3\n" +
                "resultCode = $resultCode\n" +
                "resultData = $resultData\n" +
                "stringExtra = $stringExtra"
        Toast.makeText(context, toast, Toast.LENGTH_SHORT).show()

        resultData = "OBR3"
        resultExtras.putString("stringExtra", stringExtra)
        setResult(resultCode, resultData, resultExtras)
    }
}


### Step - 6: Write code for MainActivity.kt 
package com.piyush.a53_ordered_broadcast

import android.annotation.SuppressLint
import android.content.Context
import android.content.Intent
import android.content.IntentFilter
import android.os.Build
import android.os.Bundle
import androidx.annotation.RequiresApi
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    private val orderedBroadcastReceiver2 = OrderedBroadcastReceiver2()

    @RequiresApi(Build.VERSION_CODES.O)
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val intentFilter = IntentFilter("com.piyush.EXAMPLE_ACTION")
        intentFilter.priority = 100
        registerReceiver(orderedBroadcastReceiver2, intentFilter, RECEIVER_EXPORTED)
    }

    override fun onDestroy() {
        super.onDestroy()
        unregisterReceiver(orderedBroadcastReceiver2)
    }
}


### Step - 7: Declare receiver and permission as follows in AndroidManifest.xml

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <permission android:name="com.piyush.EXAMPLE_PERMISSION"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme._53_Ordered_Broadcast"
        tools:targetApi="31">

        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <receiver android:name=".OrderedBroadcastReceiver1"
            android:exported="true" android:permission="android.permission.INTERNET">
            <intent-filter android:priority="75">
                <action android:name="com.piyush.EXAMPLE_ACTION" />
            </intent-filter>
        </receiver>



        <receiver android:name=".OrderedBroadcastReceiver3"
            android:exported="true" android:permission="com.piyush.EXAMPLE_PERMISSION">
            <intent-filter android:priority="25">
                <action android:name="com.piyush.EXAMPLE_ACTION" />
            </intent-filter>
        </receiver>

    </application>

</manifest>


## Broadcast Receivers Using ADB Shell:

### Step - 1: Write code for Activity_main.xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="16dp"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".CustomBroadcastActivity">

    <EditText
        android:id="@+id/editText1"
        android:layout_width="250sp"
        android:layout_height="wrap_content"
        android:hint="Enter first number"
        android:inputType="numberDecimal" />

    <EditText
        android:id="@+id/editText2"
        android:layout_width="250sp"
        android:layout_height="wrap_content"
        android:hint="Enter second number"
        android:inputType="numberDecimal" />

    <TextView
        android:id="@+id/text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="21sp"
        android:text="Message" />

    <Button
        android:id="@+id/send_broad"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Send Broadcast"/>

</LinearLayout>


### Step - 2: Write code for CustomBroadcastActivity.kt

package com.piyush.broadcastreceiver

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.content.IntentFilter
import android.os.Build
import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.TextView
import androidx.activity.enableEdgeToEdge
import androidx.annotation.RequiresApi
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class CustomBroadcastActivity : AppCompatActivity() {

    private lateinit var editText1: EditText
    private lateinit var editText2: EditText
    private lateinit var textView: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_custom_broadcast)

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        editText1 = findViewById(R.id.editText1)
        editText2 = findViewById(R.id.editText2)
        textView = findViewById(R.id.text_view)

        findViewById<Button>(R.id.send_broad).setOnClickListener {
            // Get the numbers from the EditText fields
            val number1 = editText1.text.toString().toDoubleOrNull() ?: 0.0
            val number2 = editText2.text.toString().toDoubleOrNull() ?: 0.0

            // Calculate the sum and subtraction
            val sum = number1 + number2
            val sub = number1 - number2

            // Send the broadcast with the sum
            val sumIntent = Intent("com.piyush.receiver.SUM_ACTION").apply {
                putExtra("com.piyush.EXTRA_DATA_SUM", sum.toString())
            }
            sendBroadcast(sumIntent, "com.piyush.EXAMPLE_PERMISSION")

            // Send the broadcast with the subtraction result
            val subIntent = Intent("com.piyush.receiver.SUB_ACTION").apply {
                putExtra("com.piyush.EXTRA_DATA_SUB", sub.toString())
            }
            sendBroadcast(subIntent, "com.piyush.EXAMPLE_PERMISSION")
        }
    }

    @RequiresApi(Build.VERSION_CODES.TIRAMISU)
    override fun onStart() {
        super.onStart()
        // Create the IntentFilter
        val intentFilter = IntentFilter().apply {
            addAction("com.piyush.receiver.SUM_ACTION")
            addAction("com.piyush.receiver.SUB_ACTION")
        }
        // Register the BroadcastReceiver with the appropriate parameters
        registerReceiver(broadcastReceiver, intentFilter, "com.piyush.EXAMPLE_PERMISSION", null, Context.RECEIVER_EXPORTED)
    }

    override fun onStop() {
        super.onStop()
        // Unregister the local broadcast receiver when the activity stops
        unregisterReceiver(broadcastReceiver)
    }

    private val broadcastReceiver: BroadcastReceiver = object : BroadcastReceiver() {
        override fun onReceive(context: Context?, intent: Intent?) {
            when (intent?.action) {
                "com.piyush.receiver.SUM_ACTION" -> {
                    val receivedSum = intent.getStringExtra("com.piyush.EXTRA_DATA_SUM")
                    textView.text = "The Final Sum of the two numbers is: $receivedSum"
                }
                "com.piyush.receiver.SUB_ACTION" -> {
                    val receivedSub = intent.getStringExtra("com.piyush.EXTRA_DATA_SUB")
                    textView.text = "The Broadcast sent Successfully!!"
                }
            }
        }
    }
}


### Step - 3: Declare the receiver in manifest file

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">


    <permission android:name="com.piyush.EXAMPLE_PERMISSION"/>
    <uses-permission android:name="com.piyush.EXAMPLE_PERMISSION"/>
    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Broadcastreceiver"
        tools:targetApi="31">
        <activity
            android:name=".CustomBroadcastActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity
            android:name=".MainActivity" />

    </application>

</manifest>



### Step - 4: Open Terminal and write adb, if the command do not execute then install adb in your PC first using this link

https://developer.android.com/tools/releases/platform-tools


### Step - 5: after Successfully Installing ADB in terminal write first the adb devices command and enter

### Step - 6: Ensure that the Emulator only running in ADB devices list 

### Step - 7: Write the below command and see the results in the emulator.

Command:
adb shell am broadcast -a com.piyush.adbbroadcast.MY_ACTION --es name "SucessfullyReceived"



# Protected Broadcast receivers:
### This is the final and most important broadcast Receiver

## Excercise: Protected/ Permission Broadcast Receiver.

### The permission broadcast allows use to securely broadcast our data and only the application with that permission will be able to read the broadcast.

In this excercise we will create a custom broadcast receiver with permission broadcast receiver.

### Sender app:

### Step - 1: Write code for the activity_custom_broadcast.xml

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:gravity="center"
    android:orientation="vertical"
    android:padding="16dp"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".CustomBroadcastActivity">

    <EditText
        android:id="@+id/editText1"
        android:layout_width="250sp"
        android:layout_height="wrap_content"
        android:hint="Enter first number"
        android:inputType="numberDecimal" />

    <EditText
        android:id="@+id/editText2"
        android:layout_width="250sp"
        android:layout_height="wrap_content"
        android:hint="Enter second number"
        android:inputType="numberDecimal" />

    <TextView
        android:id="@+id/text_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="21sp"
        android:text="Message" />

    <Button
        android:id="@+id/send_broad"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Send Broadcast"/>

</LinearLayout>

### STep - 2: Write code for CustomBroadcastActivity.kt

package com.piyush.broadcastreceiver

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.content.IntentFilter
import android.os.Build
import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.TextView
import androidx.activity.enableEdgeToEdge
import androidx.annotation.RequiresApi
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class CustomBroadcastActivity : AppCompatActivity() {

    private lateinit var editText1: EditText
    private lateinit var editText2: EditText
    private lateinit var textView: TextView

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_custom_broadcast)

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        editText1 = findViewById(R.id.editText1)
        editText2 = findViewById(R.id.editText2)
        textView = findViewById(R.id.text_view)

        findViewById<Button>(R.id.send_broad).setOnClickListener {
            // Get the numbers from the EditText fields
            val number1 = editText1.text.toString().toDoubleOrNull() ?: 0.0
            val number2 = editText2.text.toString().toDoubleOrNull() ?: 0.0

            // Calculate the sum and subtraction
            val sum = number1 + number2
            val sub = number1 - number2

            // Send the broadcast with the sum
            val sumIntent = Intent("com.piyush.receiver.SUM_ACTION").apply {
                putExtra("com.piyush.EXTRA_DATA_SUM", sum.toString())
            }
            sendBroadcast(sumIntent, "com.piyush.EXAMPLE_PERMISSION")

            // Send the broadcast with the subtraction result
            val subIntent = Intent("com.piyush.receiver.SUB_ACTION").apply {
                putExtra("com.piyush.EXTRA_DATA_SUB", sub.toString())
            }
            sendBroadcast(subIntent, "com.piyush.EXAMPLE_PERMISSION")
        }
    }

    @RequiresApi(Build.VERSION_CODES.TIRAMISU)
    override fun onStart() {
        super.onStart()
        // Create the IntentFilter
        val intentFilter = IntentFilter().apply {
            addAction("com.piyush.receiver.SUM_ACTION")
            addAction("com.piyush.receiver.SUB_ACTION")
        }
        // Register the BroadcastReceiver with the appropriate parameters
        registerReceiver(broadcastReceiver, intentFilter, "com.piyush.EXAMPLE_PERMISSION", null, Context.RECEIVER_EXPORTED)
    }

    override fun onStop() {
        super.onStop()
        // Unregister the local broadcast receiver when the activity stops
        unregisterReceiver(broadcastReceiver)
    }

    private val broadcastReceiver: BroadcastReceiver = object : BroadcastReceiver() {
        override fun onReceive(context: Context?, intent: Intent?) {
            when (intent?.action) {
                "com.piyush.receiver.SUM_ACTION" -> {
                    val receivedSum = intent.getStringExtra("com.piyush.EXTRA_DATA_SUM")
                    textView.text = "The Final Sum of the two numbers is: $receivedSum"
                }
                "com.piyush.receiver.SUB_ACTION" -> {
                    val receivedSub = intent.getStringExtra("com.piyush.EXTRA_DATA_SUB")
                    textView.text = "The Broadcast sent Successfully!!"
                }
            }
        }
    }
}


### Step - 3: Make the CustomBroadcastActivity as launcher activity and decalre custom permission in the broadcast Receiver.

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">


    <permission android:name="com.piyush.EXAMPLE_PERMISSION"/>
    <uses-permission android:name="com.piyush.EXAMPLE_PERMISSION"/>
    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Broadcastreceiver"
        tools:targetApi="31">
        <activity
            android:name=".CustomBroadcastActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity
            android:name=".MainActivity" />

    </application>

</manifest>



## Receiver App:

### Step - 1: Write code for activity_custom_broadcast_receiver.xml 

<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".CustomBroadcastReceiver">

    <TextView
        android:id="@+id/textView2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="280dp"
        android:text="The Final Result:"
        android:textSize="34sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/textView"
        android:layout_width="250dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="384dp"
        android:text=""
        android:textSize="21dp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.498"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/textView3"
        android:layout_width="250dp"
        android:layout_height="wrap_content"
        android:layout_marginTop="48dp"
        android:text=""
        android:textSize="21sp"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintHorizontal_bias="0.498"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/textView" />

</androidx.constraintlayout.widget.ConstraintLayout>


### Step - 2: Write code for CustomBroadcastReceiver.kt

package com.piyush.receiver

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.content.IntentFilter
import android.os.Build
import android.os.Bundle
import android.widget.TextView
import androidx.activity.enableEdgeToEdge
import androidx.annotation.RequiresApi
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class CustomBroadcastReceiver : AppCompatActivity() {

    private lateinit var textView: TextView
    private lateinit var textView3: TextView

    @RequiresApi(Build.VERSION_CODES.O)
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_custom_broadcast_receiver)

        textView = findViewById(R.id.textView)
        textView3 = findViewById(R.id.textView3)

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        // Register the broadcast receiver
        val intentFilter = IntentFilter().apply {
            addAction("com.piyush.receiver.SUM_ACTION")
            addAction("com.piyush.receiver.SUB_ACTION")
        }
        registerReceiver(customBroadcast, intentFilter, Context.RECEIVER_EXPORTED)
    }

    override fun onDestroy() {
        super.onDestroy()
        // Unregister the broadcast receiver
        unregisterReceiver(customBroadcast)
    }

    private val customBroadcast = object : BroadcastReceiver() {
        override fun onReceive(context: Context?, intent: Intent?) {
            when (intent?.action) {
                "com.piyush.receiver.SUM_ACTION" -> {
                    val receivedSum = intent.getStringExtra("com.piyush.EXTRA_DATA_SUM")
                    textView.text = "The Sum of the two Numbers is $receivedSum"
                }
                "com.piyush.receiver.SUB_ACTION" -> {
                    val receivedSub = intent.getStringExtra("com.piyush.EXTRA_DATA_SUB")
                    textView3.text = "The Subtraction of the two Numbers is $receivedSub"
                }
            }
        }
    }
}


### Step - 3: Add the receiver in the AndroidManifest.kt

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">


    <uses-permission android:name="com.piyush.EXAMPLE_PERMISSION"/>
    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.Receiver"
        tools:targetApi="31">
        <activity
            android:name=".CustomBroadcastReceiver"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity
            android:name=".MainActivity"
            />
    </application>

</manifest>


The main Activity will remain Same...
