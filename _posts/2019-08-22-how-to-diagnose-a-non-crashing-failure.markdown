---
layout: post
title:  "How to diagnose a non-crashing failure"
date:   2019-08-22 10:26:43 +0200
categories: iOS swift
---

As we may know, catching errors is pretty easy by using breakpoints or breakpoint-exceptions on Xcode, but sometimes we need to catch when a non-crashing error occurred on our app, for instance, when our API parser failed, or we expected to have a non-nil object, but a `nil` was returned.

_If you didn't know how to create an exception breakpoint, here's how. This breakpoint will jump on any crashing exception._

<img src="/post_assets/images/diagnose_post/create_exception.png" alt="" width="250"/>

But in this post we'll talk about how to get a console message when something goes wrong.

Imagine you have a very long `guard`, when you use it, it's because you want to make sure that everything is working as expected, so, for instance, you can create something like this:

{% highlight swift %}

//Given this struct
struct Person {
	
	var name : String
	var surname : String
	var age : Int
}

//You can create a parser method like this
class PersonParser {
	public class func transformDictionaryToPerson(_ dict: Dictionary<String, Any>) throws -> Person? {
	
		guard
			let name = dict["name"] as? String,
			let surname = dict["surname"] as? String,
			let age = dict["age"] as? Int
		else {

			//your throwing error here
			throw MyProject.MyErrors.unableToParseError
		}

		let person = Person(name: name, surname: surname, age: age)

		return person
	}
}

{% endhighlight %}

At this point, you have a non-crashing code, but if your parser fails on `guard` you won't be noticed unless you use a `do catch`:

{% highlight swift %}

do {

	//This will fail because we set a String on age. Int expected.
	let myDictionary = ["name" : "Carlos", "surname" : "Mellado", "age" : "not interesting"]

	let person = try PersonParser.transformDictionaryToPerson(myDictionary)
            
} catch {
            
	print("\(error.localizedDescription)")
}

{% endhighlight %}

Also, you can use `try?` without `do catch` and you will get a `nil` `Person`:

{% highlight swift %}

//This will return nil because we set a String on age. Int expected.
let myDictionary = ["name" : "Carlos", "surname" : "Mellado", "age" : "not interesting"]

let person = try? PersonParser.transformDictionaryToPerson(myDictionary)
            
//person is nil

{% endhighlight %}


<h2>The problem</h2>

This is what you see in the console when it fails:

<img src="/post_assets/images/diagnose_post/nothing_meme.jpg" alt="" width="350"/>

If you have just one person to parse, it's easy to find where's the problem.

But what about when you parse **n** persons?

<h2>Let's do the magic</h2>

We'll write a `public` returning-nil method accessible from the whole app, on this case, we'll write it on our `AppDelegate.swift`, like this:

{% highlight swift %}

public func diagnose<T>(file: String = #file, line: Int = #line, extraErrorString: String? = nil) -> T? {
    
    print("--------------\nDIAGNOSE FAILED\n--------------\nFILE \"\(file)\" AT LINE \(line) \(extraErrorString ?? "")\n--------------")
    return nil
}

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
//blah blah blah
{% endhighlight %}

And now, we'll modify our parser adding the `diagnose` method we created as fallback on `nil`. 

Like this:

{% highlight swift %}
class PersonParser {
	public class func transformDictionaryToPerson(_ dict: Dictionary<String, Any>) throws -> Person? {
	
		guard
			let name = dict["name"] as? String ?? diagnose(),
			let surname = dict["surname"] as? String ?? diagnose(),
			let age = dict["age"] as? Int ?? diagnose()
		else {

			//your throwing error here
			throw MyProject.MyErrors.unableToParseError
		}

		let person = Person(name: name, surname: surname, age: age)

		return person
	}
}
{% endhighlight %}

With this little changes, we'll see the following on the console when having an error:

{% highlight swift %}
--------------
DIAGNOSE FAILED
--------------
FILE "/Users/carlosmellado/test_project/PersonParser.swift" AT LINE 7
--------------
{% endhighlight %}

So, now, you have the failing file and the line causing the issue. 

<h2>Here comes more magic, again</h2>

Let's go to do something better. 

Let's see the `key` and `Dictionary` causing the errors, and also, let's create an easier way to extract values from `Dictionary` by extending our `Dictionary` class:

{% highlight swift %}

extension Dictionary {
    
    func me_object<T>(forKey key: String, returningType: T.Type, diagnosing: Bool = true, file: String = #file, line: Int = #line) -> T? {
        
        return (self as? Dictionary<String, Any>)?[key] as? T ?? (diagnosing ? diagnose(file: file, line: line, extraErrorString: "parsing key \"\(key)\" to class \(T.self) at following object:\n\(self as AnyObject)") : nil)
    }
}

{% endhighlight %}

And now, go back to the parser method to do some changes:

{% highlight swift %}
class PersonParser {
	public class func transformDictionaryToPerson(_ dict: Dictionary<String, Any>) throws -> Person? {
	
		guard
			let name = dict.me_object(forKey: "name", returningType: String.self),
			let surname = dict.me_object(forKey: "surname", returningType: String.self),
			let age = dict.me_object(forKey: "age", returningType: Int.self)
		else {

			//your throwing error here
			throw MyProject.MyErrors.unableToParseError
		}

		let person = Person(name: name, surname: surname, age: age)

		return person
	}
}
{% endhighlight %}

As you can see, you can let the method `me_object(forKey:returningType:)` what kind of element you expect, super practical and easy to use.

And the best is yet to come, if you look at the console, you'll see the following when failing:

{% highlight swift %}
--------------
DIAGNOSE FAILED
--------------
FILE "/Users/carlosmellado/test_project/PersonParser.swift" AT LINE 7 parsing key "age" to class String at following object:
{
    name = Carlos;
    surname = Mellado;
    age = "not interesting";
}
--------------
{% endhighlight %}

<h1>Boom!</h1>

Now you can find why your parser is failing just by looking at the console, pretty useful.


You can also avoid console diagnosing by entering `diagnosing: false` on `me_object(forKey:returningType:)` like this:

{% highlight swift %}

dict.me_object(forKey: "name", returningType: String.self, diagnosing: false)

{% endhighlight %}


And that's all!

Hope you liked this post, one of the most interestings in my oppinion instead of being a little bit long! 😅