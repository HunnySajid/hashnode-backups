## Web Performance - Reducing Paint flashing on Amazon.com 

 That's a mouthful title alright !!

![so mouthful](https://i.gifer.com/Btsm.gif)

But this was a challenge which was organized at the end of a [Web Performance Bootcamp](https://www.meetup.com/JavaScriptMeetup/events/276621594/) held by team [devkode](https://devkode.io/) (founder - [Sunny Puri](https://www.linkedin.com/in/sunnypuri/)) on **JavaScript Meetup** (co-organizer [NC Patro](https://www.linkedin.com/in/ncpatro/)) platform. If you're interested in knowing my experience of the same, then here is the [link](https://www.linkedin.com/posts/lakshya-thakur_webdevelopment-bootcamps-teamdevkode-activity-6788698522888806400-coSs) but we will focus more on the challenge here. 

**Problem Statement :-**

> When you open and close the [amazon.com](https://www.amazon.com/) sidebar, [paint flashing](https://developers.google.com/web/fundamentals/performance/rendering/simplify-paint-complexity-and-reduce-paint-areas) occurs which can be eliminated via CSS modification. So you're required to identify those changes and submit the same. 

If you're unfamiliar with terms like **layout**, **paint** and **composition**, I would recommend you to go through [CRP](https://developers.google.com/web/fundamentals/performance/critical-rendering-path) link to get a know how so you can better relate with the solution ahead.

Alright, so how to first visualize this **paint flashing** stuff ?

To start with, let's go to amazon.com and there, we will take help of our age old friend **DevTools** :-

![devtools pic](https://i1.wp.com/css-tricks.com/wp-content/uploads/2018/02/chrome-devtools.jpg?fit=1200%2C600&ssl=1)

After opening the **DevTools**, lets do <kbd>Ctrl</kbd>/<kbd>Command</kbd> + <kbd>Shift</kbd> + <kbd>P</kbd> to open the **Command Menu**. Search for **Show paint flashing rectangles** and select that option. 

Now if you try to interact with the left hamburger icon used to open/close the sidebar, you will see **green** flashing rectangles on the screen indicating **paint** pipeline is triggered. And it is this **paint**, we aim to eliminate. 


![Amazon_default_paint_flashing.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1628180955555/Il_GDPNEt.gif)

Alright so now we visualize the problem and before I go on to explain the solution, you can also try arriving at the solution. Also, I want you to know that it took me **1.5** hours to identify the **CSS modifications**. You might take less or more time since that's all relative. 

Did you try it ?

Yes/No ? (Drop in the comment section)

![Okay, here we go](https://telltaletv.com/wp-content/uploads/2018/05/the-good-place-okay-here-we-go-gif.gif)

I identified two main elements that were responsible for this **paint flashing** :-

* the CSS class `lock-position` on `body` tag
* the sidebar element with **id** of `hmenu-container`

### body.lock-position

Consider the following CSS when *sidebar is open* :-
```
body.lock-position {
    overflow: hidden;
}
```

This is used by Amazon to prevent the scroll of page content when the sidebar is open. Now if we go to [css triggers](https://csstriggers.com/overflow-x) to see the **layout, paint and composition** cost of the same, here is what we will find :-


![overflow-x-css-triggers](https://cdn.hashnode.com/res/hashnode/image/upload/v1628178843346/ZoMFr-xMo.png)


![overflow-y-css-triggers](https://cdn.hashnode.com/res/hashnode/image/upload/v1628178854157/0AjCe9pNtn.png)

Both `overflow-x` and `overflow-y` together combined are equivalent to `overflow` CSS property and as we can see a change in this triggers the **layout** pipeline making it an expensive operation. 

If we remove, the `overflow:hidden` from `body.lock-position`, you will notice that while closing the **sidebar**, there is no more **paint flashing** but while opening there still is (Obviously, now the when the sidebar is open, a user can scroll through page content but that was not a constraint). We will eliminate the leftover **paint flashing** next. 

The result till now :-

![Amazon_body_fix_paint_flashing.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1628181324922/M46w-rk3V.gif)

### #hmenu-container

Consider the following CSS when *sidebar is closed* :-

```
#hmenu-container {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    visibility: hidden;
    z-index: 100000;
}
```

Consider the following CSS then *sidebar is open*:-

```
#hmenu-container.hmenu-visible {
    visibility: visible;
}
```

You will notice that `visibility` CSS property initially takes a value of `hidden` and on click of hamburger icon changes to `visible`. Now let's go to [css triggers](https://csstriggers.com/visibility) to know what part of **layout, paint and composition** does `visibility` trigger :-


![visibility-css-trigger](https://cdn.hashnode.com/res/hashnode/image/upload/v1628178282064/XOli1xIEN.png)

Now in the bootcamp, there was a certain section where it was talked about how `transform` CSS property is used to perform UI changes on a separate layer via the **compositor thread** and `will-change` CSS property let's the browser know beforehand how an element is expected to change so it can promote the element to a separate layer as an optimization. 

Let's look at how `transform` works from [css triggers](https://csstriggers.com/transform):-

![transform-css-triggers](https://cdn.hashnode.com/res/hashnode/image/upload/v1628185346820/g0QyX-x1h_.png)

Cool lets use the above for the final piece of our solution :-

```
#hmenu-container {
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    will-change:transform;
    transform: translateX(-100%);
    z-index: 100000;
}

#hmenu-container.hmenu-visible {
    transform: translateX(0);
}
```

To mimic **sidebar being closed**, we can add `transform: translateX(-100%);` to `#hmenu-container` which will shift the whole container to left by 100% of it's width. 

And to mimic **sidebar being open**, we can add `transform: translateX(0);` to `#hmenu-container.hmenu-visible` which will show the whole container in current viewport. 

Here is the final result :-

![Amazon_final_result.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1628181814924/pUhagrVlv.gif)


**Note:- The above will work even without `will-change:transform` since that's an additional optimization of promoting `#hmenu-container` to another layer before the sidebar is made to open.**

And that's it for the solution. This challenge was fun because it integrated what we learned at the bootcamp. Also I think the other participants who completed the challenge might have had other approaches. So if you figure out some other way, do share the same in the comments 👇.


P.S. - Btw if you're wondering about those slick looking keyboard keys support in markdown, check out [this](https://townhall.hashnode.com/hashnodes-editor-now-supports-8-new-html-tags-kbd-abbr-sub-sup-and-more). Also the **cover image** is generated using [this](https://slickr.vercel.app/app) awesome web-app by @[Savio Martin](@saviomartin).

## Thank you for your time :D