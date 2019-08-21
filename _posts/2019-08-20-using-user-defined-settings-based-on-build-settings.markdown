---
layout: post
title:  "Using User Defined Settings based on build settings"
date:   2019-08-20 17:13:43 +0200
categories: iOS swift
---
Sometimes, we want to keep different build configurations, for example, when we want to add an external SDK that requires an API key, and you want to have different ones for _**debug**_ and _**release**_ versions.

<img src="/post_assets/images/dothethingsright.jpg" alt="" width="400"/>


<h2>Use User Defined Settings</h2>

As you may know, you can access to `Info.plist` values within your code, by using something like:

{% highlight swift %}

if let yourValue = Bundle.main.object(forInfoDictionaryKey: "your_key") as? String {
                
    return yourValue
}
            
assertionFailure("your_key was not found on dictionary")

{% endhighlight %}


Or we can create a magic function using `Type` that returns always a value reading the `Info.plist`, like this:

{% highlight swift %}

class func getValueFromInfoPlist<T>(withKey key: String?, returning: T.Type) -> T? {
        
    if let key = key, let infoValue = Bundle.main.object(forInfoDictionaryKey: key) as? T {
            
        return infoValue
    }
        
    return nil
}


{% endhighlight %}


In this way, we can call whatever we want from our `Info.plist` by using this function like this:

{% highlight swift %}

let myString = MyClass.getValueFromInfoPlist(withKey: "my_string_key", returning: String.self)
//myString will contain a String?

let myInt = MyClass.getValueFromInfoPlist(withKey: "my_int_key", returning: Int.self)
//myInt will contain a Int?

{% endhighlight %}

<h2>Yes, but show me how to have different values per build setting</h2>

Okay. Now you know how to get values from your plist. Now, lets go to your _**workspace**_ and go to _**build settings**_, press on `+` button and click on `Add User-Defined Settings`.

<img src="/post_assets/images/build_settings_user_defined.png" alt="" width="100%"/>

Now, depending on your build configurations, you'll see a different numbers of fields for fill. Each one with the name of your build config.
Here you can enter the different values you want.
_Let's say we have three build configurations: **Debug**, **Release** and **Staging**, so we will see the following:_

<img src="/post_assets/images/build_configs.png" width="400"/>

So, at this point, we should go to our `Info.plist` and add a new value with the name that you choose, and here comes the magic, the value should be `$(MY_KEY)` or in other words, `$(YOUR_NAME_ON_USER_DEFINED_SETTING)`.

<img src="/post_assets/images/build_configs_info_plist.png" width="400"/>

Finally, you can use the method for getting the value on the `Info.plist`. When building the app, the compiler will set the value depending on your `Build Config`, so you can set your keys saved and they can change without changin anything on your code, just compiling with different build settings!

{% highlight swift %}

let myString = MyClass.getValueFromInfoPlist(withKey: "my_key", returning: String.self)
//On Debug configuration will return "debug_key"
//On Release configuration will return "release_key"
//On Staging configuration will return "staging_key"
{% endhighlight %}

So, now that you know how to doing this, you've no excuses for use different keys on debug and production versions!

Hope you enjoyed!

