---
layout: post
title: "[Flutter] Google Sign in with Flutter #2"
date: 2018-07-22 14:00:00
author: Dojin Kim
categories: Flutter
tags: flutter mobile anroid ios
cover:  "/assets/imgs/flutter/flutter_cover.png"
---


## Flutter and Firebase 로 Google Sign In 만들기 #2(Android편)


<h3>Step 1: Google login 가능하게 하는 코드를 작성한다</h3>
<h5>1. google sign in 패키지를 import 한다</h5>
```dart
import 'package:google_sign_in/google_sign_in.dart';
```
<h5>2. google sign in 코드를 작성한다. </h5>

```dart
GoogleSignIn _googleSignIn = new GoogleSignIn(
  scopes: [
    'email',
    'https://www.googleapis.com/auth/contacts.readonly',
  ],
);

```
<img src="{{ site.baseurl }}/assets/img/flutter/fb15.png"/>


<h5>3. _MyHomePageState 부분에서 코드를 다음과 같이 수정한다</h5>
<img src="{{ site.baseurl }}/assets/img/flutter/fb14.png"/>


<h5>4. 밑의 코드를 작성한다.</h5>
- 'dart:async'를 import해야 async가 작동이 될 것이다.
```dart
Future<Null> _handleSignIn() async{
 	 try{
    	await _googleSignIn.signIn();
    	print("signed in "+_googleSignIn.currentUser.displayName);
  	}catch (error){
   	 print(error);
  	}
}
```
<img src="{{ site.baseurl }}/assets/img/flutter/fb16.png"/>


<br><br>
<h3>Step 2: Emulator을 키고 Run 한다</h3>
- 이때 emulator는 google play store을 사용할 수 있는 버전이어야 한다.

<h5>1. 우측 하단의 (+) 사인을 누르면 구글로그인이 가능하게 된다.</h5>
<img src="{{ site.baseurl }}/assets/img/flutter/fb17.png"/>


<h5>이것으로 Firebase와 Flutter를 사용해서 Google Login이 가능하게 되었다.</h5>
