# BrainX SDK集成文档


### 更新日志
| 日期         | 版本      | 日志                            |
|------------|---------|-------------------------------|
| 2024-07-24 | 1.0.0.1 | 1、Banner、Splash支持。            |
| 2024-08-30 | 2.0.0.1 | 1、RewardVideo、Interstitial支持。 |
| 2024-09-19 | 2.0.0.2 | 1、展示效果优化，已知问题修复。              |
| 2024-10-23 | 2.0.0.3 | 1、网络请求优化，已知问题修复。              |
| 2024-11-07 | 2.0.0.4 | 1、优化填充效果，已知问题修复。              |
| 2024-11-15 | 2.0.1.0 | 1、Native广告支持，优化填充效果，修复已知问题。   |
| 2024-11-26 | 2.0.1.1 | 1、优化填充效果，修复已知问题。              |
| 2025-04-15 | 2.0.1.2 | 1、Banner接入接口优化。               |
| 2025-06-05 | 2.0.1.3 | 1、优化填充效果，已知问题修复。              |
| 2025-06-17  | 2.1.0.0 | 1、调整接入方式，已知问题修复。              |

## 功能描述

| Ad Type          | Material Support      | Display Mode        | Description                                                                 |
| ---------------- | --------------------- | ------------------- | --------------------------------------------------------------------------- |
| **Splash**       | Image                 | Embedded Display    | Display at app launch, requires >3/4 screen coverage                        |
| **Banner**       | Image                 | Embedded Display    | Can be embedded anywhere in the page, supports multiple size specifications |
| **RewardVideo**  | Video                 | Full-Screen Display | Full-screen video playback, users receive rewards after completion          |
| **Interstitial** | Image + Text or Video | Full-Screen Display | Full-screen display, supports image, video combinations                     |
| **Native**       | Image + Text          | Embedded Display    | Fully integrates into app interface, supports template and custom rendering |


## 接入方式

### 添加SDK依赖库
	
    implementation 'tech.brainx.sdk:brainxsdk:$VERSION'
	//例如 implementation 'tech.brainx.sdk:brainxsdk:2.1.0.0'

### 在AndroidManifest.xml中添加SDK依赖的权限申明

	<uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />


### SDK混淆规则
    
	-keep class com.td.** { *; }
	-keepclassmembers class com.td.** {
	    @com.td.* public *;
	}

## 初始化

#### 请先联系BrainX创建应用位，获取App Id，用于进行初始化。

#### 1、创建TDConfig对象

	TDConfig tdConfig = new TDConfig.Builder()
			.setAppId(APP_ID)
			.setCOPPA(true)
			.setGDPR(true)
			.setCCPA(true)
			.build();

|TDConfig.Builder|描述| 是否必须设置 |
|---|---| ---|
|TDConfig.Builder setAppId(int appId)| 设置appid | 是 |
|TDConfig.Builder setCOPPA(boolean coppa)| 设置COPPA, true: 成人, false: 儿童 | 否，但是如果应用需要上架GooglePlay且投放区域包含美国，投放人群包含未成年，则必须设置。默认值为true |
|TDConfig.Builder setGDPR(boolean gdpr)| 设置GDPR, true: 用户同意协议, false: 用户拒绝协议 | 否，但是如果应用需要上架GooglePlay且投放区域包含欧盟，则必须设置。默认值为true |
|TDConfig.Builder setCCPA(boolean ccpa)| 设置CCPA, true: 用户同意收集个人信息, false: 用户拒绝收集个人信息 | 否，但是如果应用需要上架GooglePlay且投放区域包含美国加州，则必须设置。默认值为true |

相关隐私协议了解：[GDPR](https://zh.wikipedia.org/wiki/%E6%AD%90%E7%9B%9F%E4%B8%80%E8%88%AC%E8%B3%87%E6%96%99%E4%BF%9D%E8%AD%B7%E8%A6%8F%E7%AF%84)，[COPPA](https://zh.wikipedia.org/wiki/%E5%84%BF%E7%AB%A5%E5%9C%A8%E7%BA%BF%E9%9A%90%E7%A7%81%E4%BF%9D%E6%8A%A4%E6%B3%95)，[CCPA](https://en.wikipedia.org/wiki/California_Consumer_Privacy_Act)

#### 2、初始化SDK
	
	TDSDK.init(context, tdConfig, new TDSDK.initCallback(){
		@Override
        public void onSDKInitSuccess() {
            // 初始化成功，进行后续广告操作
    	}

        @Override
        public void onSDKInitFail(@NonNull TDError tdError) {
			// 初始化失败
        }
	})


|TDError|描述|
|---|---|
|int getCode()| 获取错误码 |
|String getMsg()| 获取错误信息 |

#### 3、获取初始化状态
	
	TDSDK.isInitialized()

#### 注意：SDK在初始化时会收集用户数据，所以在各隐私协议涉及的相关地区请先获取用户同意之后，通过TDConfig设置协议结果，再进行SDK的初始化以及后续的广告行为。

## Splash
	
####请先联系BrainX创建Splash广告位，获取对应Placement Id，用于广告请求。

#### 1、创建TDSplashConfig对象
	
	TDSplashConfig tdSplashConfig = new TDSplashConfig();
	tdSplashConfig.setAdTimeOut(5);

|TDSplashConfig|描述|
|---|---|
|void setAdTimeOut(int seconds)| 设置广告请求超时时长，单位为秒，最少为3s，默认为3秒 |

#### 2、创建TDSplashAd实例

	TDSplashAd splashAd = new TDSplashAd("SPLASH_PLACEMENT_ID", tdSplashConfig);

#### 3、设置事件监听

	splashAd.setListener(new TDSplashAdListener() {
		@Override
		public void onAdLoaded(@NonNull TDSplash tdSplash) {
			// 请求成功，可进行展示
			splashAd.show(container)
		}

		@Override
		public void onError(@NonNull TDError tdError) {
			// 请求失败
		}

		@Override
		public void onAdClicked() {
			// 广告点击
		}
		
		@Override
		public void onAdDismissed() {
			// 广告关闭
		}
		
		@Override
		public void onAdShowed() {
			// 广告展示
		}

		@Override
		public void onAdShowedFail(@NonNull TDError error) {
			// 广告展示失败
		}
	});

|TDSplashAdListener|描述|
|---|---|
|void onAdLoaded(TDSplash tdSplash)| 广告请求成功，返回TDSplash实例 |
|void onError(TDError tdError)| 广告请求失败，返回TDError实例 |
|void onAdClicked()| 广告点击回调，用户点击广告热区时触发 |
|void onAdDismissed()| 广告关闭回调，用户点击跳过按钮，或者倒计时完成时触发 |
|void onAdShowed()| 广告展示回调，广告有效展示时触发 |
|void onAdShowedFail(TDError error)| 广告展示失败回调 |

|TDSplash|描述|
|---|---|
|double getBidPrice()| 获取广告价格 |

|TDSplashAd|描述|
|void show(ViewGroup container)| 展示Splash |

#### 4、请求并展示广告

	splashAd.load();

调用load()方法请求广告，在onAdLoaded回调中将广告展示到 container 中。

**注意**：Splash需要占屏幕大小的 **3/4** 以上，并且其本身不能被遮挡超过 **1/3**，否则将 **无法** 正常计算展示。   


## Banner

####请先联系BrainX创建Banner广告位，获取对应placement Id，用于广告请求。

####创建广告位时请说明需要的广告尺寸，是否需要自动刷新，以及自动刷新间隔。

####目前支持的Banner尺寸为320\*50，300\*250，320\*90，728\*90，800\*600

#### 1、创建TDBannerConfig对象

	TDBannerConfig tdBannerConfig = new TDBannerConfig();
	tdBannerConfig.setAdTimeOut(5);

|TDBannerConfig|描述|
|---|---|
|void setAdTimeOut(int seconds)| 设置广告请求超时时长，单位为秒，最少为3s，默认为3秒 |

#### 2、创建TDBannerAdView实例

	TDBannerAdView bannerAdView = new TDBannerAdView(context, "BANNER_PLACEMENT_ID", tdBannerConfig);

#### 3、设置事件监听

	bannerAdView.setListener(new TDBannerAdListener() {
		@Override
		public void onAdLoaded(@NonNull TDBanner tdBanner) {
			// 请求成功
		}

		@Override
		public void onError(@NonNull TDError tdError) {
			// 请求失败
		}

		@Override
		public void onAdClicked() {
			// 广告点击
		}

		@Override
		public void onAdDismissed() {
			// 广告关闭
		}

		@Override
		public void onAdShowed() {
			// 广告展示
		}
	});

|TDBannerAdListener|描述|
|---|---|
|void onAdLoaded(TDBanner tdBanner)| 广告请求成功，返回TDBanner实例 |
|void onError(TDError tdError)| 广告请求失败，返回TDError实例 |
|void onAdClicked()| 广告点击回调，用户点击热区时触发 |
|void onAdDismissed()| 广告关闭回调，用户点击关闭按钮时触发 |
|void onAdShowed()| 广告展示回调，广告有效展示时触发 |

|TDBanner| 描述           |
|---|--------------|
|double getBidPrice() | 获取广告价格       |
|double getAdWidth() | 获取广告宽度       |
|double getAdHeight() | 获取广告高度       |

#### 4、请求并展示广告

	bannerAdView.load();
	container.addView(bannerAdView);

创建TDBannerAdView实例后，调用load()方法请求广告，同时将bannerAdView添加到目标container当中。   
**注意**：Banner不能被遮挡超过 **1/3** ，否则将 **无法** 正常计算展示。

#### 5、销毁广告

	bannerAdView.destroy();

在页面销毁或者banner不再需要展示时调用以回收资源。

## RewardVideo

####请先联系BrainX创建RewardVideo广告位，获取对应placement Id，用于广告请求。

####创建广告位时请说明是否需要S2S下发奖励，如需要则需要提供奖励名称，奖励数量以及Callback Url

#### 1、创建TDRewardVideoConfig对象
	
	// 如果需要S2S下发奖励，则需要在创建Config时传入USER_ID
	TDRewardVideoConfig tDRewardVideoConfig = new TDRewardVideoConfig("USER_ID");
	tDRewardVideoConfig.setAdTimeOut(8);

|TDRewardVideoConfig|描述|
|---|---|
|void setAdTimeOut(int seconds)| 设置广告请求超时时长，单位为秒，最少为3s，默认为8秒 |

#### 2、创建TDRewardVideoAd实例

	TDRewardVideoAd rewardVideoAd = new TDRewardVideoAd("REWARDVIDEO_PLACEMENT_ID", tDRewardVideoConfig);

#### 3、设置事件监听

	rewardVideoAd.setListener(new TDRewardVideoAdListener() {
		@Override
		public void onAdLoaded(@NonNull TDRewardVideo tdRewardVideo) {
			// 请求成功
		}

		@Override
		public void onError(@NonNull TDError tdError) {
			// 请求失败
		}

		@Override
        public void onAdShowedFail(@NonNull TDError error) {
			//广告展示失败
        }

		@Override
        public void onRewardedSuccess(@NonNull TDRewardItem rewardItem) {
			//广告激励成功
        }

        @Override
        public void onRewardedFail() {
			//广告激励失败
        }

		@Override
		public void onAdClicked() {
			// 广告点击
		}

		@Override
		public void onAdDismissed() {
			// 广告关闭
		}

		@Override
		public void onAdShowed() {
			// 广告展示
		}
	});

|TDRewardItem|描述|
|---|---|
|String getItemName()| 奖励名称 |
|String getItemNumber()| 奖励数量 |

|TDRewardVideoAdListener|描述|
|---|---|
|void onAdLoaded(TDRewardVideo tdRewardVideo)| 广告请求成功，返回TDRewardVideo实例 |
|void onError(TDError tdError)| 广告请求失败，返回TDError实例 |
|void onAdShowedFail(TDError error)| 广告展示失败回调 |
|void onRewardedSuccess(TDRewardItem rewardItem)| 广告激励成功回调 |
|void onRewardedFail()| 广告激励失败回调 |
|void onAdClicked()| 广告点击回调，用户点击热区时触发 |
|void onAdDismissed()| 广告关闭回调，用户点击关闭按钮时触发 |
|void onAdShowed()| 广告展示回调，广告有效展示时触发 |

|TDRewardVideo|描述|
|---|---|
|double getBidPrice() | 获取广告价格 |

|TDRewardVideoAd|描述|
|---|---|
|boolean isReady()| 判断RewardVideo是否可播放 |
|void show()| 展示RewardVideo |

**注意**：RewardVideo涉及视频素材加载，耗时较长，为了保证展示效果，建议提前对广告对象进行加载！！

#### 4、请求广告

	rewardVideoAd.load();

#### 5、展示广告

	if (rewardVideoAd.isReady()) {
		rewardVideoAd.show();
	}

## Interstitial

####请先联系BrainX创建Interstitial广告位，获取对应placement Id，用于广告请求。

####创建广告位时请说明需要的插屏类型：图文; 视频; 图文视频混合

#### 1、创建TDInterstitialConfig对象
	
	TDInterstitialConfig tDInterstitialConfig = new TDInterstitialConfig();
	tDInterstitialConfig.setAdTimeOut(8);

|TDInterstitialConfig|描述|
|---|---|
|void setAdTimeOut(int seconds)| 设置广告请求超时时长，单位为秒，最少为3s，默认为8秒 |

#### 2、创建TDInterstitialAd实例

	TDInterstitialAd interstitialAd = new TDInterstitialAd("INTER_PLACEMENT_ID", tDInterstitialConfig);

#### 3、设置事件监听

	interstitialAd.setListener(new TDInterstitialAdListener() {
		@Override
		public void onAdLoaded(@NonNull TDInterstitial tDInterstitial) {
			// 请求成功
		}

		@Override
		public void onError(@NonNull TDError tdError) {
			// 请求失败
		}

		@Override
        public void onAdShowedFail(@NonNull TDError error) {
			//广告展示失败
        }

		@Override
		public void onAdClicked() {
			// 广告点击
		}

		@Override
		public void onAdDismissed() {
			// 广告关闭
		}

		@Override
		public void onAdShowed() {
			// 广告展示
		}
	});

|TDInterstitialAdListener|描述|
|---|---|
|void onAdLoaded(TDInterstitial tDInterstitial)| 广告请求成功，返回TDInterstitial实例 |
|void onError(TDError tdError)| 广告请求失败，返回TDError实例 |
|void onAdShowedFail(TDError error)| 广告展示失败回调 |
|void onAdClicked()| 广告点击回调，用户点击热区时触发 |
|void onAdDismissed()| 广告关闭回调，用户点击关闭按钮时触发 |
|void onAdShowed()| 广告展示回调，广告有效展示时触发 |

|TDInterstitial|描述|
|---|---|
|double getBidPrice() | 获取广告价格 |

|TDInterstitialAd|描述|
|---|---|
|boolean isReady()| 判断Interstitial是否可播放 |
|void show()| 展示Interstitial |

**注意**：Interstitial涉及视频素材加载，耗时较长，为了保证展示效果，建议提前对广告对象进行加载！！

#### 4、请求广告

	interstitialAd.load();

#### 5、展示广告

	if (interstitialAd.isReady()) {
		interstitialAd.show();
	}

## Native

####请先联系BrainX创建Native广告位，获取对应placement Id，用于广告请求。


#### 1、创建TDNativeConfig对象
	
	TDNativeConfig tdNativeConfig = new TDNativeConfig(TDNativeConfig.NativeType.TEMPLATE_RENDERING);
	tdNativeConfig.setAdTimeOut(8);

|TDNativeConfig.NativeType|描述|
|---|---|
|TEMPLATE_RENDERING| 模板渲染 |
|SELF_RENDERING| 自渲染 |

|TDNativeConfig|描述|
|---|---|
|void setAdTimeOut(int seconds)| 设置广告请求超时时长，单位为秒，最少为3s |

#### 2、创建TDNativeAd实例

	TDNativeAd nativeAd = new TDNativeAd("NATIVE_PLACEMENT_ID", tdNativeConfig);

#### 3、设置事件监听

	nativeAd.setListener(new TDNativeAdListener() {
		@Override
		public void onAdLoaded(@NonNull TDNative tdNative) {
			// 请求成功，模板渲染需要调用renderForTemplate
			if (tdNativeConfig.getNativeType() == TDNativeConfig.NativeType.TEMPLATE_RENDERING) {
				nativeAd.renderForTemplate(context);
			}
		}

		@Override
		public void onError(@NonNull TDError tdError) {
			// 请求失败
		}

		@Override
		public void onAdClicked() {
			// 广告点击
		}

		@Override
		public void onAdDismissed() {
			// 广告关闭
		}

		@Override
		public void onAdShowed() {
			// 广告展示
		}

		@Override
		public void onRenderFail(TDError error) {
			// 模板Native渲染失败
		}

		@Override
		public void onRenderSuccess(TDNativeView nativeView) {
			// 模板Native渲染成功
			container.addView(nativeView);
		}
	});

|TDNativeAdListener|描述|
|---|---|
|void onAdLoaded(TDNative tdNative)| 广告请求成功，返回TDNative实例 |
|void onError(TDError tdError)| 广告请求失败，返回TDError实例 |
|void onAdClicked()| 广告点击回调，用户点击热区时触发 |
|void onAdDismissed()| 广告关闭回调，用户点击关闭按钮时触发 |
|void onAdShowed()| 广告展示回调，广告有效展示时触发 |
|void onRenderFail(TDError error)| 模板Native渲染失败 |
|void onRenderSuccess(TDNativeView view)| 模板Native渲染成功 |

|TDNative|描述|
|---|---|
|string getIcon() | 获取Icon Url |
|string getTitle() | 获取Title |
|string getDescription() | 获取Description |
|double getRating() | 获取Rating |
|string getCTAText(): String | 获取CTA按钮文字 |
|View getAdLogoView(context: Context) | 获取BrainX Logo View |
|View getMediaView(context: Context) | 获取Media View |
|double getBidPrice() | 获取广告价格 |

|TDNativeAd|描述|
|---|---|
|boolean bindViewsForInteraction(ViewGroup container, List<View> creativeViews, View dislikeView) | 自渲染Native绑定交互事件，需要在主线程调用, 返回true表示绑定成功，返回false表示绑定失败 |
|void renderForTemplate(Context context) | 渲染模板Native |
|void destroy() | 销毁广告，回收广告资源 |

#### 4、请求广告

	nativeAd.load();

#### 5、渲染广告
~~~
//模板渲染
// 在onAdLoaded回调中调用
nativeAd.renderForTemplate(context);
~~~

	//自渲染
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

**注意**： TDNativeConfig传入的NativeType代表了Native的类型：  

- 当传入TEMPLATE_RENDERING时，代表此Native为模板渲染，在onAdLoaded回调中调用renderForTemplate(Context context)以渲染模板Native。
- 当传入SELF_RENDERING时，需要自己编写native view，并通过TDNative对象获取素材并自行渲染。并调用bindViewsForInteraction(ViewGroup container, List<View> creativeViews, View dislikeView)接口来绑定交互事件以及监听。container表示native view的根容器，creativeViews代表所有素材View，dislikeView代表关闭按钮。 **所有creativeView以及dislikeView都必须位于container内，MediaView以及LogoView必须被添加到container中，并且被放入creativeViews的List中！！**

#### 6、展示广告

	container.addView(nativeView)

将广告View添加到目标container当中去展示。   
**注意：**Native不能被遮挡超过 **1/3** ，否则将 **无法** 正常计算展示。

#### 7、销毁广告

	nativeAd.destroy()

在页面销毁或者native不再需要展示时调用以回收资源。

## 隐私
BrainX会收集设备信息、GAID并上报这些数据，用于确定用户ID。如果应用需要上架到 GooglePlay，您需要在 GooglePlay 开发者控制台上和隐私政策协议中声明使用条款。   
[Brainx End User Data Privacy Policy](https://brainx.tech/privacy/users)  
[Brainx Publisher Agreement](https://brainx.tech/publisher)   
[Brainx Partner Data Privacy Policy](https://brainx.tech/partner)    

## 聚合平台支持

|平台名称|支持广告| Network Adapter 版本 | 依赖                                                          |
|---|---|--------------------|-------------------------------------------------------------|
|TradPlus|Splash、Banner、RewardVideo、Inter| 1100               | implementation 'tech.brainx.sdk:network-tradplus:1.1.0.0'   |
|Topon|Splash、Banner、RewardVideo、Inter| 1100               | implementation 'tech.brainx.sdk:network-topon:1.1.0.0'      |
|IronSource|Banner、RewardVideo、Inter| 1100               | implementation 'tech.brainx.sdk:network-ironsource:1.1.0.0' |
|Max|Banner、RewardVideo、Inter| 1100               | implementation 'tech.brainx.sdk:network-max:1.1.0.0'        |
|Admob|Banner、RewardVideo、Inter| 1100               | implementation 'tech.brainx.sdk:network-admob:1.1.0.0'      |