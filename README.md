# flutter_fingerprint_auth

Fingerprint Authentication in Flutter

## Introduction
In this article we are talking about fingerprint authentication. As far as we have discussed about authentication like google, facebook, phone and email/password that will need authentication at server side. Fingerprint authentication is type of local authentication you can also include other biometric authentication like face,voice recognition as well in local authentication. We are only covering fingerprint authentication in this article. So tie your seat belt and let’s start :)))
I have found a plugin for local authentication and we are going to use it for fingerprint authentication.

![alt text](https://raw.githubusercontent.com/myvsparth/flutter_fingerprint_auth/master/screenshots/screenshot.png)

Prerequisite: fingerprint sensor in mobile device you are testing on.

## Steps

1. Create new flutter project. I have created a new project named as “flutter_fingerprint_auth”

2. Add a dependency for plugin “local_auth” in file “pubspec.yaml” in project root directory

```
dependencies:
 flutter:
   sdk: flutter
 cupertino_icons: ^0.1.2
 local_auth: ^0.4.0+1
```
NOTE: I have tried local_auth version 0.5.2+3 but it is not working but version 0.4.0+1 working for me.
	And run flutter packages get

3. Add a permission for android in android/app/src/main/AndroidManifest.xml
```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.example.app">
  <uses-permission android:name="android.permission.USE_FINGERPRINT"/>
<manifest>
```

4. I have put some main functions for understanding the whole authentication process. Please read the comment in code which explain you the process. Please check below code. I have also given my github project directory link below

```
import 'package:flutter/material.dart';
//1. imported local authentication plugin
import 'package:local_auth/local_auth.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: MyHomePage(title: 'Fingerprint Authentication'),
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);
  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  // 2. created object of localauthentication class
  final LocalAuthentication _localAuthentication = LocalAuthentication();
  // 3. variable for track whether your device support local authentication means
  //    have fingerprint or face recognization sensor or not
  bool _hasFingerPrintSupport = false;
  // 4. we will set state whether user authorized or not
  String _authorizedOrNot = "Not Authorized";
  // 5. list of avalable biometric authentication supports of your device will be saved in this array
  List<BiometricType> _availableBuimetricType = List<BiometricType>();

  Future<void> _getBiometricsSupport() async {
    // 6. this method checks whether your device has biometric support or not
    bool hasFingerPrintSupport = false;
    try {
      hasFingerPrintSupport = await _localAuthentication.canCheckBiometrics;
    } catch (e) {
      print(e);
    }
    if (!mounted) return;
    setState(() {
      _hasFingerPrintSupport = hasFingerPrintSupport;
    });
  }

  Future<void> _getAvailableSupport() async {
    // 7. this method fetches all the available biometric supports of the device
    List<BiometricType> availableBuimetricType = List<BiometricType>();
    try {
      availableBuimetricType =
          await _localAuthentication.getAvailableBiometrics();
    } catch (e) {
      print(e);
    }
    if (!mounted) return;
    setState(() {
      _availableBuimetricType = availableBuimetricType;
    });
  }

  Future<void> _authenticateMe() async {
    // 8. this method opens a dialog for fingerprint authentication.
    //    we do not need to create a dialog nut it popsup from device natively.
    bool authenticated = false;
    try {
      authenticated = await _localAuthentication.authenticateWithBiometrics(
        localizedReason: "Authenticate for Testing", // message for dialog
        useErrorDialogs: true,// show error in dialog
        stickyAuth: true,// native process
      );
    } catch (e) {
      print(e);
    }
    if (!mounted) return;
    setState(() {
      _authorizedOrNot = authenticated ? "Authorized" : "Not Authorized";
    });
  }

  @override
  void initState() {
    _getBiometricsSupport();
    _getAvailableSupport();
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text("Has FingerPrint Support : $_hasFingerPrintSupport"),
            Text(
                "List of Biometrics Support: ${_availableBuimetricType.toString()}"),
            Text("Authorized : $_authorizedOrNot"),
            RaisedButton(
              child: Text("Authorize Now"),
              color: Colors.green,
              onPressed: _authenticateMe,
            ),
          ],
        ),
      ),
    );
  }
}
```

## MAY BE ERRORS:
    Error: import androidx.annotation.NonNull;
    Solution : 
    Put android.useAndroidX=true
    android.enableJetifier=true
    In android/gradle.properties file

## Git Repo:
- https://github.com/myvsparth/flutter_fingerprint_auth

## Conclusion:
- This article is all about fingerprint authentication in android you can also use this for ios but you need to add permission in ios which is defined in plugins documentation. You can use this authentication when you need very high level of security like making payments or any confidential transactions.


For help getting started with Flutter, view  
[online documentation](https://flutter.dev/docs), which offers tutorials, 
samples, guidance on mobile development, and a full API reference.
