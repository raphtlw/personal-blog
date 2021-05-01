---
title: "The Best Method for Creating a Navigation Menu in Your Flutter Apps"
date: 2021-05-01T19:32:28+08:00
draft: false
---

By the end of this tutorial, you will know how to use setState to dynamically update the content of the main screen of your app just by using a few simple lines of code in Flutter. You will also be doing that via implementing a floating navigation menu in your app.

![](https://cdn-images-1.medium.com/max/800/0*XDbyp6V_jwElj9of)

We’ll start with an empty flutter application with a Stateful widget so we can update it when we tap on a menu item.

```dart
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatefulWidget {  
  @override  
  _MyAppState createState() => _MyAppState();  
}

class _MyAppState extends State<MyApp> {  
  @override
  Widget build(BuildContext context) {  
    return Container();  
  }  
}
```

We should also create the classes which represent the screen to switch to when an element of the navigation drawer is tapped.

```dart
**// Place this below the _MyAppState class**

class Item1 extends StatelessWidget {  
  @override
  Widget build(BuildContext context) {  
    return Scaffold(  
      body: Text('Item 1'),  
    );  
  }  
}

class Item2 extends StatelessWidget {  
  @override
  Widget build(BuildContext context) {  
    return Scaffold(  
      body: Text('Item 2'),  
    );  
  }  
}
```

Now, we can proceed with creating the navigation drawer.

Inside `_MyAppState`, create a `MaterialApp` which contains the `AppBar` with the `Drawer`. This creates the foundation for the navigation drawer.

```dart
**// class _MyAppState**

return MaterialApp(  
  title: 'NavBar Tutorial',  
  home: Scaffold(  
    appBar: AppBar(  
      title: Text('NavBar Tutorial'),  
    ),  
    drawer: Drawer(  
      child: ListView(  
        padding: EdgeInsets.zero,  
        children: <Widget>[  
          **// Drawer items go in here**  
        ],  
      ),  
    ),  
  ),  
);
```

We can now put items into the navigation drawer.

```dart
**// class _MyAppState**

drawer: Drawer(  
  child: ListView(  
    padding: EdgeInsets.zero,  
    children: <Widget>[  
      ListTile(  
        title: Text('Item 1'),  
        onTap: () {  
          **// What happens after you tap the navigation item**  
        },  
      ),  
      ListTile(  
        title: Text('Item 2'),  
        onTap: () {  
          **// What happens after you tap the navigation item**  
        },  
      ),  
    ],  
  ),  
),
```

We need to remove the padding so that items do not get squished inside the `Drawer`.

Looking back at the top class, before we call the build function, we should create a `Widget` variable so that we can dynamically update the main screen when we tap something in the navigation drawer.

```dart
**// Your _MyAppState should now look something like this**

class _MyAppState extends State<MyApp> {  
  Widget mainWidget = Item1(); **// Add this here**

@override
  Widget build(BuildContext context) {  
    return MaterialApp(  
      title: 'NavBar Tutorial',  
      home: Scaffold(  
        appBar: AppBar(  
          title: Text('NavBar Tutorial'),  
        ),  
        drawer: Drawer(  
          child: ListView(  
            padding: EdgeInsets.zero,  
            children: <Widget>[  
              ListTile(  
                title: Text('Item 1'),  
                onTap: () {  
                  **// What happens after you tap the navigation item**  
                },  
              ),  
              ListTile(  
                title: Text('Item 2'),  
                onTap: () {  
                  **// What happens after you tap the navigation item**  
                },  
              ),  
            ],  
          ),  
        ),  
      ),  
    );  
  }  
}
```

Now, we can tell flutter what to do when an item from the navigation bar is tapped. 🥳

Inside the `onTap` function, we can tell flutter that we want to update the main screen with the `Item1` widget. How do we do that?

We can use the `setState` function built into Stateful widgets in flutter!

This is how your `Drawer`'s `ListView` should now look like:

```dart
ListTile(  
  title: Text('Item 1'),  
  onTap: () {  
    **// What happens after you tap the navigation item**  
    setState(() {  
      mainWidget = Item1();  
    });  
  },  
),  
ListTile(  
  title: Text('Item 2'),  
  onTap: () {  
    **// What happens after you tap the navigation item**  
    setState(() {  
      mainWidget = Item2();  
    });  
  },  
),
```

Now, when you tap an item in the navigation menu, the main app screen will just update to the tapped item. 🥳

When you tap the item though, the navigation drawer does not close. How can we fix that?

We can do that by using the `Navigator` in flutter to navigate back by 1 screen

```dart
**// Inside onTap**

onTap: () {  
  **// What happens after you tap the navigation item**  
  setState(() {  
    mainWidget = Item1();  
  });  
  Navigator.pop(context);  
},
```

In order for the navigation drawer to work, we still need to do 1 thing; Add a body to the `Scaffold`.

```dart
**// After that, your _MyAppState class should now look something like this**

class _MyAppState extends State<MyApp> {  
  Widget mainWidget = Item1();

  @override
  Widget build(BuildContext context) {  
    return MaterialApp(  
      title: 'NavBar Tutorial',  
      home: Scaffold(  
        appBar: AppBar(  
          title: Text('NavBar Tutorial'),  
        ),  
        drawer: Drawer(  
          child: ListView(  
            padding: EdgeInsets.zero,  
            children: <Widget>[  
              ListTile(  
                title: Text('Item 1'),  
                onTap: () {  
                  **// What happens after you tap the navigation item**  
                  setState(() {  
                    mainWidget = Item1();  
                  });  
                  Navigator.pop(context);  
                },  
              ),  
              ListTile(  
                title: Text('Item 2'),  
                onTap: () {  
                  **// What happens after you tap the navigation item**  
                  setState(() {  
                    mainWidget = Item2();  
                  });  
                  Navigator.pop(context);  
                },  
              ),  
            ],  
          ),  
        ),  
        body: mainWidget, **// Here**  
      ),  
    );  
  }  
}
```

Just like that, you have a navigation menu that doesn’t take you to a new screen but updates the main screen itself. 😋

* * *

Thanks for reading this article. I hope it helps!

Made with ❤ by [Raphael](https://twitter.com/raphtlw).

> Header image by [Christian Wiediger](https://unsplash.com/@christianw) on [Unsplash](https://unsplash.com)
