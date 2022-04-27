---
title: "220227-안드로이드SDK설명----"
date: 2022-02-27 00:00:00 +0900
layout: article
tag: 
- HyundaiDev
- HyundaiDev_SDK
categories: 
- Development
- InstallGuide
- SDK
published: true
sidebar:
  nav: SDK_Sidebar
---

Created: April 27, 2022 4:39 PM
Tags: 현대차증권
분류: 현대차증권SDK

### SDK 추가

1. aar 파일을 libs 폴더에 놓습니다

![Image](/assets/images/posts/2022-04-27-22_0214_현대_sdk_안드로이드sdk설명/untitled.png)

TestCaption

1. build.gradle 에 aar 추가 설정합니다

```java
...
dependencies {
    ...
    //implementation fileTree(include: '*.aar', dir: 'libs')

    api files('../../userhabit/lib-debug.aar')

    api 'androidx.core:core-ktx:1.3.2'
    api "org.jetbrains.kotlinx:kotlinx-coroutines-android:1.3.9"
    api "org.jetbrains.kotlin:kotlin-stdlib:1.4.0"
}
```

1. AndroidManifest.xml 에 다음 추가 설정 합니다

```java
...
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

    <application>
        <uses-library
            android:name="org.apache.http.legacy"android:required="false" />
    </application>
</manifest>
```

1. 코드에서 시작 함수 호출

```java
	class MainApplication : Application() {
	    override fun onCreate() {
        	super.onCreate()
		// 라이브러리 다음과 같이 한 줄만 추가하면 됩니다
	        UserHabit.start(/*Application*/this, /*AppKey*/"b3068e50a8afca37a2909990f9b8c0f7efbe2168")
	    }
	}
```

![Image](/assets/images/posts/2022-04-27-22_0214_현대_sdk_안드로이드sdk설명/untitled_1.png)

화면 이름 설정

### 하이어라키

```java
`/**	 * 화면 오픈 정보로 상황에 맞는 표시 작업을 한다
	 * @param infoOpen
	 * @return	 */private boolean procOpenScreen(OpenScreenInfo infoOpen)
	{
		//UserHabit.setHierarchy(m_actMain, "", infoOpen.m_strScreenNo + infoOpen.m_strScreenName);
		UserHabit.setHierarchy(m_actMain, "", infoOpen.m_strScreenName);
		...`
```

```java
public class CtlTabHeader ...
			public void onClick( View v )
			{
				if ( 300 > System.currentTimeMillis() - lastClickMillis )
					return;

				if (v instanceof TextView) {
					TextView t = (TextView)v;
					String parent = "";
					String child = t.getText().toString();
					switch(child) {
						case "최근조회":
						case "보유종목":
							parent = "관심종목";
							break;
						case "호가":
						case "차트":
						case "시간별":
						case "일자별":
						case "거래원":
						case "투자자별":
						case "뉴스": // Lv2
						case "종목토론":
						case "종목정보":
						case "기업정보":
						case "배당":
						case "업종시세":
						case "시간외":
						case "투자의견":
							parent = "주식 현재가"; // Lv1
							break;
					}
					UserHabit.setHierarchy((Activity) v.getContext(), parent, child);
				}
```

```java
public class CtlRadioGroup ...
			public void onChangeChecked(int preIdx, int curIdx) {
				CtlRadio checkedView = (CtlRadio)getChildAt(curIdx);

				if (checkedView instanceof TextView) {
					TextView t = (TextView)checkedView;
					String parent = "";
					String child = t.getText().toString();
					switch(child) {
						case "매매":
						case "신용":
						case "대주":
						case "공매도":
							parent = "일자별";
							break;
						case "전체":
						case "뉴스": // Lv3
						case "공시":
							parent = "뉴스"; // Lv2
							child = "(Lv3)" + child; // 뉴스라는 아이템명이 겹쳐서 하위 뉴스는 다른 이름으로 변경
							break;
					}
					UserHabit.setHierarchy((Activity) checkedView.getContext(), parent, child);
				}
```

**오브젝트 이름 등록**

```java
`@Override
	public boolean initWithXMLAttribute( TBXML reader, TBXMLElement elem )
	{
		// Attribute 지정
		TBXMLAttribute attr = elem.firstAttribute;
		while (attr != null) 
		{
			initProperty(
					reader.attributeName(attr), 
					reader.attributeValue(attr)
			);
			attr = attr.next;
		}
		UserHabit.setObject(this, "TBXENm_" + elem.name); // <-- 추가
		return true;
	}`
```

### 내장 설정

```
서버주소 : "[https://mceext.hmsec.com](https://mceext.hmsec.com/)"
이미지수집모드 : true
```

자세한 설명은 문의해주세요

감사합니다