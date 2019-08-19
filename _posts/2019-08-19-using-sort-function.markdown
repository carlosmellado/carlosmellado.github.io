---
layout: post
title:  "Using sort function"
date:   2019-08-19 15:27:09 +0200
categories: iOS swift
---
This time we'll talk about `sort` function on Swift.
Forget about [Captain Obvious]({% post_url 2019-08-19-using-filter-function %}) this time let's go directly to a good definition provided by apple:

`Returns the elements of the sequence, sorted.`

![image](/assets/images/what.jpg)

It seems Captain Obvious strikes again.

Well, at least Cocoa/Foundation uses function names completely describing what the function does. So, nothing more to explain here.
Let's see how it works.

<h2>sort or sorted?</h2>

Yes, maybe you figured out that there are two similar methods. `sort` and `sorted`.

Usage are exactly the same, but `sort` will make the changes on your given array and `sorted` will return a new array instance with the items sorted.

_Be aware that if you use `sort` you need to use a mutable value. In other words, use `var` instead `let` if you want to use `sort`_

{% highlight swift %}

var myNumbers = [1, 2, 3, 4, 5]
print("\(myNumbers)")

/*
    Output:
    1
    2
    3
    4
    5
*/

//Then, we'll change the order
myNumbers.sort(by: {$0 > $1})
print("\(myNumbers)")

/*
    Output:
    5
    4
    3
    2
    1
*/
{% endhighlight %}


As you can see, like [map]({% post_url 2019-08-19-using-map-function %}) or [filter]({% post_url 2019-08-19-using-filter-function %}) functions, you can use `$0` to refer to your first parameter you're sorting, and `$1` to the second one, so you can play with both.

In this case, we wanted to sort the numbers descending, that's why we used `$0 > $1`.

Sorted works in the same way, so let's see an example:

{% highlight swift %}

let myNumbers = [1, 2, 3, 4, 5]
print("\(myNumbers)")

/*
    Output:
    1
    2
    3
    4
    5
*/

//Then, we'll change the order
let descNumbers = myNumbers.sorted(by: {$0 > $1})
print("\(descNumbers)")

/*
    Output:
    5
    4
    3
    2
    13
*/
{% endhighlight %}

On this case is **important** that you figure out that we have two instances. One is named `myNumbers` that contains the main array, without sorting, and we created another one named `descNumbers` containing the values sorted.
On this case, as we didn't need a mutable objects, we declared it as `let` instead `var` as before.

You can sort an array of objects by comparing a property within, for instance:

{% highlight swift %}

//personsArray will contain some Person objects sorted by name ascendently
let personsArray = MyAPI.getPeople().sorted(by: { $0.name < $1.name })


{% endhighlight %}

And well, if we need a more complex conditions, we can use the default completion:

{% highlight swift %}

//personsArray will contain some Person objects
var personsArray = MyAPI.getPeople()

//lets sort as we want
personsArray.sorted { (firstPerson, secondPerson) -> Bool in
            
    //Execute the comparisons here.
    return true //Return true to return it first, or false to return it last
}

{% endhighlight %}

And that's all for the `sort` and `sorted` chapter! Hope you enjoyed!

Enjoy sorting!