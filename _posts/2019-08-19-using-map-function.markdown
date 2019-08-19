---
layout: post
title:  "Using map function"
date:   2019-08-19 11:57:09 +0200
categories: iOS swift
---
A lot of times, we need to transform an array of objects to a different type of objects, and we used to do with a mutable array, by creating instances of the new objects and adding them to a new array that we will return, more or less like the following code:

{% highlight swift %}
//Having this struct  
struct Person {      
    
    var name : String?      
    var surname : String?      
 
    init(withDict dict: Dictionary) {          

        self.name = dict["name"]          
        self.surname = dict["surname"]      
    }  
}
{% endhighlight %}

We should do the following to transform an array of `Dictionary` to a `Person` array:

{% highlight swift %}
var persons = [Person]()

for dict in dicts {

    let person = Person(withDict: dict)
    persons.append(person)
}

return persons
{% endhighlight %}

By this way, we obtain a new array of `Persons` by going through all the values of the `Dictionary` array.

`map` function on Swift, will allow us to do it in a faster and powerful way:

{% highlight swift %}
dicts.map(Person.init(withDict: ))
//Boom!
{% endhighlight %}

It’ll insert every object in your constructor and will return it.
But this is just the beginning. If your constructor can return `nil` objects, you can use `compactMap` to do the same as `map` but just returning non-nil objects.

Also, you can use `map` to return an array of values, for instance, let’s say we have an array of `Person` and we want to obtain an array of their names, in a `String` array.
So, this is as easy as:

{% highlight swift %}
return personsArray.map({$0.name})
{% endhighlight %}

And this will return an array of name properties, declared as `String`.
Remember, you still can use compactMap to get non-nil values.

![image](/assets/images/map_image_1.jpg)

<h1>Wait</h1>
Still needing a more powerful method for mapping?
You can use a default completion for this, and start typing your own conditions to get your desired mapping.

{% highlight swift %}
arrayDict.compactMap { (dictObject) -> Person? in        
    //Your conditions here
    return //your object, or nil 
}
{% endhighlight %}

Uh… Okay, this works. But in this case, I recommend to use `filter` after or before your mapping. But just to let you know that you can use `map` in a different way.
We’ll talk about [filter]({% post_url 2019-08-19-using-filter-function %}) in other posts!

Enjoy mapping!