---
layout: post
title:  "Using filter function"
date:   2019-08-19 11:57:09 +0200
categories: iOS swift
---
On this post we'll talk about `filter` function on Swift, and how it works.

<h2>Okay. So, what filter does?</h2>

`filter` filters.

<img src="/assets/images/captain_obvious.png" alt="" width="500"/>

<h2>😅Well, better explained</h2>
Filter allow us to select a desired objects within an `array`. 
For instance:

{% highlight swift %}

let numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

let graterThan5 = numbers.filter({$0 > 5})

/*
    Output:
    6
    7
    8
    9
    10
*/
{% endhighlight %}

`$0` will be each value on the numbers `array`, and will be filtered using our condition. If condition is true, it will be added to the returned value, else, will be discarded.

With `filter` you can also check for values if the array contains a list of objects, so imagine:

{% highlight swift %}

//Given this struct
struct Person {

    var name : String?
    var age : Int?    
}
{% endhighlight %}

You can filter an array of persons by using:

{% highlight swift %}

//personsArray will contain some Person objects
let personsArray = MyAPI.getPeople()

let legalOfAge = personsArray.filter({$0.age >= 18})

//legalOfAge now contains persons with an age greater or equal to 18
{% endhighlight %}


On the same way that `map` has a default completion block, you can use it on filter, to add all conditions you want.

{% highlight swift %}

let newArray = personsArray.filter { (person) -> Bool in
            
    //Your conditions here...
    return true //true to add or false to discard
}

{% endhighlight %}
_All returning true values will be added to `newArray`. Else, the values will be discarded._


So now, you know how to filter your values! On other post we'll talk about [sorting items]({% post_url 2019-08-19-using-sort-function %}) with `sort` function.

Enjoy filtering!

