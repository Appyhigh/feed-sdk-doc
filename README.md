# NewsFeedSDK

A news feed sdk to seamlessly integrate news in your application

# Table of contents

- [Initialization](#initialization)
- [Custom Views](#custom-views)
- [Features](#features)
- [Handle Notifications & Dynamic Links](#handle-notifications--dynamic-links)
- [Handle Remote Config](#handle-remote-config)
- [Notification Payload Examples](#notification-payload-examples)
- [Common Errors Examples](#common-errors-examples)

## Initialization
[(Back to top)](#table-of-contents)

1)  Add the auth token in `$HOME/.gradle/gradle.properties` file
    ```grrovy
    authToken=**JITPACK_AUTH_TOKEN**
    ```
2)  Then use authToken as the username in your project `build.gradle` file.
    ```grrovy
    
	allprojects {
	    repositories {
		...
		maven {
		    url "https://www.jitpack.io"
		    credentials { username authToken }
		}
	    }
	 }
    ```
3)  Add the library to your app `build.gradle` file.

    In your  `build.gradle`:

    ```groovy

	dependencies {
	    implementation 'com.github.Appyhigh:news-feed-sdk:1.0.11'
	    // ...
	}
    ```
4)  Add the following metadata inside your `application` tag in your `AndroidManifest.xml`

    ```xml
    <application>
        <meta-data
            android:name="app_name"
            android:value="**parent app name**" />
        <meta-data
            android:name="news_feed_app_id"
            android:value="**feedsdk app_id for app**" />
        <meta-data
            android:name="feed_target_activity"
            android:value="**activity that has feeds integrated**"/>
        <meta-data
            android:name="feed_app_icon"
            android:resource="**icon of parent app**"/>
        <meta-data
            android:name="is_location_already_asked"
            android:value="**if(location is already being asked in parent app) true else false" />
    </application>
    ```
    #### Example
    ```xml
        <meta-data
            android:name="app_name"
            android:value="@string/app_name" />
        <meta-data
            android:name="news_feed_app_id"
            android:value="@string/news_feed_app_id" />
        <meta-data
            android:name="feed_target_activity"
            android:value="com.appyhigh.exampleapp.activity.HomeActivity"/>
        <meta-data
            android:name="feed_app_icon"
            android:resource="@drawable/ic_app_logo"/>
        <meta-data
            android:name="is_location_already_asked"
            android:value="false" />
    ```
5)  Inititalize SDK(Recommended to initialize in Splash and Main Activity)

    initializeSdk(context: Context, lifecycle: Lifecycle, versionCode:String, versionName:String user: User? = null, showCricketNotification:Boolean?=true, isDark:Boolean = false)

    ***Kotlin***
    ```kotlin
    private fun initFeedSDK() {
         FeedSdk().initializeSdk(this, lifecycle, BuildConfig.VERSION_CODE.toString(), BuildConfig.VERSION_NAME, user, showCricketNotification, isDark) //user can be null also
         FeedSdk().setFirebaseDynamicLink(**dynamic link domain of parent app**)
         FeedSdk().setShareBody(** shareBody text of the dynamic links of posts that are shared from feedsdk **)
         SpUtil.getGEOPoints(this)  // this is required for feedSDK to set the language by location
    }
    ```
    ***Java***
    ```java
    void initFeedSDK(){
         new FeedSdk().initializeSdk(this, getLifecycle(), BuildConfig.VERSION_CODE.toString(), BuildConfig.VERSION_NAME, user, showCricketNotification, isDark); //user can be null also
         FeedSdk.Companion.setMFirebaseDynamicLink(**dynamic link domain of parent app**)
         FeedSdk.Companion.setShareBody(** shareBody text of the dynamic links of posts that are shared from feedsdk **)
         SpUtil.Companion.getGEOPoints(this)  // this is required for feedSDK to set the language by location
     }
    ```

6) If you have enabled progaurd in your app then add the following lines in the prograurd rules
```grrovy
    
	# FeedSDK
	-keepclassmembers enum * { *; }
	-keepclassmembers class com.appyhigh.newsfeedsdk.apicalls.**{
	    <fields>;
	    <init>();
	    <methods>;
	}
	-keepclassmembers class com.appyhigh.newsfeedsdk.model.** {
	    <fields>;
	    <init>();
	    <methods>;
	}
   ```
7)  [**Handle Notifications & Dynamic Links**](#handle-notifications--dynamic-links)
8)  <a href="https://firebase.google.com/docs/perf-mon/get-started-android" ><b>Add firebase performance sdk in your parent app</b></a> (Optional)

## Custom Views
[(Back to top)](#table-of-contents)
- [News Feed](#news-feed)
- [Reels](#reels)
- [Explore](#explore)
- [Cricket Feed](#cricket-feed)

### News Feed
[(Back to top)](#custom-views)

<img src="https://github.com/tejaSomanchi/feedDoc/blob/main/20210928_182405.gif" width="300" />

1)  Add the following lines inside your xml to show the News Feed view
    ```xml
    <com.appyhigh.newsfeedsdk.customview.NewsFeedList
       android:id="@+id/newsFeed"
       android:layout_width="match_parent"
       android:layout_height="match_parent"
       />
    ```
2)  Create object of custom view and call the following functions according to your requirements.
    ```kotlin
    val newsFeedList = view.findViewById(R.id.newsFeed)     //id of NewsFeedList defined in xml
    newsFeedList.stopVideoPlayback()    // to stop playing videos in this view
    newsFeedList.startVideoPlayback()   // to start playing videos in this view
    newsFeedList.refreshFeeds()     // to refresh feeds in this view
    ``` 

### Reels
[(Back to top)](#custom-views)

<img src="https://github.com/tejaSomanchi/feedDoc/blob/main/20210928_183834.gif" width="300" />

1)  Add the following lines inside your xml to show the Reels view
    ```xml
    <com.appyhigh.newsfeedsdk.customview.VideoFeed
        android:id="@+id/video_feed"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
    ```
2)  Create object of custom view and call the following functions according to your requirements.
    ```kotlin
    val videoFeed = view.findViewById(R.id.video_feed)   //id of VideoFeed defined in xml
    videoFeed.onFocusChanged()  // to stop playing videos in this view
    videoFeed.onResume()    // to start playing videos in this view
    ``` 

### Explore
[(Back to top)](#custom-views)

<img src="https://github.com/tejaSomanchi/feedDoc/blob/main/20210928_184155.gif" width="300" />

1)  Add the following lines inside your xml to show the Explore view
    ```xml
    <com.appyhigh.newsfeedsdk.customview.ExploreFeed
        android:id="@+id/explore_feed"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
    ```

### Cricket Feed
[(Back to top)](#custom-views)

<img src="https://github.com/tejaSomanchi/feedDoc/blob/main/20210928_184633.gif" width="300" />

1)  Add the following lines inside your xml to show the Cricket Feed view
    ```xml
    <com.appyhigh.newsfeedsdk.customview.CricketView
       android:id="@+id/cricketFeed"
       android:layout_width="match_parent"
       android:layout_height="match_parent"/>
    ```
2)  Create object of custom view and call the following functions according to your requirements.
    ```kotlin
    val cricketFeed = view.findViewById(R.id.cricketFeed)     //id of CricketView defined in xml
    cricketFeed.stopVideoPlayback()    // to stop playing videos in this view
    cricketFeed.refreshFeeds()     // to refresh feeds in this view
    ``` 

## Features
[(Back to top)](#table-of-contents)

- [Ads](#ads)
- [Search Sticky Bar](#search-sticky-bar)
- [Bottom Navigation Nudge](#bottom-navigation-nudge)
- [Functions](#functions)

### Ads
[(Back to top)](#features)

1) Add your ad units after initializing the sdk to show the ads.

   ***Kotlin***
    ```kotlin
    private fun initFeedSDK() {
         FeedSdk().initializeSdk(this, lifecycle, user, showCricketNotification) //user can be null also
         // ...
         // all the ad ids should be native ad units except search_footer_banner_intermediate, native_footer_banner, ad_post_interstitial
         val adsModel = AdsModel(
            getString(R.string.ad_unit_feed_native),
            getString(R.string.ad_unit_video_ad_native),
            getString(R.string.search_page_native),
            getString(R.string.search_footer_banner_intermediate),
            getString(R.string.ad_unit_between_article),
            getString(R.string.ad_id_between_article_native_fallback),
            getString(R.string.ad_unit_article_end),
            getString(R.string.ad_id_article_end_native_fallback),
            getString(R.string.ad_post_interstitial),
            getString(R.string.native_footer_banner)
        )
        FeedSdk().setAdsModel(adsModel)
        // ...
    }
    ```
   ***Java***
    ```java
    void initFeedSDK(){
         new FeedSdk().initializeSdk(this, getLifecycle(), user, showCricketNotification); //user can be null also
         // all the ad ids should be native ad units except search_footer_banner_intermediate, native_footer_banner, ad_post_interstitial
         // ...
          AdsModel adsModel = new AdsModel(
            getString(R.string.ad_unit_feed_native),
            getString(R.string.ad_unit_video_ad_native),
            getString(R.string.search_page_native),
            getString(R.string.search_footer_banner_intermediate),
            getString(R.string.ad_unit_between_article),
            getString(R.string.ad_id_between_article_native_fallback),
            getString(R.string.ad_unit_article_end),
            getString(R.string.ad_id_article_end_native_fallback),
            getString(R.string.ad_post_interstitial),
            getString(R.string.native_footer_banner)
        );        
        FeedSdk.Companion.setMAdsModel(adsModel);
        // ...
     }
    ```
2) Add the following line after initializing the sdk to hide the ads in the sdk.

   ***Kotlin***
   ```kotlin
   private fun initFeedSDK() {
        FeedSdk().initializeSdk(this, lifecycle, user, showCricketNotification) //user can be null also
        // ...
        FeedSdk().setShowAds(false)
       // ...
   }
   ```
   ***Java***
   ```java
   void initFeedSDK(){
        new FeedSdk().initializeSdk(this, getLifecycle(), user, showCricketNotification); //user can be null also  
        // ...
       FeedSdk.Companion.setShowAds(false);
       // ...
    }
   ```
   **Note : By Default, ads are shown in Feedsdk**

### Search Sticky Bar
[(Back to top)](#features)

<img src="https://github.com/tejaSomanchi/feedDoc/blob/main/search_sticky_bar.jpg" width="600" />

1)  Add the following line after initializing the sdk to show the search sticky bar in the notification tray.

    ***Kotlin***
    ```kotlin
    private fun initFeedSDK() {
         FeedSdk().initializeSdk(this, lifecycle, user, showCricketNotification) //user can be null also
         // ...
         FeedSdk().setSearchStickyNotification(defaultBackground:String, intent: Intent)
        // ...
    }
    ```
    ***Java***
    ```java
    void initFeedSDK(){
         new FeedSdk().initializeSdk(this, getLifecycle(), user, showCricketNotification); //user can be null also  
         // ...
        new FeedSdk().setSearchStickyNotification(defaultBackground, getIntent());
        // ...
     }
    ```
2) Add the following if condition before initializing the sdk.

   ***Kotlin***
   ```kotlin
       if(intent.hasExtra("fromSticky") && intent.getStringExtra("fromSticky")=="reels"){
            com.appyhigh.newsfeedsdk.Constants.isVideoFromSticky = true
        }
        // ...
       FeedSdk().initializeSdk(this, lifecycle, user, showCricketNotification) //user can be null also
        // ...
       
   ```
   ***Java***
   ```java
       if(getIntent().hasExtra("fromSticky") && getIntent().getStringExtra("fromSticky").equals("reels")){
           com.appyhigh.newsfeedsdk.Constants.INSTANCE.setVideoFromSticky(true);
       }
       // ...
       new FeedSdk().initializeSdk(this, getLifecycle(), user, showCricketNotification); //user can be null also  
       // ...
   ``` 

3) Add the following lines after initializing the sdk where you handle your parent app notification intent.

***Kotlin***
   ```kotlin
       if(intent.hasExtra("fromSticky")){
            Handler(Looper.getMainLooper()).postDelayed({
                if(intent.getStringExtra("fromSticky")=="reels") {
                   //open tab or activity that handles reels view of feedSDK
		   //ignore if not using reels view in parent app
		} else{
		   //open tab or activity that handles feeds view of feedSDK
		   //ignore if not using feeds view in parent app
		}
            },1000)
        }
       // ...
   ```
***Java***
   ```java
       if(intent.hasExtra("fromSticky")){
		new Handler(Looper.getMainLooper()).postDelayed(new Runnable() {
		    @Override
		    public void run() {
			if(intent.getStringExtra("fromSticky").equals("reels")) {
			   //open tab or activity that handles reels view of feedSDK
                  	   //ignore if not using reels view in parent app
			} else{
			   //open tab or activity that handles feeds view of feedSDK
                  	   //ignore if not using feeds view in parent app
			}
		    }
		}, 1000);
    	}
       // ...
   ```
4) Start `SettingsActivity` to open the settings for this notification in parent app.
   ***Kotlin***
   ```kotlin
    startActivity(Intent(this, SettingsActivity::class.java))
   ```
   ***Java***
   ```java
   startActivity(new Intent(this, SettingsActivity.class));
   ```
   
### Bottom Navigation Nudge
[(Back to top)](#features)

1) Pass the bottom navigation instance, the position of icon where you want to show nudge, number of notification (optional), Also save the result for removing the nudge
    
    ***Kotlin***
    ```kotlin
    val object = FeedSdk().setNudge(bottomNavigation,1,5)
   ```
   
    ***Java***
    ```java
    BottomNavigationItemView object = new FeedSdk().setNudge(bottomNavigation,1,5);
   ```
    
2) Whenever you want to remove the nudge, pass the object saved in the above step as follows:
    
    ***Kotlin***
    ```kotlin
    if(object != null)
        FeedSdk().removeNudge(object)
    ```
   
    ***Java***
    ```java
    if(object != null)
        new FeedSdk().removeNudge(object);
    ```

### Functions
[(Back to top)](#features)

#### Pluto SDK
If your parent app has pluto sdk then add the following lines before intialiazing the sdk to get the newtork logs of feedsdk in pluto.

   ***Kotlin***
   ```kotlin
   FeedSdk.hasPluto = true
   ```
   ***Java***
   ```java
   FeedSdk.Companion.setHasPluto(true);
   ```
#### Add languages for feedsdk
To add languages directly from parent app, use `setLanguagesForFeedSDK(languages:String)`

 `languages` param should be a string of language codes with "," seperated.
 
***Kotlin***
   ```kotlin
   FeedSdk().setLanguagesForFeedSDK("en,hi")
   ```
   ***Java***
   ```java
   new FeedSdk().setLanguagesForFeedSDK("en,hi");
   ```
#### Add language for `getfeeds` api call from parent app.
To add language directly for api call from parent app, use `setLanguageForAPICalls(languages:String)`

 `languages` param should be a string oflanguage codes with "," seperated.
 
***Kotlin***
   ```kotlin
   FeedSdk().setLanguageForAPICalls("en,hi")
   ```
   ***Java***
   ```java
   new FeedSdk().setLanguageForAPICalls("en,hi");
   ```
#### Add interests for `getfeeds` api call from parent app.
To add interests directly for api call from parent app, use `setInterestsForAPICalls(interests:String)`

 `interests` param should be a string of interest id with "," seperated.
 
***Kotlin***
   ```kotlin
   FeedSdk().setInterestsForAPICalls("cricket,sports")
   ```
   ***Java***
   ```java
   new FeedSdk().setInterestsForAPICalls("cricket,sports");
   ```
   
## Handle Notifications & Dynamic Links

[(Back to top)](#table-of-contents)

### Handle Notifications


1) Add the following if condition before initializing the sdk.

   ***Kotlin***
   ```kotlin
       if(FeedSdk.isScreenNotification(intent)){
           SpUtil.pushIntent = intent
       } else{
           SpUtil.pushIntent = null
       }
        // ...
       FeedSdk().initializeSdk(this, lifecycle, user, showCricketNotification) //user can be null also
        // ...
       
   ```
   ***Java***
   ```java
       if(FeedSdk.Companion.isScreenNotification(getIntent())){
           SpUtil.Companion.setPushIntent(getIntent());
       } else{
            SpUtil.Companion.setPushIntent(null);
        }
       // ...
       new FeedSdk().initializeSdk(this, getLifecycle(), user, showCricketNotification); //user can be null also  
       // ...
   ```
2) Add the following lines after initializing the sdk where you handle your parent app notification intent.

   ***Kotlin***
   ```kotlin
       if(FeedSdk.isScreenNotification(intent) || FeedSdk.fromLiveMatch(intent)){
           Handler(Looper.getMainLooper()).postDelayed({
               if(FeedSdk.checkFeedSdkTab("explore", intent)){
                  //open tab or activity that handles explore view of feedSDK
                  //ignore if not using explore view in parent app
               } else if(FeedSdk.checkFeedSdkTab("reels", intent)){
                   //open tab or activity that handles reels view of feedSDK
                   //ignore if not using reels view in parent app
               } else {
                  //open tab or activity that handles feeds view of feedSDK
                  //ignore if not using feeds view in parent app
               }
           },1000)
       } else if (intent.extras != null && !FeedSdk.handleIntent(this, intent)) {
            //Handle your parent app notifications
       }
       // ...
   ```
   ***Java***
   ```java
       if(FeedSdk.Companion.isScreenNotification(getIntent()) || FeedSdk.Companion.fromLiveMatch(getIntent())){
           new Handler(Looper.getMainLooper()).postDelayed(new Runnable() {
               @Override
               public void run() {
                   if(FeedSdk.Companion.checkFeedSdkTab("explore", getIntent())){
                       //open tab or activity that handles explore view of feedSDK
                       //ignore if not using explore view in parent app
                   } else if(FeedSdk.Companion.checkFeedSdkTab("reels", getIntent())){
                       //open tab or activity that handles reels view of feedSDK
                       //ignore if not using reels view in parent app
                   } else {
                      //open tab or activity that handles feeds view of feedSDK
                      //ignore if not using feeds view in parent app
                   }
               }
           }, 1000);
       } else if (getIntent().getExtras() != null && !FeedSdk.Companion.handleIntent(this, getIntent())) {
            //Handle your parent app notifications
       }
       // ...
   ```


### Handle Dynamic Links

[(Back to top)](#handle-notifications--dynamic-links)

1) Fetch and check the params("feed_id","is_native","podcast_id","filename","matchType","pwa","matchesMode") from firebase dynamic link.

   ***Kotlin***
   ```kotlin
      Firebase.dynamicLinks
           .getDynamicLink(intent)
           .addOnSuccessListener(this) { pendingDynamicLinkData ->
               // Get deep link from result (may be null if no link is found)
               var deepLink: Uri? = null
              try {
                  if (pendingDynamicLinkData != null) {
                      deepLink = pendingDynamicLinkData.link
                      try {
		      		  if (deepLink!!.getQueryParameter("feed_id") != null) {
					if(deepLink!!.getQueryParameter("is_native")!=null){
					   isNative = true
					}
					post_id= pendingDynamicLinkData.link?.getQueryParameter("feed_id")!!
				  }
				  if (deepLink!!.getQueryParameter("podcast_id") != null) {
				      podcast_id = deepLink.getQueryParameter("podcast_id")!!
				  }
				  if (deepLink!!.getQueryParameter("covid_card") != null) {
				      covid_card = deepLink.getQueryParameter("covid_card")!!
				  }
				  if (deepLink!!.getQueryParameter("filename") != null 
					  && deepLink.getQueryParameter("matchType") != null
					  && deepLink.getQueryParameter("pwa") != null) {
					      filename = deepLink.getQueryParameter("filename")!!
					      matchType = deepLink.getQueryParameter("matchType")!!
					      pwa = deepLink.getQueryParameter("pwa")!!
				  }
				  if (deepLink.getQueryParameter("matchesMode") != null) {
				       matchesMode = deepLink.getQueryParameter("matchesMode")!!
				  }

			      } catch (ex:Exception){}
			      fetchDynamicData.onFetchSuccess()
                  }else{
                      fetchDynamicData.onFetchSuccess()
                  }
              }catch (e:Exception){
                  e.printStackTrace()
                  fetchDynamicData.onFetchSuccess()
              }
           }
           .addOnFailureListener(this) {
               e -> Log.w("TAG", "getDynamicLink:onFailure", e)
           }
   ```
   ***Java***
   ```java
       FirebaseDynamicLinks.getInstance().getDynamicLink(intent)
               .addOnSuccessListener(new OnSuccessListener<PendingDynamicLinkData>() {
                   @Override
                   public void onSuccess(PendingDynamicLinkData pendingDynamicLinkData) {
                       Uri deepLink = null;
                       if (pendingDynamicLinkData != null) {
                                deepLink = pendingDynamicLinkData.getLink();
		       		if(deepLink.getQueryParameter("feed_id") != null) {
					if(deepLink.getQueryParameter("is_native")!=null){
						isNative = true
					}
				       post_id = deepLink.getQueryParameter("feed_id");
				}
				if (deepLink.getQueryParameter("filename") != null 
					&& deepLink.getQueryParameter("matchType") != null
					&& deepLink.getQueryParameter("pwa") != null) {
					filename = deepLink.getQueryParameter("filename");
					matchType = deepLink.getQueryParameter("matchType");
					pwa = deepLink.getQueryParameter("pwa");
				}
                           	if (deepLink.getQueryParameter("podcast_id") != null) {
                                	podcast_id = deepLink.getQueryParameter("podcast_id");
                           	}
			   	if (deepLink.getQueryParameter("covid_card") != null) {
                              		covid_card = deepLink.getQueryParameter("covid_card");
                           	}
                           	if (deepLink.getQueryParameter("matchesMode") != null) {
                                	matchesMode = deepLink.getQueryParameter("matchesMode");
                           	}
                       }
                   }
               })
               .addOnFailureListener(new OnFailureListener() {
                   @Override
                   public void onFailure(@NonNull Exception e) {
                       e.printStackTrace();
                   }
               });
   ```
2) Add the fetched params as extras to intent so that you can pass that as param in FeedSdk.handleIntent(this, intent)**[Step 2 in Handle Notifications]**

   ***Kotlin***
   ```kotlin
       if(post_id.isNotEmpty()) intent.putExtra("post_id", post_id)
       if(isNative) intent.putExtra("is_native", "true")
       if(podcast_id.isNotEmpty()) intent.putExtra("podcast_id",podcast_id)
       if(covid_card.isNotEmpty()) intent.putExtra("covid_card",covid_card)
       if(filename.isNotEmpty() && matchType.isNotEmpty() && pwa.isNotEmpty()){
		   intent.putExtra("filename", filename)
		   intent.putExtra("matchType", matchType)
		   intent.putExtra("link", pwa)
       }
       if(matchesMode.isNotEmpty()) intent.putExtra("matchesMode", matchesMode)
       // ...
       //pass the intent as param to FeedSdk.handleIntent(this, intent)[Step 2 in Handle Notifications]
       // ...
       
   ```
   ***Java***
   ```java
       if (!post_id.isEmpty())
           intent.putExtra("post_id", post_id);
       if(isNative) intent.putExtra("is_native", "true");
       if (!filename.isEmpty() && !matchType.isEmpty() && !pwa.isEmpty()) {
		   intent.putExtra("filename", filename);
		   intent.putExtra("matchType", matchType);
		   intent.putExtra("link", pwa);
       }
       if (!podcast_id.isEmpty())
           intent.putExtra("podcast_id", podcast_id);
       if (!covid_card.isEmpty())
	   intent.putExtra("covid_card", covid_card);
       if(!matchesMode.isEmpty())
           intent.putExtra("matchesMode", matchesMode);
       // ...
       //pass the intent as param to FeedSdk.handleIntent(this, intent)[Step 2 in Handle Notifications]
       // ...
   ```

## Handle Remote Config

[(Back to top)](#handle-notifications--dynamic-links)

1. Add "feedSdkConfig" key in your remote config with below json structure
```
{
  "socket_series": "",
  "feed_native_ad_interval": 60,
  "searchSticky": {
    "timer": 300,
    "workManager": 6,
    "showStickyOnTop": true
  }
}
```

## Notification Payload Examples

[(Back to top)](#table-of-contents)

- [Old Structures](#old-structures)
- [New Structures](#new-structures)

### Old Structures

### Payload to open feed detail Page

```groovy
{
    "to" : "/topics/Debug",
    "collapse_key" : "type_a",
    "data": {
            "title": "15 Photos Showing Kids’ Reactions to Stuff From the 1990s",
	    "message": "15 Photos Showing Kids’ Reactions to Stuff From the 1990s",
	    "image": "https://s3.amazonaws.com/asviral-wp-media/wp-content/uploads/2021/05/25193613/14-3-1.jpg",
	    "push_source": "feedsdk",
	    "post_source":"pushx",
	    "feed_type": "push",
	    "is_native":"true",
	    "post_id": "news_indiaheraldnews_9a2e4f2321ed47c0d7feea767bbd1892"
  	}
}
```
1) when "is_native"-> "true", it will open post native detail page else post detail page.

### Payload to open post as first card in feed category

```groovy
{
    "to" : "/topics/Debug",
    "collapse_key" : "type_a",
    "data": {
        "title": "15 Photos Showing Kids’ Reactions to Stuff From the 1990s",
        "message": "15 Photos Showing Kids’ Reactions to Stuff From the 1990s",
        "image": "https://s3.amazonaws.com/asviral-wp-media/wp-content/uploads/2021/05/25193613/14-3-1.jpg",
        "push_source": "feedsdk",
        "post_source": "trending",
        "interests":"news",
        "feed_type": "push",
        "post_id": "news_indiaheraldnews_9a2e4f2321ed47c0d7feea767bbd1892",
        "short_video":false
    }
}
```
1) "interests" can be changed to your required category tab to open in feeds.
2) If you open the short video post in reels then change "short_video" to **true** and add respective "post_id".

### Payload to open cricket match detail page.

```groovy
{
    "to" : "/topics/Debug",
    "collapse_key" : "type_a",
    "data": {
        "title": "PBKS Gets 1st Wicket, Rohit Sharma Leaves!",
        "message": "Ravi Bishnoi Gives PBKS Breakthrough. Catch LIVE Action",
        "image": "https://cricketimage.blob.core.windows.net/notifications/1st-Wicket_2021-09-28T21:45:02.616851+05:30.png",
        "filename": "mikp09282021204181",
	"link":"https://masterfeed.io/crichouse/match/mikp09282021204181",
        "launchType": "cricket",
        "post_source": "ipl_push"
    }
}
```

### Payload to open podcasat detail page.

```groovy
{
 "to" : "/topics/Debug",
 "collapse_key" : "type_a",
 "data": {
        "title": "15 Photos Showing Kids’ Reactions to Stuff From the 1990s",
	    "message": "15 Photos Showing Kids’ Reactions to Stuff From the 1990s",
	    "image": "https://s3.amazonaws.com/asviral-wp-media/wp-content/uploads/2021/05/25193613/14-3-1.jpg",
	    "link": "https://asviral.com/15-photos-showing-kids-reaction-to-stuff-from-the-1990s/?utm_source=push_Messenger Pro&utm_medium=asgrowth",
	    "which": "L",
	    "push_source": "feedsdk",
	    "post_source": "trending",
	    "feed_type": "push",
	    "post_id": "podcast_podcastIndex_637249_165e4c88900c19c079657b29c3f9a070",
	    "page":"SDK://podcastDetail"
    }
}
```

### New Structures

[(Back to top)](#notification-payload-examples)

### Payload to open feed category Page

```groovy
{
    "to" : "/topics/Debug",
    "collapse_key" : "type_a",
    "data": {
        "title": "15 Photos Showing Kids’ Reactions to Stuff From the 1990s",
	    "message": "15 Photos Showing Kids’ Reactions to Stuff From the 1990s",
	    "image": "https://s3.amazonaws.com/asviral-wp-media/wp-content/uploads/2021/05/25193613/14-3-1.jpg",
	    "page":"SDK://feed",
	    "push_source": "feedsdk",
	    "post_source": "trending",
	    "feed_type": "push",
	    "category": "news",
	    "post_id": "news_indiaheraldnews_9a2e4f2321ed47c0d7feea767bbd1892"
	}
}
```
1) If post_id is not added in payload, it will only redirect to that category tab

### Payload to open reels Page

```groovy
{
    "to" : "/topics/Debug",
    "collapse_key" : "type_a",
    "data": {
        "page": "SDK://reels",
        "category": "quick_bites",
        "title": "Krissh 3",
        "push_source":"feedsdk",
        "post_source": "video_push",
        "feed_type": "push",
        "post_id": "9gag_a41bZ8y",
        "message": "See hritiks best action super hero movie only on liveTv",
        "image": "https://img.youtube.com/vi/1N_zzi2ad04/hqdefault.jpg"
    }
}
```

## Common Errors Examples

[(Back to top)](#table-of-contents)

 #### Could not GET 'https://jitpack.io/com/github/Appyhigh/news-feed-sdk/....'. Received status code 403 from server: Forbidden
 
 #### Check the following steps to fix it
 
 1) Make sure you are using the authtoken in your project gradle file.
 
  ```
  allprojects {
	    repositories {
		...
		maven {
		    url "https://jitpack.io"
		    credentials { username authToken }
		}
	    }
	 }
  ```
 2) Check and disable Gradle 'offline mode' and sync project
 3) Update the url from "https://jitpack.io" to "https://www.jitpack.io"
 
  ```
  allprojects {
	    repositories {
		...
		maven {
		    url "https://jitpack.io"
		    credentials { username authToken }
		}
	    }
	 }
```
