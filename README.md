# Android Navigation codelab

Content: https://codelabs.developers.google.com/codelabs/android-navigation/

Benefits of Jetpack Navigation 
--------------------------------

Automatic handling of fragment transactions

Correctly handling up and back by default

Deep linking as a first class operation

Implementing navigation UI patterns (like navigation drawers and bottom nav) with little additional work

Type safety when passing information while navigating


1. Navigation Graph (New XML resource) - This is a resource that contains all navigation-related information in one centralized location. This includes all the places in your app, known as destinations, and possible paths a user could take through your app.

2. NavHostFragment (Layout XML view) - This is a special widget you add to your layout. It displays different destinations from your Navigation Graph.

3. NavController (Kotlin/Java object) - This is an object that keeps track of the current position within the navigation graph. It orchestrates swapping destination content in the NavHostFragment as you move through a navigation graph.

Navigation Graph:
-------------------------------
<navigation> is the root node of every navigation graph.

<navigation> contains one or more destinations, represented by <activity> or <fragment> elements.

app:startDestination is an attribute that specifies the destination that is launched by default when the user first opens the app.


```
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            xmlns:tools="http://schemas.android.com/tools"
    		app:startDestination="@+id/home_dest">
<fragment
    android:id="@+id/flow_step_one_dest"
    android:name="com.example.android.codelabs.navigation.FlowStepFragment"
    tools:layout="@layout/flow_step_one_fragment">
    <argument
        .../>

    <action
        android:id="@+id/next_action"
        app:destination="@+id/flow_step_two_dest">
    </action>
</fragment>
</navigation>
```


NavHostFragment is hosted in an activity which looks like this:
----------------------------------------------------------------

```
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.example.android.codelabs.navigation.MainActivity">

    <androidx.appcompat.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/colorPrimary"
        android:theme="@style/ThemeOverlay.MaterialComponents.Dark.ActionBar" />

    <fragment
        android:id="@+id/my_nav_host_fragment"
        android:name="androidx.navigation.fragment.NavHostFragment"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        app:defaultNavHost="true"
        app:navGraph="@navigation/mobile_navigation" />

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottom_nav_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:menu="@menu/bottom_nav_menu" />
</LinearLayout>
```

This is a layout for an activity. It contains the global navigation, including a bottom nav and a toolbar

android:name="androidx.navigation.fragment.NavHostFragment" and app:defaultNavHost="true" connect the system back button to the NavHostFragment
app:navGraph="@navigation/mobile_navigation" associates the NavHostFragment with a navigation graph. This navigation graph specifies all the destinations the user can navigate to, in this NavHostFragment.

NavController
--------------

findNavController().navigate(R.id.flow_step_one_dest)

There are a few ways to get a NavController object associated with your NavHostFragment. In Kotlin, it's recommended you use one of the 

following extension functions, depending on whether you're calling the navigation command from within a fragment, activity or view:

```
Fragment.findNavController()
View.findNavController()
Activity.findNavController(viewId: Int)
```

Your NavController is associated with a NavHostFragment. Thus whichever method you use, you must be sure that the fragment, view, or view ID

is either a NavHostFragment itself, or has a NavHostFragment as a parent. Otherwise you will get an IllegalStateException.

Navigation by actions has the following benefits over navigation by destination:

You can visualize the navigation paths through your app

Actions can contain additional associated attributes you can set, such as a 

1. transition animation, 

2. arguments values, 

3. and backstack behavior

You can use the plugin safe args to navigate, which you'll see shortly

```
<fragment
    android:id="@+id/flow_step_one_dest"
    android:name="com.example.android.codelabs.navigation.FlowStepFragment">

    <argument
        .../>

    <action
        android:id="@+id/next_action"
        app:destination="@+id/flow_step_two_dest">
    </action>
</fragment>

<fragment
    android:id="@+id/flow_step_two_dest"
    android:name="com.example.android.codelabs.navigation.FlowStepFragment">
    <!-- ...removed for simplicity-->
</fragment>
```

Notice:

The actions are nested within the destination - this is the destination you will navigate from

The action includes a destination argument referring to flow_step_two_dest; this is the ID of where you will navigate to

The ID for the action is "next_action"


We can navigate between destinations in two ways:

1. findNavController().navigate(R.id.flow_step_one_dest, null, options)

where flow_step_one_dest= destination we want to go to, options is navOptions ( for transition anims )

2. Using actions defined in Navigation graph XML, 


```
 <fragment
        android:id="@+id/home_dest"
        android:name="com.example.android.codelabs.navigation.HomeFragment"
        android:label="@string/home"
        tools:layout="@layout/home_fragment">

        <args...
        />

        <action
            android:id="@+id/next_action"
            app:destination="@id/flow_step_one_dest"
            app:enterAnim="@anim/slide_in_right"
            app:exitAnim="@anim/slide_out_left"
            app:popEnterAnim="@anim/slide_in_left"
            app:popExitAnim="@anim/slide_out_right" />
    </fragment>
```

Navigation.createNavigateOnClickListener(R.id.next_action, null)


Using safe args for navigation
-------------------------------

Safe args allows you to get rid of code like this when passing values between destinations:

```
val username = arguments?.getString("usernameKey")
```

And, instead, replace it with code that has generated setters and getters.

```
val username = args.username
```

Another way of navigating using actions and safe args is by using Direction classes

HomeFragment.kt

```
view.findViewById<Button>(R.id.navigate_action_button)?.setOnClickListener{
        val action = HomeFragmentDirections.nextAction() // you can pass args as well in parameters here optionally. 
        findNavController().navigate(action)
      }
```      

Navigating using menus, drawers and bottom navigation
------------------------------------------------------

See MainActivity for details


Deep Linking to a destination
------------------------------

Deep links are a way to jump into the middle of your app's navigation, whether that's from an actual URL link or a pending intent from a notification.

Navigation provides a NavDeepLinkBuilder class to construct a PendingIntent that will take the user to a specific destination.


Associating a web link with a destination
------------------------------------------

One of the most common uses of a deep link is to allow a web link to open an activity in your app. Traditionally you would use an intent-

filter and associate a URL with the activity you want to open.

The navigation library makes this extremely simple and allows you to map URLs directly to destinations in your navigation graph.

deepLink is an element you can add to a destination in your graph. Each <deepLink> element has a single required attribute: app:uri.

Add a URIbased Deep Link using <deepLink>

In this step, you'll add a deep link to www.example.com.

1. Open mobile_navigation.xml

2. Add a <deepLink> element to the deeplink_dest destination.

```
mobile.navigation.xml

<fragment
    android:id="@+id/deeplink_dest"
    android:name="com.example.android.codelabs.navigation.DeepLinkFragment"
    android:label="@string/deeplink"
    tools:layout="@layout/deeplink_fragment">

    <argument
        android:name="myarg"
        android:defaultValue="Android!"/>

    <deepLink app:uri="www.example.com/{myarg}" />
</fragment>
```

AndroidManifest

```
<activity android:name=".MainActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>

    <nav-graph android:value="@navigation/mobile_navigation" />
</activity>
```

For navigation through menu item, dest id and menu id must be same. 

```
override fun onOptionsItemSelected(item: MenuItem): Boolean {
    return item.onNavDestinationSelected(findNavController())
        || super.onOptionsItemSelected(item)
  }
```

License
-------

Copyright 2018 The Android Open Source Project, Inc.

Licensed to the Apache Software Foundation (ASF) under one or more contributor
license agreements.  See the NOTICE file distributed with this work for
additional information regarding copyright ownership.  The ASF licenses this
file to you under the Apache License, Version 2.0 (the "License"); you may not
use this file except in compliance with the License.  You may obtain a copy of
the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.  See the
License for the specific language governing permissions and limitations under
the License.
