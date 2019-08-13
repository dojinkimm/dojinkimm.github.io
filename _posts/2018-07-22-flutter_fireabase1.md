---
layout: post
title: "[Flutter] Google Sign in with Flutter #1"
date: 2018-07-22 12:00:00
author: Dojin Kim
categories: Flutter
tags: flutter mobile anroid ios
cover:  "/assets/imgs/flutter/flutter_cover.png"
---

## Flutter and Firebase 로 Google Sign In 만들기 #1(Android편)


<h3>Step 1: Flutter 사용할 IDE로 새로운 프로젝트 생성</h3>
- IntelliJ, VScode, Android Studio 등 Flutter 프로젝트를 만들 수 있는 IDE로 새로운 프로젝트 만들기.
<img src="{{ site.baseurl }}/assets/img/flutter/intelli.png"/>

<br>
<h3>Step 2: Firebase login후 project생성하기 파트1</h3>
<h5>1. 프로젝트를 생성한다.</h5>

<img src="{{ site.baseurl }}/assets/img/flutter/fb0.png"/>


<h5>2. 화면 우측 상단에 다른 앱추가를 클릭하고 Android 앱에 Firebase 추가 누르기</h5>

<img src="{{ site.baseurl }}/assets/img/flutter/fb1.png"/>


<h5>3.IDE 에 들어가서 android/app/src 밑에 있는 AndroidManifest.xml을 누른다</h5>
<h5>4.맨 위에 package = "-------------" 이 부분만 복사를 한다.</h5>

<img src="{{ site.baseurl }}/assets/img/flutter/manifest.png"/>

<h5>5.그 이름을 Android 패키지 이름에다 복사를 한다 (앱 닉네임은 선택사항이다)</h5>
<h5>6.디버그 서명 인증서 옆에 물음표 버튼을 누르고 이 페이지를 참조를 클릭한다.</h5>
<img src="{{ site.baseurl }}/assets/img/flutter/fb2.png"/>

<h5>7.페이지에 들어가서 OS에 따라서 하이라이트된 부분을 복사한다.</h5>
<img src="{{ site.baseurl }}/assets/img/flutter/fb3.png"/>

<h5>8. 복사한 부분을 터미널에 붙혀놓기를 하고 비밀번호는 "android" 혹은 그냥 Enter를 치면 다음과 같은 화면이 나온다. </h5>
- SHA1: 뒤에 부분을 복사를 하고 firabse 사이트로 들어가서 붙혀놓기를 한다.
- 다음을 눌러서 다음 페이지로 넘어간다.
<img src="{{ site.baseurl }}/assets/img/flutter/fb4.png"/>
<img src="{{ site.baseurl }}/assets/img/flutter/fb6.png"/>
<br>

<h3>Step 3: Flutter 사용할 IDE로 새로운 프로젝트 생성 파트2</h3>
<h5>1. 파일을 다운로드 한다.</h5>
<img src="{{ site.baseurl }}/assets/img/flutter/fb7.png"/>
<h5>2. 다운로드 한 파일을 android/app 폴더에 복사를 한다.</h5>
<img src="{{ site.baseurl }}/assets/img/flutter/fb8.png"/>



