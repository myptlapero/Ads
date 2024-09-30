# Native Preload and Native Fullscreen

## 1. Native Preload

- Native ads can be formatted to match app's visual design
- When ads loads, app receives an ad object that contains its assets, and is responsible for displaying them

### a. Load

- Use preload ad in before screen to preload ad for next screen
- Follow these steps:

  - Layout xml ad view: `native_ad_layout.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <com.google.android.gms.ads.nativead.NativeAdView
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@drawable/bg_native_exit"
        android:backgroundTint="#FBFBFF">

        <androidx.constraintlayout.widget.ConstraintLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@drawable/bg_native_exit"
            android:backgroundTint="#F3F3F3"
            android:padding="@dimen/_5sdp">


            <com.makeramen.roundedimageview.RoundedImageView
                android:id="@+id/ad_app_icon"
                android:layout_width="@dimen/_36sdp"
                android:layout_height="@dimen/_36sdp"
                android:adjustViewBounds="true"
                android:src="@color/colorPrimary"
                app:layout_constraintBottom_toBottomOf="parent"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toTopOf="parent"
                app:riv_corner_radius="8dp" />

            <TextView
                android:id="@+id/textView4"
                style="@style/AppTheme.Ads.Rounded"
                android:layout_gravity="center_vertical"
                app:layout_constraintTop_toTopOf="@id/ad_app_icon"
                android:layout_marginStart="@dimen/_10sdp"
                app:layout_constraintStart_toEndOf="@+id/ad_app_icon"
                android:background="@drawable/bg_corner_4dp" />

            <TextView
                android:id="@+id/ad_headline"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_marginStart="@dimen/_4sdp"
                android:ellipsize="end"
                android:fontFamily="@font/inter_bold"
                android:maxLines="1"
                android:textColor="#333333"
                android:textSize="16sp"
                android:textStyle="bold"
                tools:text="hello world"
                app:layout_constraintTop_toTopOf="@+id/ad_app_icon"
                app:layout_constraintBottom_toBottomOf="@id/textView4"
                app:layout_constraintStart_toEndOf="@+id/textView4"/>

            <TextView
                android:id="@+id/ad_body"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                app:layout_constraintWidth_percent="0.5"
                android:ellipsize="end"
                android:lines="2"
                android:maxLength="90"
                android:textColor="@color/color_666666"
                android:textSize="12sp"
                app:layout_constraintBottom_toBottomOf="@+id/ad_app_icon"
                app:layout_constraintStart_toStartOf="@+id/textView4"
                app:layout_constraintTop_toBottomOf="@+id/textView4"
                tools:text="@tools:sample/lorem/random" />

            <Button
                android:id="@+id/ad_call_to_action"
                style="?android:attr/borderlessButtonStyle"
                android:layout_width="wrap_content"
                android:layout_height="@dimen/_36sdp"
                android:layout_marginTop="@dimen/_5sdp"
                android:background="@drawable/background_radius_50"
                android:backgroundTint="#E6E6E6"
                android:gravity="center"
                android:paddingHorizontal="@dimen/_16sdp"
                android:textAllCaps="true"
                android:textColor="#444444"
                android:textSize="@dimen/_14ssp"
                android:textStyle="bold"
                app:layout_constraintBottom_toBottomOf="parent"
                app:layout_constraintEnd_toEndOf="parent"
                app:layout_constraintTop_toTopOf="parent"
                tools:text="Settings" />
        </androidx.constraintlayout.widget.ConstraintLayout>

    </com.google.android.gms.ads.nativead.NativeAdView>
    ```

    Preview Native Ad Layout:

    ![image](https://github.com/user-attachments/assets/1943daa8-c855-4422-bf53-43858253840e)


  - Create NativeAdConfig and implement method preload in previous screen:

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = inflateBinding(layoutInflater)
        setContentView(binding.root)

        // create native ad config
        val nativeAdConfig = NativeAdConfig(
            idAds = "your_id_ads",
            canShowAds = true,
            canReloadAds = true,
            layoutId =  R.layout.native_ad_layout
        )

        // Preload native ad
        NativeAdPreload.getInstance().preload(
            context = this,
            nativeAdConfig = nativeAdConfig
        )
    }
    ```

    `idAds: String`: is your ad unit id, value is fixed, not change.

    `canShowAds: Boolean`: is a flag to show or hide ads.

    `canReloadAds: Boolean`: is a flag to reload ads.

    `layoutId: Int`: is a layout id of native ad view.

### b. Show

- In the screen contains the previous preloaded ad, follow these steps to display the ad.
- In xml layout create layout shimmer to show loading: `native_shimmer.xml`

  ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <com.facebook.shimmer.ShimmerFrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:id="@+id/shimmerContainerNative"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@drawable/bg_native_exit"
        android:backgroundTint="#FBFBFF">

        <androidx.constraintlayout.widget.ConstraintLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@drawable/bg_native_exit"
            android:padding="@dimen/_5sdp">

            <com.makeramen.roundedimageview.RoundedImageView
                android:id="@+id/ad_app_icon"
                android:layout_width="@dimen/_36sdp"
                android:layout_height="@dimen/_36sdp"
                android:adjustViewBounds="true"
                android:src="@color/lightTransparent"
                app:layout_constraintBottom_toBottomOf="parent"
                app:layout_constraintStart_toStartOf="parent"
                app:layout_constraintTop_toTopOf="parent"
                app:riv_corner_radius="8dp" />

            <TextView
                android:id="@+id/textView4"
                style="@style/AppTheme.Ads.Rounded"
                android:layout_gravity="center_vertical"
                android:layout_marginStart="@dimen/_10sdp"
                android:background="@drawable/bg_corner_4dp"
                app:layout_constraintStart_toEndOf="@+id/ad_app_icon"
                app:layout_constraintTop_toTopOf="@id/ad_app_icon" />

            <TextView
                android:id="@+id/ad_headline"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_marginStart="@dimen/_2sdp"
                android:background="@color/lightTransparent"
                android:ellipsize="end"
                android:maxLines="1"
                android:textColor="#333333"
                android:textSize="16sp"
                android:textStyle="bold"
                app:layout_constraintBottom_toBottomOf="@id/textView4"
                app:layout_constraintStart_toEndOf="@+id/textView4"
                app:layout_constraintTop_toTopOf="@+id/ad_app_icon"
                app:layout_constraintEnd_toEndOf="@id/ad_body"/>

            <TextView
                android:id="@+id/ad_body"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:background="@color/lightTransparent"
                android:ellipsize="end"
                android:lines="2"
                android:maxLength="90"
                android:textColor="@color/color_666666"
                android:textSize="8sp"
                app:layout_constraintBottom_toBottomOf="@+id/ad_app_icon"
                app:layout_constraintStart_toStartOf="@+id/textView4"
                app:layout_constraintTop_toBottomOf="@+id/textView4"
                app:layout_constraintWidth_percent="0.6" />

            <Button
                android:id="@+id/ad_call_to_action"
                style="?android:attr/borderlessButtonStyle"
                android:layout_width="wrap_content"
                android:layout_height="@dimen/_40sdp"
                android:layout_marginTop="@dimen/_5sdp"
                android:background="@drawable/background_radius_50"
                android:backgroundTint="@color/lightTransparent"
                android:gravity="center"
                android:paddingHorizontal="@dimen/_16sdp"
                android:textAllCaps="true"
                android:textColor="#444444"
                android:textSize="@dimen/_14ssp"
                android:textStyle="bold"
                app:layout_constraintBottom_toBottomOf="@id/ad_app_icon"
                app:layout_constraintEnd_toEndOf="parent"
                app:layout_constraintTop_toTopOf="@id/ad_app_icon" />
        </androidx.constraintlayout.widget.ConstraintLayout>

    </com.facebook.shimmer.ShimmerFrameLayout>
  ```

  Preview Native Shimmer Layout:

  ![image](https://github.com/user-attachments/assets/3c7493d2-7813-4b61-9127-0ebe6a49d733)


- Layout xml screen contains ad view include: `native_shimmer.xml`

  ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <androidx.constraintlayout.widget.ConstraintLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <FrameLayout
            android:id="@+id/frAds"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:background="@color/transparent"
            android:visibility="visible"
            app:layout_constraintBottom_toBottomOf="parent">

            <include
                android:id="@+id/includeNative"
                layout="@layout/native_shimmer"
                android:visibility="visible" />
        </FrameLayout>

    </androidx.constraintlayout.widget.ConstraintLayout>
  ```

  Preview Screen Layout:

  ![image](https://github.com/user-attachments/assets/0c90f05d-7547-4aa6-a1dd-55787ac0857b)

- Implement method show in the screen:

  ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = inflateBinding(layoutInflater)
        setContentView(binding.root)

        // create native ad config
        val nativeAdConfig = NativeAdConfig(
            idAds = "your_id_ads",
            canShowAds = true,
            canReloadAds = true,
            layoutId = R.layout.native_ad_layout
        )

        // init nativeAdHelper
        val nativeAdHelper = NativeAdHelper(
            context = this,
            lifecycleOwner = this,
            nativeAdConfig = nativeAdConfig
        )

        // can regis ad call back
        NativeAdPreload.getInstance().registerAdCallback(
            nativeAdConfig = nativeAdConfig, // can pass adId
            adCallback = object : AperoAdCallback() {
                override fun onAdImpression() {
                    super.onAdImpression()
                    // this is call back when ads is shown to user
                }

                override fun onAdClicked() {
                    super.onAdClicked()
                    // this is call back when user click on ads to open ad
                }

                override fun onAdLoaded() {
                    super.onAdLoaded()
                    // this is call back when ads is loaded successfully and ready to show
                }
            }
        )

        // this method is used to set the native ad view
        nativeAdHelper.setNativeContentView(binding.frAds)


        // this method is used to set the shimmer layout view if the native ad is loading
        nativeAdHelper.setShimmerLayoutView(binding.includeNative.shimmerContainerNative)

        // this method is used to preload the native ad if the buffer is empty
        // if ads be preloaded, it will not load again
        nativeAdHelper.requestAds(NativeAdParam.Request.create())

    }
  ```
  
## 2. Native Fullscreen-

- Similar to native preload, but ads will be displayed in full screen
- Follow these steps:

  - Create shimmer layout: `native_fullscreen_shimmer.xml`:
    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <com.facebook.shimmer.ShimmerFrameLayout 
            xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            android:id="@+id/shimmerContainerNative"
            android:layout_width="match_parent"
            android:layout_height="match_parent">

            <androidx.constraintlayout.widget.ConstraintLayout
                android:id="@+id/ad_unit_content"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_gravity="bottom"
                android:layout_marginHorizontal="@dimen/_12sdp"
                android:layout_marginBottom="@dimen/_16sdp"
                android:background="@color/white"
                android:layoutDirection="ltr"
                android:orientation="vertical"
                android:paddingBottom="@dimen/_8sdp">

                <androidx.appcompat.widget.AppCompatTextView
                    android:id="@+id/iconAd"
                    style="@style/AppTheme.Ads"
                    android:background="@drawable/bg_ad_ads_tl_8_br_8"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintTop_toTopOf="parent" />

                <androidx.appcompat.widget.AppCompatImageView
                    android:id="@+id/ad_app_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp"
                    android:layout_marginStart="@dimen/_10sdp"
                    android:adjustViewBounds="true"
                    android:src="@color/lightTransparent"
                    app:layout_constraintStart_toStartOf="parent"
                    app:layout_constraintTop_toBottomOf="@id/iconAd" />

                <androidx.appcompat.widget.AppCompatTextView
                    android:id="@+id/ad_headline"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_marginStart="@dimen/_5sdp"
                    android:layout_marginEnd="@dimen/_10sdp"
                    android:background="@color/lightTransparent"
                    android:ellipsize="end"
                    android:maxLines="2"
                    android:textColor="@color/color_red"
                    android:textSize="14sp"
                    android:textStyle="bold"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toEndOf="@id/ad_app_icon"
                    app:layout_constraintTop_toTopOf="@id/ad_app_icon" />

                <androidx.appcompat.widget.AppCompatTextView
                    android:id="@+id/ad_body"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_marginStart="@dimen/_5sdp"
                    android:layout_marginEnd="@dimen/_10sdp"
                    android:background="@color/lightTransparent"
                    android:ellipsize="end"
                    android:lines="2"
                    android:textColor="#797C80"
                    android:textSize="12sp"
                    app:layout_constraintBottom_toBottomOf="@id/ad_app_icon"
                    app:layout_constraintEnd_toEndOf="parent"
                    app:layout_constraintStart_toEndOf="@id/ad_app_icon"
                    app:layout_constraintTop_toBottomOf="@id/ad_headline" />

                <androidx.appcompat.widget.AppCompatButton
                    android:id="@+id/ad_call_to_action"
                    android:layout_width="match_parent"
                    android:layout_height="@dimen/_35sdp"
                    android:layout_marginHorizontal="@dimen/_10sdp"
                    android:layout_marginVertical="@dimen/_10sdp"
                    android:background="@drawable/background_radius_50"
                    android:backgroundTint="@color/lightTransparent"
                    android:gravity="center"
                    android:stateListAnimator="@null"
                    android:textColor="@color/colorWhite"
                    android:textSize="@dimen/_12sdp"
                    android:textStyle="bold"
                    app:layout_constraintTop_toBottomOf="@id/ad_body" />
            </androidx.constraintlayout.widget.ConstraintLayout>

        </com.facebook.shimmer.ShimmerFrameLayout>

    ```

    Preview Native Fullscreen Shimmer Layout:
    
    ![image](https://github.com/user-attachments/assets/043ee4b0-12ba-4415-8b48-2fbc270639e4)

    
  - Create layout xml ad view: `native_fullscreen.xml`

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <FrameLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <FrameLayout
            android:id="@+id/frAds"
            android:layout_width="match_parent"
            android:layout_height="match_parent">

            <include
                android:id="@+id/includeNativeFullScreen"
                layout="@layout/native_fullscreen_shimmer"
                android:visibility="visible" />
        </FrameLayout>

        <androidx.appcompat.widget.AppCompatImageView
            android:id="@+id/icClose"
            android:layout_width="36dp"
            android:layout_height="36dp"
            android:layout_gravity="right"
            android:layout_margin="24dp"
            android:padding="8dp"
            android:src="@drawable/ic_close"
            app:tint="#80000000" />
    </FrameLayout>
    ```

  - Implement method show in the screen:

    ```kotlin
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = inflateBinding(layoutInflater)
        setContentView(binding.root)

        // create native ad config
        val nativeAdConfig = NativeAdConfig(
            idAds = "your_id_ads_fullscreen",
            canShowAds = true,
            canReloadAds = true,
            layoutId = R.layout.native_fullscreen
        )

        // init nativeAdHelper
        val nativeAdHelper = NativeAdHelper(
            context = this,
            lifecycleOwner = this,
            nativeAdConfig = nativeAdConfig
        )

        // can regis registerAdListener
        nativeAdHelper.registerAdListener(object : AperoAdCallback() {
            override fun onAdImpression() {
                super.onAdImpression()
                // this is call back when ads is shown to user
            }

            override fun onAdClicked() {
                super.onAdClicked()
                // this is call back when user click on ads to open ad
            }

            override fun onAdLoaded() {
                super.onAdLoaded()
                // this is call back when ads is loaded successfully and ready to show
            }
        })

        // this method is used to set the native ad view
        nativeAdHelper.setNativeContentView(binding.frAds)

        // this method is used to set the shimmer layout view if the native ad is loading
        nativeAdHelper.setShimmerLayoutView(binding.includeNativeFullScreen.shimmerContainerNativeFullScreen)

        // this method is used to preload the native ad if the buffer is empty
        // if ads be preloaded, it will not load again
        nativeAdHelper.requestAds(NativeAdParam.Request.create())
    }
    ```

- Preview native fullscreen:
    - Loading ads:
      
      ![image](https://github.com/user-attachments/assets/4fb9a6c6-3e1d-4b10-adda-ba8c8a0fe7a3)


    - Ads loaded:
 
      

    
