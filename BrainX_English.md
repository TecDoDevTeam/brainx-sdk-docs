

# BrainX SDK Integration Documentation


### Update Log
| Date | Version | Log |
|--|--|--|
| 2024-07-24 | 1.0.0.1 | 1、Support for Banner and Splash. |
| 2024-08-30 | 2.0.0.1 | 1、Support for RewardVideo and Interstitial. |
| 2024-09-19 | 2.0.0.2 | 1、Display optimisation, known issues fixed. |
| 2024-10-23 | 2.0.0.3 | 1、Network optimisation, known issues fixed. |
| 2024-11-07 | 2.0.0.4 | 1、Fill rate optimisation, known issues fixed. |
| 2024-11-15 | 2.0.1.0 | 1、Support for Native, fill rate optimisation, known issues fixed. |

## Integration

### Add SDK Implementation

    implementation 'tech.brainx.sdk:brainxsdk:$VERSION'
	//For example: implementation 'tech.brainx.sdk:brainxsdk:2.0.1.0'

### Add SDK-dependent permission declaration

	<uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />


### SDK Proguard rules
    
	-keep class com.td.** { *; }
	-keepclassmembers class com.td.** {
	    @com.td.* public *;
	}

## Initialization

#### Please contact BrainX to create an application and get the App Id for initialization.

#### 1. Create object of TDConfig.

	TDConfig tdConfig = new TDConfig.Builder()
			.setAppId(APP_ID)
			.setCOPPA(true)
			.setGDPR(true)
			.setCCPA(true)
			.build();

|TDConfig.Builder|Description|Necessary|
|---|---|---|
|setAppId(int appId)| Set appid | YES |
|setCOPPA(boolean coppa)| Set the configuration of COPPA, true: adult, false: child | No, but if the app needs to be published on Google Play and the release area includes the United States, and the release population includes children, it must be set. The default value is true | 
|setGDPR(boolean gdpr)| Set the configuration of GDPR, true: User has granted the consent, false: User doesn't grant consent | No, but if the app needs to be publised on Google Play and the release area includes the European Union, it must be set. The default value is true |
|setCCPA(boolean ccpa)| Set the configuration of CCPA, true: "sale" of personal information is permitted, false: user has opted out of "sale" of personal information | No, but if the app needs to be publised on Google Play and the release area includes California, USA, it must be set. The default value is true |

#### 2. Initialize the SDK
	
	TDSDK.init(context, tdConfig, new TDSDK.initCallback(){
		@Override
        public void onSDKInitSuccess() {
            // Initialization is successful, proceed with subsequent advertising operations
    	}

        @Override
        public void onSDKInitFail(@NonNull TDError tdError) {
			// Initialization is failed.
        }
	})

|TDError|Description|
|---|---|
|int getCode()| Get the error code |
|String getMsg()| Get the error message |


Related privacy agreements：[GDPR](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation)，[COPPA](https://en.wikipedia.org/wiki/Children%27s_Online_Privacy_Protection_Act)，[CCPA](https://en.wikipedia.org/wiki/California_Consumer_Privacy_Act)

#### 3. Get initialization status
	
	TDSDK.isInitialized()

### IMPORTANT：The SDK will collect user data during initialization, so please obtain user consent in the relevant areas covered by each privacy agreement, set the agreement results through TDConfig, and then initialize the SDK and perform subsequent advertising activities.

## Splash

####Please contact BrainX to create a Splash ad slot first and obtain the corresponding Placement Id for ad requests.
	
#### 1. Create object of TDSplashConfig 
	
	TDSplashConfig tdSplashConfig = new TDSplashConfig();
	tdSplashConfig.setAdTimeOut(5);

|TDSplashConfig|Description|
|---|---|
|void setAdTimeOut(int seconds)| Set the ad request timeout, at least 3 seconds |

#### 2. Load a Splash Ad

	TDSplash.load("SPLASH_PLACEMENT_ID", tdSplashConfig, new TDSplashLoadListener() {
		@Override
        public void onAdLoaded(@NonNull TDSplash tdSplash) {
			// load success
			this.tdSplash = tdSplash;
        }

        @Override
        public void onError(@NonNull TDError tdError) {
			// load fail
        }
    });

|TDSplashLoadListener|Description|
|---|---|
|void onAdLoaded(TDSplash tdSplash)| The ad request is successful and the TDSplash instance is returned |
|void onError(TDError tdError)| The ad request fails and returns a TDError instance. |

|TDSplash|Description|
|---|---|
|View getAdView()| Get the adview for display |
|void setEventListener(eventListener: TDSplashEventListener)| Set event listener for ad |
|double getBidPrice()| Get the price of the Ad |

#### 3. Register Ad Event Callback

	tdSplash.setEventListener(new TDSplashEventListener() {
		@Override
		public void onAdClicked() {
			// Ad is clicked
		}
		
		@Override
		public void onAdDismissed() {
			// Ad is dismissed
			SplashActivity.this.finish();
		}
		
		@Override
		public void onAdShowed() {
			// Ad is showed
		}
	});

|TDSplashEventListener|Description|
|---|---|
|void onAdClicked()| Ad click callback, triggered when the user clicks on the ad hotspot |
|void onAdDismissed()| Ad close callback, triggered when the user clicks the skip button or the countdown is completed |
|void onAdShowed()| Ad display callback, triggered when the ad is effectively displayed |

#### 4. Display the Ad

	View adView = tdSplash.getAdView();
	container.addView(adView)

Call the getAdView() method of the TDSplash instance to obtain the ad View, and add the ad View to the target container for display.

**Note**：Splash needs to occupy more than 3/4 of the screen size, and it cannot be blocked by more than 1/3, otherwise it will not be considered to be displayed normally.

## Banner

####Please contact BrainX to create a Banner ad slot first and obtain the corresponding placement ID for ad requests.

####When creating an ad slot, please specify the required ad size, whether automatic refresh is required, and the automatic refresh interval.

####Currently supported Banner sizes are 320\*50, 300\*250, 320\*90, 728\*90, 800\*600

#### 1. Create object of TDBannerConfig

	TDBannerConfig tdBannerConfig = new TDBannerConfig(TDBannerConfig.BannerSize.W_320_H_50);
	tdBannerConfig.setAdTimeOut(5);

|TDBannerConfig.BannerSize|Description|
|---|---|
|W_320_H_50| 320 * 50 |
|W_300_H_250| 300 * 250 |
|W_320_H_90| 320 * 90 |
|W_728_H_90| 728 * 90 |
|W_800_H_600| 800 * 600 |

|TDBannerConfig|Description|
|---|---|
|void setAdTimeOut(int seconds)| Set the ad request timeout, at least 3 seconds |

**Note**：The size of the ad creative is determined only by the size selected when creating the ad slot. To ensure the best display effect, the BannerSize passed in here needs to be consistent with the size of the ad slot.

#### 2. Load a Banner Ad

	TDBanner.load("BANNER_PLACEMENT_ID", tdBannerConfig, new TDBannerLoadListener() {
		@Override
		public void onAdLoaded(@NonNull TDBanner tdBanner) {
			// load success
			this.tdBanner = tdBanner
		}

        @Override
		public void onError(@NonNull TDError tdError) {
			// load fail
		}
	});


|TDBannerLoadListener|Description|
|---|---|
|void onAdLoaded(TDBanner tdBanner)| The ad request is successful and the TDBanner instance is returned |
|void onError(TDError tdError)| The ad request fails and returns a TDError instance. |

|TDBanner|Description|
|---|---|
|View getAdView()| Get the AdView for display |
|void setEventListener(eventListener: TDBannerEventListener)| Set event listener for the Ad |
|double getBidPrice() | Get the price of the Ad |
|void destroy() | Destroy the Ad and recycle resources |

#### 3. Register Ad Event Callback

	tdBanner.setEventListener(new TDBannerEventListener() {
		@Override
		public void onAdClicked() {
			// Ad is clicked
		}

		@Override
		public void onAdDismissed() {
			// Ad is dismissed
		}

		@Override
		public void onAdShowed() {
			// Ad is shown
		}
	});

|TDBannerEventListener|Description|
|---|---|
|void onAdClicked()| Ad click callback, triggered when the user clicks on the ad hotspot |
|void onAdDismissed()| Ad close callback, triggered when the user clicks the skip button |
|void onAdShowed()| Ad display callback, triggered when the ad is effectively displayed |

#### 4. Display the Ad

	View adView = tdBanner.getAdView();
	// You can also set the width and height of adView yourself
	// adView.setLayoutParams(new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT))
	container.addView(adView)

Call the getAdView() method of the TDBanner instance to obtain the ad View, and add the ad View to the target container for display.      

**Note**: The size of the ad creative is only determined by the size selected when creating the ad slot. The BannerSize passed in here is only used to preset a width and height for AdView. To ensure the best display effect, the BannerSize passed in here needs to be consistent with the size selected when creating the ad slot. Of course, you can also set the width and height for AdView yourself after obtaining AdView.

#### 5. Destory the Ad

	tdBanner.destroy()

Call this method to reclaim resources when the page is destroyed or the banner no longer needs to be displayed.

## RewardVideo

####Please contact BrainX first to create a RewardVideo ad slot and obtain the corresponding placement ID for ad requests.

####When creating an ad slot, please indicate whether you need S2S reward. If so, you need to provide the reward name, reward quantity and Callback Url.

#### 1、Create object of TDRewardVideoConfig 
	
	// If you need S2S reward, you need to set USER_ID when creating config.
	TDRewardVideoConfig tDRewardVideoConfig = new TDRewardVideoConfig("USER_ID");
	tDRewardVideoConfig.setAdTimeOut(8);

|TDRewardVideoConfig|Description|
|---|---|
|void setAdTimeOut(int seconds)| Set the ad request timeout in seconds, the minimum is 3s, the default is 8 seconds |


#### 2、Load a Reward Video

	TDRewardVideo.load("REWARDVIDEO_PLACEMENT_ID", tDRewardVideoConfig, new TDRewardVideoLoadListener() {
		@Override
		public void onAdLoaded(@NonNull TDRewardVideo tdRewardVideo) {
			// load success
			this.tdRewardVideo = tdRewardVideo
		}

        @Override
		public void onError(@NonNull TDError tdError) {
			// load fail
		}
	});


|TDRewardVideoLoadListener|Description|
|---|---|
|void onAdLoaded(TDRewardVideo tdRewardVideo)| The ad request is successful and a TDRewardVideo instance is returned. |
|void onError(TDError tdError)| The ad request failed and a TDError instance was returned. |


|TDRewardVideo|Description|
|---|---|
|boolean isReady()| Whether RewardVideo can be played |
|void show()| Show RewardVideo |
|void setEventListener(TDRewardVideoEventListener eventListener)|  Set event listener for the Ad |
|double getBidPrice() | Get the price of the Ad |

**Note**：RewardVideo involves loading video material, which might takes time. In order to ensure the display effect, it is recommended to load the ad in advance! !

#### 3、Register Ad Event Callback

	tdRewardVideo.setEventListener(new TDRewardVideoEventListener() {
		@Override
        public void onAdShowedFail(@NonNull TDError error) {
			// Ad fail to show
        }

		@Override
        public void onRewardedSuccess(@NonNull TDRewardItem rewardItem) {
			// reward success
        }

        @Override
        public void onRewardedFail() {
			// reward fail
        }

		@Override
		public void onAdClicked() {
			// Ad is clicked
		}

		@Override
		public void onAdDismissed() {
			// Ad is dismissed
		}

		@Override
		public void onAdShowed() {
			// Ad is showed
		}
	});

|TDRewardItem|Description|
|---|---|
|String getItemName()| Reward Name |
|String getItemNumber()| Reward Number |

|TDRewardVideoEventListener|Description|
|---|---|
|void onAdShowedFail(TDError error)| Ad show fail callback |
|void onRewardedSuccess(TDRewardItem rewardItem)| Ad reward success callback |
|void onRewardedFail()| Ad reward fail callback |
|void onAdClicked()| Ad click callback, triggered when the user clicks on the ad hotspot |
|void onAdDismissed()| Ad close callback, triggered when the user clicks the skip button |
|void onAdShowed()| Ad display callback, triggered when the ad is effectively displayed |

#### 4、 Display the Ad

	if (tdRewardVideo.isReady()) {
		tdRewardVideo.show();
	}

## Interstitial

#### Please contact BrainX first to create an Interstitial ad slot and obtain the corresponding placement ID for ad request.

#### When creating an ad slot, please indicate the type of interstitial required: image; video; image and video  both

#### 1、Create object of TDInterstitialConfig 
	
	TDInterstitialConfig tDInterstitialConfig = new TDInterstitialConfig();
	tDInterstitialConfig.setAdTimeOut(8);

|TDInterstitialConfig|Description|
|---|---|
|void setAdTimeOut(int seconds)| Set the ad request timeout in seconds, the minimum is 3s, the default is 8 seconds |


#### 2、Load an Interstitial

	TDInterstitial.load("INTER_PLACEMENT_ID", tDInterstitialConfig, new TDInterstitialLoadListener() {
		@Override
		public void onAdLoaded(@NonNull TDInterstitial tDInterstitial) {
			// load success
			this.tDInterstitial = tDInterstitial
		}

        @Override
		public void onError(@NonNull TDError tdError) {
			// load fail
		}
	});


|TDInterstitialLoadListener|Description|
|---|---|
|void onAdLoaded(TDInterstitial tDInterstitial)| The ad request is successful and a TDInterstitial instance is returned. |
|void onError(TDError tdError)| The ad request failed and a TDError instance was returned. |


|TDInterstitial|Description|
|---|---|
|boolean isReady()| Whether RewardVideo can be played |
|void show()| Show Interstitial |
|void setEventListener(TDInterstitialEventListener eventListener)| Set event listener for the Ad |
|double getBidPrice() | Get the price of the Ad |

**Note**：Interstitial involves loading video material, which might takes time. In order to ensure the display effect, it is recommended to load the ad in advance! !

#### 3、Register Ad Event Callback

	tDInterstitial.setEventListener(new TDInterstitialLoadListener() {
		@Override
        public void onAdShowedFail(@NonNull TDError error) {
			// Ad fail to show
        }

		@Override
		public void onAdClicked() {
			// Ad is clicked
		}

		@Override
		public void onAdDismissed() {
			// Ad is dismissed
		}

		@Override
		public void onAdShowed() {
			// Ad is showed
		}
	});

|TDInterstitialLoadListener|Description|
|---|---|
|void onAdShowedFail(TDError error)| Ad show fail callback |
|void onAdClicked()| Ad click callback, triggered when the user clicks on the ad hotspot |
|void onAdDismissed()| Ad close callback, triggered when the user clicks the skip button |
|void onAdShowed()| Ad display callback, triggered when the ad is effectively displayed |

#### 4、Display the Ad

	if (tDInterstitial.isReady()) {
		tDInterstitial.show();
	}

## Native

####Please contact BrainX first to create an Native ad slot and obtain the corresponding placement ID for ad request.


#### 1、Create object of TDNativeConfig 
	
	TDNativeConfig tdNativeConfig = new TDNativeConfig(TDNativeConfig.NativeType.TEMPLATE_RENDERING);
	tdNativeConfig.setAdTimeOut(8);

|TDNativeConfig.NativeType|Description|
|---|---|
|TEMPLATE_RENDERING| Template rendering |
|SELF_RENDERING| Self rendering |

|TDNativeConfig|Description|
|---|---|
|void setAdTimeOut(int seconds)| Set the ad request timeout, at least 3 seconds |


#### 2、Load a Native

	TDNative.load("NATIVE_PLACEMENT_ID", tdNativeConfig, new TDNativeLoadListener() {
		@Override
		public void onAdLoaded(@NonNull TDNative tdNative) {
			// load success
			this.tdNative = tdNative
		}

        @Override
		public void onError(@NonNull TDError tdError) {
			// load fail
		}
	});

|TDNativeLoadListener|Description|
|---|---|
|void onAdLoaded(TDNative tdNative)| The ad request is successful and a TDNative instance is returned. |
|void onError(TDError tdError)| The ad request failed and a TDError instance was returned. |

|TDNative|Description|
|---|---|
|boolean bindViewsForInteraction(ViewGroup container, List<View> creativeViews, View dislikeView) | Bind interaction events for Self-rendering Native, it needs to be called in the main thread. Returning true means the binding is successful, and returning false means the binding fails |
|void renderForTemplate(Context context) | Render template rendering Native |
|string getIcon() | Get icon url |
|string getTitle() | Get title |
|string getDescription() | Get description |
|double getRating() | Get rating |
|string getCTAText(): String | Get CTA button text |
|View getAdLogoView(context: Context) | Get BrainX logo view |
|View getMediaView(context: Context) | Get media view |
|void setEventListener(TDNativeEventListener eventListener)|  Set event listener for the Ad |
|double getBidPrice() | Get bid price |

#### 3、Register Ad Event Callback

	tdNative.setEventListener(new TDNativeEventListener() {
		@Override
		public void onAdClicked() {
			// Ad is clicked
		}

		@Override
		public void onAdDismissed() {
			// Ad is dismissed
		}

		@Override
		public void onAdShowed() {
			// Ad is showed
		}

		@Override
		public void onRenderFail() {
			// Template Native fail to render
		}

		@Override
		public void onRenderSuccess(TDNativeView nativeView) {
			// Template Native rendering is successful
		}
	});

|TDNativeEventListener|Description|
|---|---|
|void onAdClicked()| Ad click callback, triggered when the user clicks on the ad hotspot |
|void onAdDismissed()| Ad close callback, triggered when the user clicks the skip button |
|void onAdShowed()| Ad display callback, triggered when the ad is effectively displayed |
|void onRenderFail()| Template Native fail to render |
|void onRenderSuccess(TDNativeView view)| Template Native rendering is successful |

#### 4、Render
~~~
//Template Rendering
tdNative.renderForTemplate(NativeActivity.this);
~~~
	
	//Self Rendering
	ViewGroup nativeView = (ViewGroup) LayoutInflater.from(this).inflate(R.layout.native_template, null, false);
	ArrayList<View> creativeViews = new ArrayList<>();
	
	//icon view
	ImageView iconIV = nativeView.findViewById(R.id.iv_icon_native_template);
	if (nativeAd.getIcon().isEmpty()) {
	    iconIV.setVisibility(View.GONE);
	} else {
	    Glide.with(this).load(nativeAd.getIcon()).into(iconIV);
	    creativeViews.add(iconIV);
	}

	//title view
	TextView titleTV = nativeView.findViewById(R.id.tv_title_native_template);
	titleTV.setText(nativeAd.getTitle());
	creativeViews.add(titleTV);

	//description view
	TextView descTV = nativeView.findViewById(R.id.tv_desc_native_template);
	if (nativeAd.getDescription().isEmpty()) {
	    descTV.setVisibility(View.GONE);
	} else {
	    descTV.setText(nativeAd.getDescription());
	    creativeViews.add(descTV);
	}

	//media view
	ViewGroup mediaViewContainer = nativeView.findViewById(R.id.container_media_native_template);
	View mediaView = nativeAd.getMediaView(NativeActivity.this);
	mediaViewContainer.addView(mediaView, FrameLayout.LayoutParams.MATCH_PARENT, FrameLayout.LayoutParams.MATCH_PARENT);
	creativeViews.add(mediaView);

	//cta button
	TextView ctaBtn = nativeView.findViewById(R.id.btn_cta_native_template);
	ctaBtn.setText(nativeAd.getCTAText());
	creativeViews.add(ctaBtn);

	//logo view
	View LogoView = nativeAd.getAdLogoView(NativeActivity.this);
	creativeViews.add(LogoView);

	//dislike view
	View dislikeView = nativeView.findViewById(R.id.btn_close_native_template);	

	//bind
	boolean bindResult = nativeAd.bindViewsForInteraction(nativeView, creativeViews, dislikeView);

**Note**: The NativeType passed in by TDNativeConfig represents the type of Native:

- When TEMPLATE_RENDERING is passed in, it means that this Native is template rendering, and renderForTemplate(Context context) should be called to render the template Native.
- When SELF_RENDERING is passed in, you need to write your own native view, obtain the ad material through the TDNative object and render the native view yourself. And call the bindViewsForInteraction(ViewGroup container, List<View> creativeViews, View dislikeView) interface to bind interactive events and listeners. container represents the root container of the native view, creativeViews represents all material Views, and dislikeView represents the close button.  **All creativeViews and dislikeView must be located in the your native view, MediaView and LogoView must be added to your native view and added in the creativeViews List for binding! !**

#### 5、Display the Ad

	adContainer.addView(nativeView)

Add the ad View to the target container for display.   
    
**Notice：**Native cannot be blocked by more than 1/3, otherwise it will not be considered to be displayed normally.

#### 6、Destroy the Ad

	tdNative.destroy()

Call this method to reclaim resources when the page is destroyed or the native ad no longer needs to be displayed.

## Privacy 
BrainX will collect device information and GAID and report this data to determine the user ID. If the app needs to be published on Google Play, you need to declare the terms of use on the Google Play Developer Console and in the Privacy Policy Agreement.   
[Brainx End User Data Privacy Policy](https://brainx.tech/privacy/users)  
[Brainx Publisher Agreement](https://brainx.tech/publisher)   
[Brainx Partner Data Privacy Policy](https://brainx.tech/partner)    

## Support for mediation

|Mediation|Ad type|Network Adapter version|Implementation|
|---|---|---|---|
|TradPlus|Splash、Banner、RewardVideo、Inter|1001|implementation 'tech.brainx.sdk:network-tradplus:1.0.0.1'|
|Topon|Splash、Banner、RewardVideo、Inter|1001|implementation 'tech.brainx.sdk:network-topon:1.0.0.1'|
|IronSource|Banner、RewardVideo、Inter|1001|implementation 'tech.brainx.sdk:network-ironsource:1.0.0.1'|
|Max|Banner、RewardVideo、Inter|1001|implementation 'tech.brainx.sdk:network-max:1.0.0.1'|
|Admob|Banner、RewardVideo、Inter|1001|implementation 'tech.brainx.sdk:network-admob:1.0.0.1'|