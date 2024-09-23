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


