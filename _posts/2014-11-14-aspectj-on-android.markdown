---
layout: post
title:  "AspectJ on Android"
date:   2014-11-14 15:18:00
categories: Java Android AspectJ
---

With the beginning of November, I made a [presentation][presentation-slideshare] on [GDG Istanbul][gdg-presentation] about AspectJ Implementation on Android.

Actually I mostly used AspectJ on enterprise applications just to manage database transactions, security control and profiling. But on client side like android, is it really worth to implement it? So I have created a sample android project. You can download from [github][github-aspectj-android-example]

In the project there are 5 aspect classes and each of them has specific feature. So core project looks very simple and more readable. You could read Profiling Aspect below.

{% highlight java %}
package android.mobile.peakgames.net.aspectjandroid.aspect;

import android.util.Log;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;

@Aspect
public class ProfilingAspect {
    private static final String TAG = ProfilingAspect.class.getName();
    private static final long MAX_ELAPSED_TIME = 1000;

    @Pointcut("execution(String android.mobile.peakgames.net.aspectjandroid.AspectActivity.doHttpCall(..))")
    public void doHttpCallEntryPoint() {
    }

    @Pointcut("execution(* android.mobile.peakgames.net.aspectjandroid.AspectActivity.fetchImage(..))")
    public void loadImageEntryPoint() {
    }

    @Around("doHttpCallEntryPoint() || loadImageEntryPoint()")
    public Object doHttpCallMethod(ProceedingJoinPoint joinPoint) throws Throwable {
        Object returnValue = null;

        long beginTime = System.currentTimeMillis();

        returnValue = joinPoint.proceed();

        long endTime = System.currentTimeMillis();
        long elapsedTime = (endTime - beginTime);

        Log.d(TAG, joinPoint.getSignature().getName() + " elapsed " + elapsedTime + " ms");
        if (MAX_ELAPSED_TIME < elapsedTime) {
            Log.e(TAG, joinPoint.getSignature() + " exceeded MAX_ELAPSED_TIME, the process is taking too much time");
        }

        return returnValue;
    }
}
{% endhighlight %}

You can also find more information on [Fernando's blog][fernando-blog]

[github-aspectj-android-example]: https://github.com/firstthumb/AspectJ-Android-Example
[presentation-slideshare]: http://www.slideshare.net/firstthumb/aspectj-android-with-example
[gdg-presentation]: http://www.gdgistanbul.com/2014/11/kasmda-etkinlik-baskadr.html
[fernando-blog]: http://fernandocejas.com/2014/08/03/aspect-oriented-programming-in-android
