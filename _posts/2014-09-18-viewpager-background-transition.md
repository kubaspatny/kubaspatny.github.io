---
layout: post
title: ViewPager with gradual Background Transition
---

Recently I came across one of those tutorial screens in a newly installed app. It would be quite ordinary use of the Android's `ViewPager` if not for a smooth gradual background change while swiping between pages.

I searched the web but couldn't find a nice solution anywhere, so I set off to create a solution of my own.

<!--more-->

### What are we creating

Before we dive into code, let's show off the finished effect (the source and sample apk can be found at the bottom of the article):

<img class="center image" src="/assets/2014/09/18/animation.gif"></img>

As you can see, the background of the `ViewPager` animates gradually, so you can't see the edges of adjacent views.

### The basics

First we have to set up an `Activity` (in my case `FragmentActivity` for compatibility) with a `ViewPager`.

{% highlight java %}

public class MainActivity extends FragmentActivity {

	SectionsPagerAdapter mSectionsPagerAdapter;
	ValueAnimator mColorAnimation;
	ViewPager mViewPager;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		mSectionsPagerAdapter = new SectionsPagerAdapter(getSupportFragmentManager());
		mViewPager = (ViewPager) findViewById(R.id.pager);
		mViewPager.setAdapter(mSectionsPagerAdapter);
	}
}

{% endhighlight %}

`SectionsPagerAdapter` is just a class extending the `FragmentPagerAdapter` which provides the `ViewPager` with placeholder fragments for each page.

### Changing the background

Now that we have set up the foundation for out project, we can move on to actually changing the background of the `ViewPager`.

Changing the color is a matter of one method call:

{% highlight java %}
mViewPager.setBackgroundColor(Color.BLACK);
{% endhighlight %}

The only remaining problems are:

* Determining the right color of the background while swiping if we know the color of *Page A* and the color of *page B*.
* Updating the background color while swiping.

### Calculating the right color

To determine the right color, which is a blend of *Color A* and *Color B*, I decided to use the `ValueAnimator` of `ArgbEvaluator` object.

`ValueAnimator` does all the job of calculating the correct color over time for you. You only have to pass the colors you want to iterate over - in my case 3, because my `SectionsPagerAdapter` returns only 3 pages.

{% highlight java %}
mColorAnimation = ValueAnimator.ofObject(new ArgbEvaluator(), color1, color2, color3);
{% endhighlight %}

To change the background of the `ViewPager` each time the animation updates, we have to set a listener.

{% highlight java %}

mColorAnimation.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {

	@Override
	public void onAnimationUpdate(ValueAnimator animator) {
		mViewPager.setBackgroundColor((Integer)animator.getAnimatedValue());
	}

});

{% endhighlight %}

And at last, we set the duration of our animation:

{% highlight java %}
// (3 - 1) = number of pages minus 1
mColorAnimation.setDuration((3 - 1) * 10000000000l);
{% endhighlight %}

To explain the magic number *10000000000l*, `setDuration()` method only accepts a `long` value, where as we're going to be working with `float` values while calculation the position of pages in the `ViewPager`. To keep the precision, we will multiple the float values by this number, so that for instance number *0.9876543219* will be converted to *9876543219*.


Normally when using `ValueAnimator` you would set the duration and start the animation by calling `ValueAnimator#start()`, but we're only going to use the animator for determining the right color in each state of swiping between pages.

### Determining which page is shown

To calculate the color we need to know what is the `ViewPager` currently showing. To do that we create our listener that implements `ViewPager.OnPageChangeListener`.

{% highlight java %}

private class CustomOnPageChangeListener implements ViewPager.OnPageChangeListener {
	@Override
	public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
		mColorAnimation.setCurrentPlayTime((long)((positionOffset + position)* 10000000000l));
	}
}

{% endhighlight %}

In previous section we set the duration of the animation for 3 pages to *2 * 10000000000*. Method `onPageScrolled()` will recieve value for *position* parameter in a range of [0,3) and for *positionOffset* in [0,1) range. 

An example: if *position* is 0 and *positionOffset* is equal to 0.98, it means that we're swiping to/from second page and 98% of the first page is already hidden.

Therefore, by using the formula *((positionOffset + position) *  10000000000l))* we are moving on a timeline from *0 to 20000000000* (in this example).

The last step already showed in the code above is to call `ValueAnimator#setCurrentPlayTime()` which will set the current position of the animation and will also trigger an animation update which will call our listener that will update our `ViewPager` background.

### Room for improvement

The code above will work, although there's still room for improvement. The main problem is specifying the number of pages in code. To overcome that, I would recommend using `FragmentPagerAdapter`'s method `getCount()` to determine that number. That would be essential especially when you don't know the number of pages beforehand.

### Sample App

If you'd like you can download a sample app to try the effect on your device.

<button class="download-button" onclick="window.location='/assets/ViewPagerBackgroundAnimation.apk';">
	<span class="icon"></span>
	<span class="title">Download sample apk</span>
</button>

Or view the source on [GitHub](https://github.com/kubaspatny/viewpagerbackgroundanimation).

---

**Acknowledgements:** *In the sample app, [PagerSlidingTabStrip](https://github.com/astuetz/PagerSlidingTabStrip) was used for the ViewPager tabs.*












