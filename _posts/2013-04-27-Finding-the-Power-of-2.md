---
layout: blog
title: Looking for the Power of Two
summary: Understanding the significance of powers of 2 and bitwise operations.
date: 2013-04-27
postNum: 04272013
comments: true
---

#__April 27, 2013__

**_How would you find if a given input is 2<sup>n</sup>?_**


Recently I was asked this very basic programming question and it made me think about the importance of understanding the fundamental methods of dealing with patterns in numbers. This question also led me to some very interesting solutions that made my initial response feel, well... weak.


At first glance the question seems very straight forward, which it is, so depending on your preferred programming language there are many different ways of solving it; I chose to use JavaScript. When I was asked this question for the first time it took me a few minutes to structure a solution, after which I realized that it definitely wasn't the best or most efficient way to solve such a problem.

##Initial solution:

{% highlight js linenos %}
    x = function ( n ){
      a = [];
        for (i=0; i <= 100; i++) {
          a.push( Math.pow( 2, i ));
        }
        if ( a.indexOf( n ) > -1 ) {
          console.log( true );
        } else{
          console.log( false );
        }
    };
{% endhighlight %}

Here I created a function that accepts one input argument _n_ and checks whether that input is a power of 2 using iteration.

**Step 1:**
Create a function that takes a single input argument _n_.

**Step 2:**
Create an empty array _a_

**Step 3:**
Create a for loop to store the first 100 powers of 2 in the array. This is done using JavaScript's .pow method which accepts 2 arguments, a base and an exponent. With a given base of 2 and the counter variable i from the loop as the exponent, the loop will return the all of the powers of 2 up to 100; passing this as the item inside of the .push method called on array _a_ stores each of the returned values as an item in the array.

**Step 4:**
Now that we have an array containing the first 100 powers of 2, we can then check if a given input exists inside of the array by using a conditional if statement and the JavaScript indexOf method. If the index of the given input exists inside of the array we print true to the console, if it doesn't we print false.

This initial solution I came to is definitely not the most elegant or efficient. A Google search on the topic will yield you plenty of different examples describing how to solve the question, but what intrigued me the most about the results I found were the examples using bitwise math to derive the solution. Most of my programming knowledge has been acquired through self-teaching and the first programming language I learned was JavaScript which is quite different from other, more functional, programming languages. Because of this, the iterative and recursive approaches typically come to my mind first when solving problems.

I've never had to use bitwise operators or bit-math in my career as a developer, being that it isn't as practical or necessary as it was in the earlier days of computing and, more specifically, in my line of work which is front-end development. Regardless, I needed to know why this approach was useful and also how it worked, so I took this as an opportunity to learn something new. After some intense reading on the topic I felt that I was slightly capable of structuring a solution that would return the same result as the one I had created before, only this time using bitwise operations.

However, before I began to write my solution I needed to understand how to use the binary operators to get the answer I was looking for. So, in order to truly grasp the concept, I needed to see how the powers of 2, as expressed in binary format, were significant and comparable to other numbers. Visuals tend to work best for me so I wrote the following script to lists all of the powers of 2 from 1 to 100 as represented in binary format. Run it in a console and see for yourself:

{% highlight js linenos %}
    ( function (){
      a = [];
        for ( i=0; i <= 100; i++ ) {
          a.push( Math.pow( 2, i ) );
        }

        a.forEach(function ( b ){
          console.log( b.toString( 2 ) );
        });
    })();
{% endhighlight %}

After viewing the output in a console, it's easy to see the pattern. Below is another version of the previous script, only this time displaying the binary equivalent of every number between 0 and 1000, with true if it is a power of 2 and false if it isn't. <a id="bits">Try this in a console:</a>

{% highlight js linenos %}
    (function () {
      var a = [];
      var x = [];
      for ( i=0; i <= 100; i++ ) {
        a.push( Math.pow( 2, i ) );
      }
      for ( n=0; n<=1000; n++ ){
        x.push( n );
      }
      x.forEach( function ( b ){
        if ( a.indexOf( b ) > -1 ){
          z = true;
        } else{
          z = false;
        }
        console.log( b.toString( 2 ), z );
      });
    })();
{% endhighlight %}

By comparing the previous scripts it became clear how the pattern has significance, each power of 2 has exactly one bit in its representation (1, 10, 100, 1000 ... 100000) and each integer preceding it having a full set of bits (11, 111, 1111 ... 111111). After seeing this it was easy to understand why powers of 2 are so significant. Now when looking at the other examples using bit-math to solve this problem, it no longer made my brain feel like it was going to shit down my spine. Here's the solution I came to:

##Bitwise solution:

{% highlight js linenos %}
    x = function ( n ) {
      if ( n !== 0 && ( n & ( n - 1 ) ) === 0 ){
        console.log( true );
      } else{
        console.log( false );
      }
    };
{% endhighlight %}

**Step 1:**
Create a function that takes a single input argument _n_.

**Step 2:**
Inside the function create a conditional statement that will print true if the input is a power of 2 and false if it isn't. Inside of the conditional we want to ensure that both:  

- _n_ is not 0; to eliminate the false positive produced for _n=0_

- _n_ is a power of 2

In order to do this we compare _n !== 0_ to ensure that if _n = 0_ the input isn't mistaken to be a power of 2 (false positive). Next we use the bitwiseAND ( _&_ ) operator to check if the input is a power of 2 by comparing it to itself minus 1 which, if true, will evaluate to 0 and indicate that we've found a power of 2; if we hadn't excluded _n = 0_ on the other side of the conditional statement, then entering 0 for _n_ would result in the program incorrectly indicating that 0 is a power of 2 (remove it from the if statement and see for yourself). Now using the JavaScript _&&_ operator we ensure that the program only indicates that a power of 2 has been found when both conditions are met.

The real meat of the solution is contained in the following operation: _n & (n - 1)) === 0_. To understand what's happening here you need to know that the _&_ operator is defined as _**returning a one in each bit position for which the corresponding bits of both operands are ones**_; for example take a = 2 and b = 3, so saying a & b is the same as saying 10 & 11, therefore a & b = 10. Another example, take a = 2 and b = 1, so a & b (or 10 & 1) = 0. Read the docs on bitwise operations [HERE](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Operators/Bitwise_Operators) if this is unclear.

Now back in our program, this part of the condition checks the input against itself minus 1 _(n & (n - 1))_ to verify that it evaluates to 0. This happens because, as we saw in the pattern examples presented above, each power of 2 only has exactly 1 bit inside of itself. By observing the pattern it can be seen that each power of 2 comes immediately after a binary number which has a full set of bits in its representation ([Run this example again](#bits)), so by using the bitwise _&_ to compare if the given input equals 0 when compared to itself minus 1, we can determine if the number is a power of 2. This pattern will only work for powers of 2 because all other numbers have, at a minimum, 2 bits in their representation. So, for example:

{% highlight js linenos %}
    a = 2047; //Preceding a power of 2
    b = 2048; //Power of 2

    a.toString(2) = 11111111111
    b.toString(2) = 100000000000

    a & b = 0
{% endhighlight %}

Using the _&_ when comparing these two values will always return 0 because a power of 2 will never have any bits in common with its preceding integer, thus always evaluating to 0.

I hope this explanation helps you better understand how and why powers of 2 are significant. I'd love to hear anyone's thoughts, so please post them in the comments.
