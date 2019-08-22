---
layout: post
title:  "How to have different app icons based on your build config"
date:   2019-08-22 10:26:42 +0200
categories: iOS swift
---

Many times, we want to have different app icons for each of our build configurations, for instance, for have some differencies between the test and the production versions.

On this case, we have to do something we can sound us familiar, as it has something related with [this post]({% post_url 2019-08-20-using-user-defined-settings-based-on-build-settings %}).

So, this time there's a quick and easy solution. Go to your _**workspace**_ and select `Assets.xcassets` to open the asset catalog where you have your `AppIcon` assets, containing the main icons for your app.

Create a new asset catalog by tapping on `+` -> `App Icons & Launch Images` + `New iOS App Icon`.

By doing this, you'll see a new asset catalog that you should fill with your custom images. Let's call it `AppIcon.dev` _(You can call it like you want)_


<img src="/post_assets/images/app_icons/new_asset.png" alt="" width="450"/>

Once you have all your assets set, let's go to the next step.

<h3>Now, tell Xcode choose them depending on build configurations</h3>

Now that we have our App Icons set, let's open our _**workspace**_, select our _**target**_,click on _**Build Settings**_ and filter by _**App Icon**_.

<img src="/post_assets/images/app_icons/build_settings.png" alt="" width="100%"/>

Now, on _**Asset Catalog App Icon Set Name**_ you must change the values for every build configuration you want, setting the name you gave to your new asset.

Done this, you're ready to build and enjoy having different icons.

Enjoy!
