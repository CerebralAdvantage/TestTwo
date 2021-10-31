#TestTwo
Incredible things

Today's project: find the closest two points in a 2-dimensional field
of random points.

To be honest, I've done quite a bit of thinking about this
since I stumbled upon it 3 days ago.  I've created an algorithm
based on the following two notions:

(this is not new) first, sort the entire list of points by
their x-axis values.

(after much other creativity, I realized the following)
Only the first big handful of point comparisons will be "difficult."
After that, you will have a pretty small "smallest pair."
That "smallest pair" can be used to very quickly speed through
the rest of the list.  Here's how (this is basically THE algorithm):

Because the points are sorted by their x values, you can make one
claim (even though you probably want to make a different claim!)

THE GOLDEN CLAIM:
Starting at any given point and proceeding to the next points in
the list, the distance beween the X-values of the first chosen
point and each successive point will only get larger.

You cannot make any other claim (other than that the points are
sorted in order of their x-axis values.)  This claim is important,
though, when comparing distances.  We will see THE GOLDEN CLAIM
again.

So let's say we've done the hard part and our smallest distance
is 3.5.  Now we're at array number 128, and the array holds the
following values:
```
128 9   27
129 9   12
130 10  29
131 12  3
132 13  27
```
so we look at the pair at location 128 (9,27)
we ask two questions, regarding each successive point
Question 1: is the x-distance greater than the current smallest value?
Question 2: is the y-distance greater than the current smallest value?

The responses are important.
If the answer to Question 1 is "yes", we are sone with 128, entirely
If the answer to Question 2 is "yes", we don't need to calculate the distance.

If the answer to both questions is "no", we calculate the full distance
sqrt((x2-x1)^2 + (y2-y1)^2) = distance
That's a lot of math, but as you can see, we do it rarely.
```
128 9   27
129 9   12
130 10  29
131 12  3
132 13  27
```
For instance, consider the points at 128,129.  Regarding Question 1:
The difference between x values (9-9, or 0) is not greater than 3.5,
so we consider Question 2: The difference between y values (27-12 or 15)
IS greater than 3.5.  So we do not need to use the distance formula.
We have performed 2 subtractions and 2 comparisons and we can move on.

Now we compare 128 with 130
Question 1: 10-9 is 1, and that IS NOT greater than 3.5
Question 2: 29-27 is 2, and that IS NOT greater than 3.5
Now we need to calculate the precise difference, and compare it
to the current smallest distance (1*1)+(2*2) = 5. sqrt(5) is 2.236
2.236 is less than 3.5 so we have a new smallest distance.

Question: are we done?
Not with 128.  We have to compare until the X-value (which, recall,
we can rely on always getting larger) alone is larger than the current
smallest distance.

So now we compare 128 and 131
Question 1: 12-9 is 3, and that IS greater than 2.236 (the current smallest)
So we stop here, with 128, as we are done.

so we compare 129 and 130
Question 1: 10-9 is 1, and that IS NOT greater than 2.236
Question 2: 29-12 is 17, and that IS greater than 2.236

so we can move to compare 129 and 131, without having to use the distance
formula (only 2 subtractions and 2 compares)

So now we compare 129 and 131
Question 1: 12-9 is 3, and that IS greater than 2.236
So we stop here, with 129, as we are done.

Moving on to start comparing 130 with other points...
The thing to realize here is that we are doing EVERY REQUIRED TEST.
We only test a point with points that follow it.
Why? Because all the points that precede it have already been tested
with it.

We only use the distance formula if both of the default tests fail.
Why? Because the distance between two points can only be smaller
that a comparitive distance if BOTH of the discrete X and Y distances
are smaller.  If the smallest distance is 1.7, and the distance between
the two Y points is 205, there's no calculation that will make 205
smaller than 1.7, so WHATEVER the X value is, the point is NOT closer
than 1.7! So there's no need to calculate the actual distance.

So we rarely go through the full distance formula, and when the distance
gets very small, Question 1 will usually fail.

What does that mean?
That means that you can eliminate each point with ONE test: ONE
subtraction and ONE comparison.

If the current smallest distance is 0.00025
and the next five points look like this
```
3128 9   27
3129 10  12
3130 11  29
3131 12  3
3132 13  27
```
compare:
```
3128 and 3129 10-9 = 1.  NOT smaller than 0.00025  NEXT
3129 and 3130 11-10 = 1.  NOT smaller than 0.00025  NEXT
3130 and 3131 12-11 = 1.  NOT smaller than 0.00025  NEXT
3131 and 3132 13-12 = 1.  NOT smaller than 0.00025  NEXT...
```
You see what is happening?  We don't compare the point at 3128
with a whole bunch of points.  We compare it with one point,
the following point.  The X-value difference is larger than
the smallest distance, so we are done comparing other points
with 3128, regardless of how tiny the difference in Y-values is.
And because "each successive X-value is guaranteed to be FARTHER
AWAY from 3128" (remember THE GOLDEN CLAIM?), there is no need
to check any other point.

The smaller the smallest distance gets, the faster "searching the
sorted list of points" gets.  It very quickly approaches O(n).

So, O(n logn) to sort the list, then add O(n) for a clean
time complexity of O(n logn)

There is one caveat.
You may naturally encounter a group of points that are clustered
around a point on the x axis, creating a vertical pile near a
given point on the x-axis.  This rarely happens with well-distributed
random values, but can certainly exist.

I call such a pile a "verical oasis" because I really like how it
describes the sudden pile-up of points in the middle of the well-
distributed desert.

It's really not that big of a deal, but it can slow down an otherwise
snappy search algo.  One could check for such a detail by looking ahead
10 or 20 points, and seeing if the x-values group tightly together.

Or not and say we did.

Now all that's left is to write the code!

