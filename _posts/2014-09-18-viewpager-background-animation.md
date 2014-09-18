---
layout: post
title: ViewPager Background Animation
---

Recently I came across one of those tutorial screens after installing a new app. It would be quite ordinary use of the Android's `ViewPager` if not for a gradual background change while swiping between pages, which I found really beatiful.

I searched the web but couldn't find a nice solution anywhere, so I set off to create a solution of my own.

### What are we creating

Before we dive into code, let's show off the finished effect:

<img class="center image" src="/assets/2014/09/18/animation.gif"></img>

As you can see, the background of the `ViewPager` animates gradually, so you can't see the edges of adjacent views.

### The basics

First you have to set up an `Activity` (in my case `FragmentActivity` for compatibility) with the `ViewPager`.

{% highlight java %}

public class MainActivity extends FragmentActivity {
SectionsPagerAdapter mSectionsPagerAdapter;
private ValueAnimator mColorAnimation;
ViewPager mViewPager;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);
mSectionsPagerAdapter = new SectionsPagerAdapter(getSupportFragmentManager());
mViewPager = (ViewPager) findViewById(R.id.pager);
mViewPager.setAdapter(mSectionsPagerAdapter);
}

{% endhighlight %}











