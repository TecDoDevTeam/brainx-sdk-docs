# BrainX SDK Integration Documentation

### Update Log

| Date       | Version | Log                                                               |
|------------|---------|-------------------------------------------------------------------|
| 2024-07-24 | 1.0.0.1 | 1、Support for Banner and Splash.                                  |
| 2024-08-30 | 2.0.0.1 | 1、Support for RewardVideo and Interstitial.                       |
| 2024-09-19 | 2.0.0.2 | 1、Display optimisation, known issues fixed.                       |
| 2024-10-23 | 2.0.0.3 | 1、Network optimisation, known issues fixed.                       |
| 2024-11-07 | 2.0.0.4 | 1、Fill rate optimisation, known issues fixed.                     |
| 2024-11-15 | 2.0.1.0 | 1、Support for Native, fill rate optimisation, known issues fixed. |
| 2024-11-26 | 2.0.1.1 | 1、Fill rate optimisation, known issues fixed.                     |
| 2025-04-15 | 2.0.1.2 | 1、Banner interface optimization.                                  |
| 2025-06-05 | 2.0.1.3 | 1、Fill rate optimisation, known issues fixed.                     |
| 2025-06-17 | 2.1.0.0 | 1、Adjustment of integration approach, known issues fixed.         |
| 2025-06-18 | 2.1.0.1 | 1、Known issues fixed.                                             |
| 2025-07-31 | 2.1.0.2 | 1、Banner&Native support for video creatives.                   |

## Features

| Ad Type          | Material Support      | Display Mode        | Description                                                                 |
| ---------------- | --------------------- | ------------------- | --------------------------------------------------------------------------- |
| **Splash**       | Image                 | Embedded Display    | Display at app launch, requires >3/4 screen coverage                        |
| **Banner**       | Image or Video        | Embedded Display    | Can be embedded anywhere in the page, supports multiple size specifications |
| **RewardVideo**  | Video                 | Full-Screen Display | Full-screen video playback, users receive rewards after completion          |
| **Interstitial** | Image + Text or Video | Full-Screen Display | Full-screen display, supports image, video combinations                     |
| **Native**       | Image/Video + Text    | Embedded Display    | Fully integrates into app interface, supports template and custom rendering |

## Integration

### Add SDK Implementation

    implementation 'tech.brainx.sdk:brainxsdk:$VERSION'
    //For example: implementation 'tech.brainx.sdk:brainxsdk:2.1.0.2'

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

| TDConfig.Builder        | Description                                                                                                                                   | Necessary                                                                                                                                                                                        |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| setAppId(int appId)     | Set appid                                                                                                                                     | YES                                                                                                                                                                                              |
| setCOPPA(boolean coppa) | Set the configuration of COPPA, true: adult, false: child                                                                                     | No, but if the app needs to be published on Google Play and the release area includes the United States, and the release population includes children, it must be set. The default value is true |
| setGDPR(boolean gdpr)   | Set the configuration of GDPR, true: User has granted the consent, false: User doesn't grant consent                                          | No, but if the app needs to be publised on Google Play and the release area includes the European Union, it must be set. The default value is true                                               |
| setCCPA(boolean ccpa)   | Set the configuration of CCPA, true: "sale" of personal information is permitted, false: user has opted out of "sale" of personal information | No, but if the app needs to be publised on Google Play and the release area includes California, USA, it must be set. The default value is true                                                  |

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

| TDError         | Description           |
| --------------- | --------------------- |
| int getCode()   | Get the error code    |
| String getMsg() | Get the error message |

Related privacy agreements：[GDPR](https://en.wikipedia.org/wiki/General_Data_Protection_Regulation)，[COPPA](https://en.wikipedia.org/wiki/Children%27s_Online_Privacy_Protection_Act)，[CCPA](https://en.wikipedia.org/wiki/California_Consumer_Privacy_Act)

#### 3. Get initialization status

    TDSDK.isInitialized()

### IMPORTANT：The SDK will collect user data during initialization, so please obtain user consent in the relevant areas covered by each privacy agreement, set the agreement results through TDConfig, and then initialize the SDK and perform subsequent advertising activities.

## Splash

####Please contact BrainX to create a Splash ad slot first and obtain the corresponding Placement Id for ad requests.

#### 1. Create object of TDSplashConfig

    TDSplashConfig tdSplashConfig = new TDSplashConfig();
    tdSplashConfig.setAdTimeOut(5);

| TDSplashConfig                 | Description                                    |
| ------------------------------ | ---------------------------------------------- |
| void setAdTimeOut(int seconds) | Set the ad request timeout, at least 3 seconds |

#### 2. Create TDSplashAd instance

    TDSplashAd splashAd = new TDSplashAd("SPLASH_PLACEMENT_ID", tdSplashConfig);

#### 3. Register Ad Event Callback

    splashAd.setListener(new TDSplashAdListener() {
        @Override
        public void onAdLoaded(@NonNull TDSplash tdSplash) {
            // load success, ready to show
            splashAd.show(container)
        }
    
        @Override
        public void onError(@NonNull TDError tdError) {
            // load fail
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

        @Override
        public void onAdShowedFail(@NonNull TDError error) {
            // Ad show failed
        }
    });

| TDSplashAdListener               | Description                                                                                     |
| -------------------------------- | ----------------------------------------------------------------------------------------------- |
| void onAdLoaded(TDSplash tdSplash) | The ad request is successful and the TDSplash instance is returned |
| void onError(TDError tdError)      | The ad request fails and returns a TDError instance.               |
| void onAdClicked()    | Ad click callback, triggered when the user clicks on the ad hotspot                             |
| void onAdDismissed()  | Ad close callback, triggered when the user clicks the skip button or the countdown is completed |
| void onAdShowed()     | Ad display callback, triggered when the ad is effectively displayed                             |
| void onAdShowedFail(TDError error) | Ad show fail callback |

| TDSplash                                            | Description                |
| --------------------------------------------------- | -------------------------- |
| double getBidPrice()                                | Get the price of the Ad    |

| TDSplashAd | Description |
| ---------- | ----------- |
| void show(ViewGroup container) | Show Splash |

#### 4. Load and Display the Ad

    splashAd.load();

Call the load() method to request ad, and show the ad in the container in onAdLoaded callback.

**Note**：Splash needs to occupy more than 3/4 of the screen size, and it cannot be blocked by more than 1/3, otherwise it will not be considered to be displayed normally.

## Banner

####Please contact BrainX to create a Banner ad slot first and obtain the corresponding placement ID for ad requests.

####When creating an ad slot, please specify the required ad size, whether automatic refresh is required, and the automatic refresh interval.

####Currently supported Banner sizes are 320\*50, 300\*250, 320\*90, 728\*90, 800\*600

#### 1. Create object of TDBannerConfig

    TDBannerConfig tdBannerConfig = new TDBannerConfig();
    tdBannerConfig.setAdTimeOut(5);

| TDBannerConfig                 | Description                                    |
| ------------------------------ | ---------------------------------------------- |
| void setAdTimeOut(int seconds) | Set the ad request timeout, at least 3 seconds |

#### 2. Create TDBannerAdView instance

    TDBannerAdView bannerAdView = new TDBannerAdView(context, "BANNER_PLACEMENT_ID", tdBannerConfig);

#### 3. Register Ad Event Callback

    bannerAdView.setListener(new TDBannerAdListener() {
        @Override
        public void onAdLoaded(@NonNull TDBanner tdBanner) {
            // load success
        }
    
        @Override
        public void onError(@NonNull TDError tdError) {
            // load fail
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
            // Ad is shown
        }
    });

| TDBannerAdListener | Description                                                         |
| ------------------ | ------------------------------------------------------------------- |
| void onAdLoaded(TDBanner tdBanner) | The ad request is successful and the TDBanner instance is returned |
| void onError(TDError tdError)      | The ad request fails and returns a TDError instance.               |
| void onAdClicked()    | Ad click callback, triggered when the user clicks on the ad hotspot |
| void onAdDismissed()  | Ad close callback, triggered when the user clicks the skip button   |
| void onAdShowed()     | Ad display callback, triggered when the ad is effectively displayed |

| TDBanner                          | Description                          |
| --------------------------------- | ------------------------------------ |
| double getBidPrice()              | Get the price of the Ad              |
| double getAdWidth()               | Get the width of the Ad              |
| double getAdHeight()              | Get the height of the Ad             |

#### 4. Load and Display the Ad

    bannerAdView.load();
    container.addView(bannerAdView);

Create TDBannerAdView instance, call load() method to request ad, and add bannerAdView to the target container.

**Note**: The Banner cannot be blocked by more than 1/3, otherwise it will not be calculated as normal display.

#### 5. Destory the Ad

    bannerAdView.destroy();

Call this method to reclaim resources when the page is destroyed or the banner no longer needs to be displayed.

## RewardVideo

####Please contact BrainX first to create a RewardVideo ad slot and obtain the corresponding placement ID for ad requests.

####When creating an ad slot, please indicate whether you need S2S reward. If so, you need to provide the reward name, reward quantity and Callback Url.

#### 1、Create object of TDRewardVideoConfig

    // If you need S2S reward, you need to set USER_ID when creating config.
    TDRewardVideoConfig tDRewardVideoConfig = new TDRewardVideoConfig("USER_ID");
    tDRewardVideoConfig.setAdTimeOut(8);

| TDRewardVideoConfig            | Description                                                                        |
| ------------------------------ | ---------------------------------------------------------------------------------- |
| void setAdTimeOut(int seconds) | Set the ad request timeout in seconds, the minimum is 3s, the default is 8 seconds |

#### 2、Create TDRewardVideoAd instance

    TDRewardVideoAd rewardVideoAd = new TDRewardVideoAd("REWARDVIDEO_PLACEMENT_ID", tDRewardVideoConfig);

#### 3、Register Ad Event Callback

    rewardVideoAd.setListener(new TDRewardVideoAdListener() {
        @Override
        public void onAdLoaded(@NonNull TDRewardVideo tdRewardVideo) {
            // load success
        }
    
        @Override
        public void onError(@NonNull TDError tdError) {
            // load fail
        }

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

| TDRewardItem           | Description   |
| ---------------------- | ------------- |
| String getItemName()   | Reward Name   |
| String getItemNumber() | Reward Number |

| TDRewardVideoAdListener                     | Description                                                         |
| ------------------------------------------- | ------------------------------------------------------------------- |
| void onAdLoaded(TDRewardVideo tdRewardVideo) | The ad request is successful and a TDRewardVideo instance is returned. |
| void onError(TDError tdError)                | The ad request failed and a TDError instance was returned.             |
| void onAdShowedFail(TDError error)              | Ad show fail callback                                               |
| void onRewardedSuccess(TDRewardItem rewardItem) | Ad reward success callback                                          |
| void onRewardedFail()                           | Ad reward fail callback                                             |
| void onAdClicked()                              | Ad click callback, triggered when the user clicks on the ad hotspot |
| void onAdDismissed()                            | Ad close callback, triggered when the user clicks the skip button   |
| void onAdShowed()                               | Ad display callback, triggered when the ad is effectively displayed |

| TDRewardVideo                                           | Description                       |
| ------------------------------------------------------- | --------------------------------- |
| double getBidPrice()                                    | Get the price of the Ad           |

| TDRewardVideoAd | Description |
| --------------- | ----------- |
| boolean isReady() | Whether RewardVideo can be played |
| void show() | Show RewardVideo |

**Note**：RewardVideo involves loading video material, which might takes time. In order to ensure the display effect, it is recommended to load the ad in advance! !

#### 4、Load the Ad

    rewardVideoAd.load();

#### 5、 Display the Ad

    if (rewardVideoAd.isReady()) {
        rewardVideoAd.show();
    }

## Interstitial

#### Please contact BrainX first to create an Interstitial ad slot and obtain the corresponding placement ID for ad request.

#### When creating an ad slot, please indicate the type of interstitial required: image; video; image and video  both

#### 1、Create object of TDInterstitialConfig

    TDInterstitialConfig tDInterstitialConfig = new TDInterstitialConfig();
    tDInterstitialConfig.setAdTimeOut(8);

| TDInterstitialConfig           | Description                                                                        |
| ------------------------------ | ---------------------------------------------------------------------------------- |
| void setAdTimeOut(int seconds) | Set the ad request timeout in seconds, the minimum is 3s, the default is 8 seconds |

#### 2、Create TDInterstitialAd instance

    TDInterstitialAd interstitialAd = new TDInterstitialAd("INTER_PLACEMENT_ID", tDInterstitialConfig);

#### 3、Register Ad Event Callback

    interstitialAd.setListener(new TDInterstitialAdListener() {
        @Override
        public void onAdLoaded(@NonNull TDInterstitial tDInterstitial) {
            // load success
        }
    
        @Override
        public void onError(@NonNull TDError tdError) {
            // load fail
        }

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

| TDInterstitialAdListener       | Description                                                         |
| ------------------------------ | ------------------------------------------------------------------- |
| void onAdLoaded(TDInterstitial tDInterstitial) | The ad request is successful and a TDInterstitial instance is returned. |
| void onError(TDError tdError)                  | The ad request failed and a TDError instance was returned.              |
| void onAdShowedFail(TDError error) | Ad show fail callback                                               |
| void onAdClicked()                 | Ad click callback, triggered when the user clicks on the ad hotspot |
| void onAdDismissed()               | Ad close callback, triggered when the user clicks the skip button   |
| void onAdShowed()                  | Ad display callback, triggered when the ad is effectively displayed |

| TDInterstitial                                       | Description                       |
| ---------------------------------------------------- | --------------------------------- |
| double getBidPrice()                                 | Get the price of the Ad           |

| TDInterstitialAd | Description |
| ---------------- | ----------- |
| boolean isReady() | Whether Interstitial can be played |
| void show() | Show Interstitial |

**Note**：Interstitial involves loading video material, which might takes time. In order to ensure the display effect, it is recommended to load the ad in advance! !

#### 4、Load the Ad

    interstitialAd.load();

#### 5、Display the Ad

    if (interstitialAd.isReady()) {
        interstitialAd.show();
    }

## Native

####Please contact BrainX first to create an Native ad slot and obtain the corresponding placement ID for ad request.

#### 1、Create object of TDNativeConfig

    TDNativeConfig tdNativeConfig = new TDNativeConfig(TDNativeConfig.NativeType.TEMPLATE_RENDERING);
    tdNativeConfig.setAdTimeOut(8);

| TDNativeConfig.NativeType | Description        |
| ------------------------- | ------------------ |
| TEMPLATE_RENDERING        | Template rendering |
| SELF_RENDERING            | Self rendering     |

| TDNativeConfig                 | Description                                    |
| ------------------------------ | ---------------------------------------------- |
| void setAdTimeOut(int seconds) | Set the ad request timeout, at least 3 seconds |

#### 2、Create TDNativeAd instance

    TDNativeAd nativeAd = new TDNativeAd("NATIVE_PLACEMENT_ID", tdNativeConfig);

#### 3、Register Ad Event Callback

    nativeAd.setListener(new TDNativeAdListener() {
        @Override
        public void onAdLoaded(@NonNull TDNative tdNative) {
            // load success, call renderForTemplate for template rendering
            if (tdNativeConfig.getNativeType() == TDNativeConfig.NativeType.TEMPLATE_RENDERING) {
                nativeAd.renderForTemplate(context);
            }
        }
    
        @Override
        public void onError(@NonNull TDError tdError) {
            // load fail
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
    
        @Override
        public void onRenderFail(TDError error) {
            // Template Native fail to render
        }
    
        @Override
        public void onRenderSuccess(TDNativeView nativeView) {
            // Template Native rendering is successful
            container.addView(nativeView);
        }
    });

| TDNativeAdListener               | Description                                                       |
| -------------------------------- | ----------------------------------------------------------------- |
| void onAdLoaded(TDNative tdNative) | The ad request is successful and a TDNative instance is returned. |
| void onError(TDError tdError)      | The ad request failed and a TDError instance was returned.        |
| void onAdClicked()                      | Ad click callback, triggered when the user clicks on the ad hotspot |
| void onAdDismissed()                    | Ad close callback, triggered when the user clicks the skip button   |
| void onAdShowed()                       | Ad display callback, triggered when the ad is effectively displayed |
| void onRenderFail(TDError error)                     | Template Native fail to render                                      |
| void onRenderSuccess(TDNativeView view) | Template Native rendering is successful                             |

| TDNative                                                 | Description                                                                                                                                                                              |
| -------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| string getIcon()                                         | Get icon url                                                                                                                                                                             |
| string getTitle()                                        | Get title                                                                                                                                                                                |
| string getDescription()                                  | Get description                                                                                                                                                                          |
| double getRating()                                       | Get rating                                                                                                                                                                               |
| string getCTAText(): String                              | Get CTA button text                                                                                                                                                                      |
| View getAdLogoView(context: Context)                     | Get BrainX logo view                                                                                                                                                                     |
| View getMediaView(context: Context)                      | Get media view                                                                                                                                                                           |
| double getBidPrice()                                     | Get bid price                                                                                                                                                                            |

| TDNativeAd | Description |
| ---------- | ----------- |
| boolean bindViewsForInteraction(ViewGroup container, List<View> creativeViews, View dislikeView) | Bind interaction events for Self-rendering Native, it needs to be called in the main thread. Returning true means the binding is successful, and returning false means the binding fails |
| void renderForTemplate(Context context) | Render template rendering Native |
| void destroy() | Destroy the Ad and recycle resources |

**Note**: MediaContent is obtained from TDMediaView and is used to control video content playback.

| MediaContent | Description |
| ------------ | ----------- |
| VideoController getVideoController() | Get video controller |
| float getAspectRatio() | Get aspect ratio of media content (width/height) |
| long getDuration() | Get video duration in milliseconds, only valid when video content exists, otherwise returns 0 |
| boolean hasVideoContent() | Check if video content is included |

| VideoController | Description |
| --------------- | ----------- |
| boolean isVideoPlaying | Whether video is playing |
| boolean isVideoEnd | Whether video playback has ended |
| boolean isVideoPaused | Whether video is paused |
| boolean isVideoMuted | Whether video is muted |
| void replay() | Play video (if not played before) or replay (if already finished) |
| void mute() | Mute video |
| void unmute() | Unmute video |
| void pause(boolean pausedByUser) | Pause video playback |
| void resume() | Resume video playback |
| void togglePlayState(boolean byUser) | Toggle play state |
| void toggleMuteState() | Toggle mute state |
| boolean hasVideoContent() | Check if video content is included |
| void setVideoEventListener(VideoListener listener) | Set video event listener |

#### 4、Load the Ad

    nativeAd.load();

#### 5、Render

```
//Template Rendering
// Call in onAdLoaded callback
nativeAd.renderForTemplate(context);
```

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

- When TEMPLATE_RENDERING is passed in, it means that this Native is template rendering, call renderForTemplate(Context context) in onAdLoaded callback to render the template Native.
- When SELF_RENDERING is passed in, you need to write your own native view, obtain the ad material through the TDNative object and render the native view yourself. And call the bindViewsForInteraction(ViewGroup container, List<View> creativeViews, View dislikeView) interface to bind interactive events and listeners. container represents the root container of the native view, creativeViews represents all material Views, and dislikeView represents the close button.  **All creativeViews and dislikeView must be located in the your native view, MediaView and LogoView must be added to your native view and added in the creativeViews List for binding! !**

#### 6、Display the Ad

    container.addView(nativeView)

Add the ad View to the target container for display.

**Notice：**Native cannot be blocked by more than 1/3, otherwise it will not be considered to be displayed normally.

#### 7、Destroy the Ad

    nativeAd.destroy()

Call this method to reclaim resources when the page is destroyed or the native ad no longer needs to be displayed.

## Privacy

BrainX will collect device information and GAID and report this data to determine the user ID. If the app needs to be published on Google Play, you need to declare the terms of use on the Google Play Developer Console and in the Privacy Policy Agreement.   
[Brainx End User Data Privacy Policy](https://brainx.tech/privacy/users)  
[Brainx Publisher Agreement](https://brainx.tech/publisher)   
[Brainx Partner Data Privacy Policy](https://brainx.tech/partner)    

## Support for mediation

| Mediation  | Ad type                         | Network Adapter version | Implementation                                              |
| ---------- | ------------------------------- | ----------------------- | ----------------------------------------------------------- |
| TradPlus   | Splash、Banner、RewardVideo、Inter | 1101                    | implementation 'tech.brainx.sdk:network-tradplus:1.1.0.1'   |
| Topon      | Splash、Banner、RewardVideo、Inter | 1101                    | implementation 'tech.brainx.sdk:network-topon:1.1.0.1'      |
| IronSource | Banner、RewardVideo、Inter        | 1101                    | implementation 'tech.brainx.sdk:network-ironsource:1.1.0.1' |
| Max        | Banner、RewardVideo、Inter        | 1101                    | implementation 'tech.brainx.sdk:network-max:1.1.0.1'        |
| Admob      | Banner、RewardVideo、Inter        | 1101                    | implementation 'tech.brainx.sdk:network-admob:1.1.0.1'      |