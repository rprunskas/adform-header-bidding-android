##  Adform Header Bidding Android SDK

# Getting Started

## 1. General Info

* Adform Header Bidding SDK runs on Android 4.0 (API 14), so created project version should be 4.0 (API 14) and above.

## 2. Setting up library dependencies

* To add a library to the dependencies, first we need to specify repository location. This can be done by editing `build.gradle` file and by inserting snippet (specified below) right above the `android` configuration group. 

	    ...
		repositories {
    		maven { url "https://github.com/adform/adform-header-bidding-android/raw/master/releases/" }
		}
        ...
        
![](http://i.imgur.com/xExE30V.png)

* Then in the dependency group we need to specify that we will be using `AdformHeaderBiddingSdk`, and also add `Google Play Ads` service.


	    ...
        dependencies {
    		compile 'com.adform:adform-header-bidding-sdk:1.0.0'
    		compile 'com.google.android.gms:play-services-ads:8.4.0'
        }
        ...
        
![](http://i.imgur.com/7ESWcn7.png)
        
## 3. Update AndroidManifest.xml

* Add the following permissions:

  ```xml
		<uses-permission android:name="android.permission.INTERNET" />
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /> 
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
		<!--This is needed to pull out device imei-->
		<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
    	<!--This is needed to pull out device mac address-->
    	<uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    	<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
    	<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>	
  ```

* Also additional android services are needed to work properly. To add them, update `AndroidManifest.xml` with snipped shown below between `<application></application>` tags.

  ```xml
		android:name="com.google.android.gms.version"
	    android:value="@integer/google_play_services_version"/>
  ```
    
![](http://i.imgur.com/V8sGME4.png)
    
Now you are set to use sdk.

# Basic integration


1. Create ad request in this way:

	```java
    	BidRequest bidRequest = new BidRequest.Builder()
            .setPlacementType(AdformEnum.PlacementType.INLINE)
            .setMasterTag(142493)
            .setAdSize(new AdSize(320, 50))
            .build();
  	```  
  	
	It is required to set master tag, other parameters are optional. 
                
2. You need to initialize ad loader using `Context` and `BidRequest` as parameters:

	```java
		BidLoader bidLoader = new BidLoader(this, bidRequest);   
	```
			
3. Set loading listener in order to receive events about ad loading:

	```java
		bidLoader.setListener(new AdLoadListener() {
	            @Override
	            public void onSuccess(BidResponse bidResponse) {
	                //do something with result
	            }
	
	            @Override
	            public void onFail(String errorMessage) {
	            	//do something with error
	            }
        });
	```
	You would need to implement handling of response for yourself. 
			        
4. Finally, you could start loading:

	```java
		bidLoader.requestBids();
	```
    		
# DFP integration	

In order to load Adform banners with Google DFP SDK using Adform Header Bidding SDK you need to follow these steps:

   * Configure a creative for header bidding on DFP interface.
   * Import Adform Header Bidding SDK and Google DFP SDK to your project. For more information on Adform Header Bidding SDK integration take a look at the top of the document and more information about Google DFP SDK integration can be found [here](https://developers.google.com/mobile-ads-sdk/docs/dfp/android/quick-start).
 * Make a bid request using Adform Header Bidding SDK. 

```java
    BidRequest bidRequest = new BidRequest.Builder()
        .setPlacementType(AdformEnum.PlacementType.INLINE)
        .setMasterTag(masterTag)
        .setAdSize(new AdSize(width, height))
        .build();

    mBidLoader = new BidLoader(this, bidRequest);
    mBidLoader.setListener(new BidLoadListener() {
        @Override
        public void onSuccess(BidResponse bidResponse) {
            Log.d(TAG, "onSuccess" + bidResponse.toString());
            showDfpBanner(bidResponse);
        }

        @Override
        public void onFail(String errorMessage) {
            Log.d(TAG, "onFail" + errorMessage);
            showDfpBanner(null);
        }

    });
    mBidLoader.requestBids();
```
* Pass bid price, ad unit and bidder name to DFP ad request.


```java
	PublisherAdRequest adRequest;
	PublisherAdRequest.Builder builder = new PublisherAdRequest.Builder();
	
	if(bidResponse != null){
	    DecimalFormat decim = new DecimalFormat("0.00");
	    String price = decim.format(bidResponse.getCpm());
	
	    adRequest = builder
	        .addCustomTargeting("hb_bidder", "adform")
	        .addCustomTargeting("hb_adid", bidResponse.getAdUnitScriptEncoded().replaceAll("=", "-"))
	        .addCustomTargeting("hb_pb", price).build();
	} else {
	    adRequest = builder.build();
	}
```
* Load the ad.

```java
	mPublisherAdView.loadAd(adRequest);
```
