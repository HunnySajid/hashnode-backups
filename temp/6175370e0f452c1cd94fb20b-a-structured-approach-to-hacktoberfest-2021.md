## A structured approach to Hacktoberfest 2021 

 **Opensource X October** - an ongoing theme to make people familiar with OSS across the world. 
I had my first [experience](https://blog.lakbychance.com/tee-or-tree-fest-hacktober-dilemma-1) last year. It was very exciting and explorative in nature. 

This year 2021, I wasn't sure I would be doing it again. But then I came across

%[https://github.com/Progyan1997/Operational-Transformation]

and started digging into what all it's about. 

Amazingly, I approached the things in a more structured fashion than the last year and I would like to take you through the first 4 contributions that qualified me for [Hacktoberfest 2021](https://hacktoberfest.digitalocean.com/profile) .

---

### 1. Numero Uno

**Operational Transformation ?**....sounds like a very sci-fi concept...doesn't it ?
![Autobots](https://i.pinimg.com/originals/72/ee/9a/72ee9aa8b20fca1901597e6eb812761c.gif)

A quick google search and I landed on the classic [Wikipedia](https://en.wikipedia.org/wiki/Operational_transformation) page describing what it was. 


> Operational transformation (OT) is a technology for supporting a range of collaboration functionalities in advanced collaborative software systems. OT was originally invented for consistency maintenance and concurrency control in collaborative editing of plain text documents. Its capabilities have been extended and its applications expanded to include group undo, locking, conflict resolution, operation notification and compression, group-awareness, HTML/XML and tree-structured document editing, collaborative office productivity tools, application-sharing, and collaborative computer-aided media design tools.[1] In 2009 OT was adopted as a core technique behind the collaboration features in Apache Wave and Google Docs.

Well, the above paragraph and a bit about it in the same article gave me a high level picture of the scope of this repository. It's **collaborative editing** and not limited to one **Database** or **Editor** implementation. Rather, this aims to allow multiple combinations of the either of **Database**/**Editor** as a part of **otjs** package. So you can have a **firebase** database with **monaco** editor or a **firebase** database with **ace** editor and this is just the beginning of possibilities. 

As someone who hasn't really tinkered with either of used **Database** ,**Editor** or **Operational Transformation** tech jargons used here, this was very new and kind of exciting. 

I randomly opened a couple of code files to see what's happening to only be clueless. 

![clueless](https://media1.giphy.com/media/1X7lCRp8iE0yrdZvwd/giphy.gif)

So I took another approach here. This approach has been talked a lot by folks all over dev communities when looking forward to contribute to a new OSS. It's reading the **docs** and figuring out the stuff. So naturally the **README** was the entry point for this which got me familiar with opening issues here (via discussions and not directly for this repo) and lead me to the **CONTRIBUTING** markdown file with the guidelines to make a contribution. 

This is where I found my first way to contribute. Improving the existing **docs**, **CONTRIBUTING** markdown to be specific with some linguistic nuances and correct links. Does this make me understand the code better in any way ? 

![not at all](https://thumbs.gfycat.com/FarBogusAfricanwilddog-max-1mb.gif)

So why bother ? 

It's the ice-breaker to start working on this repository. I ended up opening the following for this :-

%[https://github.com/Progyan1997/Operational-Transformation/discussions/63]

The maintainer opened up a new issue

%[https://github.com/Progyan1997/Operational-Transformation/issues/64]

for me to fix and as per the **CONTRIBUTING** markdown guidelines, I made my first PR to this repository which got merged by the maintainer.

![first time](https://img.wattpad.com/f317dfdc1573c0536f71c9308e00c64f1bedf2f9/68747470733a2f2f696d672e776174747061642e636f6d2f73746f72795f70617274732f313034373238323332372f696d616765732f313637306362363333386633386531623636333636393039303636342e676966?s=fit&h=360&w=360&q=80)

The PR 👇

%[https://github.com/Progyan1997/Operational-Transformation/pull/65]

---

### 2. Double Trouble

Well we have officially entered **Hacktoberfest 2021** even though I didn't register until I got my 3 PR's merged I believe. 

![shrug](https://media3.giphy.com/media/e1s8C0YnnfjlRf7mEr/200.gif)

So I made a very small contribution to start with and the next one should at least give me more insight into running some examples using the [packages](https://github.com/Progyan1997/Operational-Transformation/tree/main/packages) in this repository. 

And there was already a provision for examples here

%[https://github.com/Progyan1997/Operational-Transformation/tree/main/examples/collaborative-editors#readme]

But there was no **README** for the same which tells the user how to run this example. And this ultimately turned into another opportunity for a contribution. 

![opportunity](https://c.tenor.com/q4vEXq6P6zoAAAAd/there-is-an-opportunity-here-opportunity.gif)

This is the second discussion which took place 

%[https://github.com/Progyan1997/Operational-Transformation/discussions/66]

The maintainer opened up a new issue 

%[https://github.com/Progyan1997/Operational-Transformation/issues/67]
 
and told me that I can pick it up if I want to. I gave it a try in my local setup and got the example running. 
After this, I created a **README** with instructions and few images that can give a new user enough information to run and validate the working of these examples. There goes my second PR to this repository. 

![second](https://media0.giphy.com/media/UibuCmhMJwCdY1CfWA/giphy.gif)

The PR 👇

%[https://github.com/Progyan1997/Operational-Transformation/pull/71]

---

### 3. Third time's the charm

Till now, I actually didn't pick up any of the existing issues which were already up for grabs since most of them required technical know how. So it was time to try a **small** one to get my hands dirty with code. 

I found the following issue fulfilling the criteria 

%[https://github.com/Progyan1997/Operational-Transformation/issues/60]

I basically had to add support for remote selection of text to be visible in Ace editor. Let's see what this means by example. Suppose two users are contributing to a Doc powered my Ace editor, now, when one of them highlights some text or places a cursor somewhere in the same DOC, the other should know where the cursor is and what's the highlighted/selected text. 

And first I thought to myself that how is this task labelled **small** ? Wouldn't that need more lines of code ? 

![curious](https://giffiles.alphacoders.com/199/199733.gif)

My question got answered when I compared the existing code for **Monaco** with **Ace** and saw that both have almost the same implementation with code for remote cursor/selection present as well. The only thing missing from **Ace** was a bit of **CSS** implementation and that was all that needed to be figured out. The good thing is that an existing implementation helped me **compare** and arrive at the solution quickly. 

I submitted my PR and the maintainer appreciated the catch and fix. 

![3rd contribution](https://y.yarn.co/5093488d-dbdd-47ab-9ab7-8b4e87599df4_text.gif)

The PR 👇

%[https://github.com/Progyan1997/Operational-Transformation/pull/75]

He also asked me if I can give a try to add support for a remote tooltip for **Ace** Editor. I did a quick comparison between **Monaco** and **Ace** implementation again and found that this would require implementing a new Class itself and should be addressed in a separate issue. The maintainer agreed and opened a new issue for the same. 

---

### 4. May the Fourth be with you

%[https://github.com/Progyan1997/Operational-Transformation/issues/76]

The last issue helped me get familiar with a bit of code and resulted in creation of the above issue (labelled **medium**) which needed me to go deeper into **Ace** editor APIs. But I will be honest here, the editor doesn't have good docs (as far as I explored).

![not good](https://i.pinimg.com/originals/60/dc/77/60dc775fa06ab876ce2d45e62766b8d3.gif)

It was now required to implement a tooltip that lets the current user know the username of the remote user when typing. On paper, this sounds not too different from what might have gone into implementing a remote cursor/selection. 

But when I looked at the existing code for tooltip implementation in **Monaco** editor, I saw that it has a concept of **ContentWidget** which allows for such UI widgets to be built and maintained easily and the developer used the same to create a new **CursorWidget** class which takes care of adding, updating and removing the tooltip. Here comes the interesting part that **Ace** itself doesn't have the concept of **widget** in the same sense. 

There are two types of **markers** though which can be added by using `addMarker` or `addDynamicMarker` API. Initially I thought maybe the first one would be enough to help us achieve the tooltip functionality but since it's meant for static markers, which are added and removed each time using `addMarker`/`removeMarker` combo, it didn't fit the tooltip use-case. The second one i.e `addDynamicMarker's` didn't have good docs and I felt less confident in using it until I found the following tooltip implementation in another OSS 

%[https://github.com/convergencelabs/ace-collab-ext/blob/master/src/ts/AceCursorMarker.ts] 

After reading the code and trying to implement the same kind of functionality a couple of times while ensuring to follow the **CursorWidget** template, I finally got around getting the tooltip implementation to work with Ace and proposed adding debouncing as well to the tooltip announcements for a better UX. Figuring out all of this took at least 2 full days and I was happy with the end result.

![Number 4](https://64.media.tumblr.com/d028bf7ab190f50806853f6a08162840/7090a65e5cbe52fd-1f/s500x750/f5d0a0b11cf3897b1ec10904b119ea349d33e05c.gifv)

The PR 👇

%[https://github.com/Progyan1997/Operational-Transformation/pull/81]

---

If you notice the order of issues, you would see that this approach was progressive going from no-code to code contributions within 4 PRs. I know the maintainer from Linkedin and have interacted with him in DMs and comments a couple of times. So when I came across his post that he is looking for contributors to his new repo, I thought let's give it a shot !

While contributing this time, besides the problem solving and technical learning, I got to know about [Semantic Commit Messages](https://gist.github.com/joshbuchea/6f47e86d2510bce28f8e7f42ae84c716), [--fixup](https://fle.github.io/git-tip-keep-your-branch-clean-with-fixup-and-autosquash.html) flag, a gist of Github Discussions and creating issues from the same. 

Besides the above 4 PRs, I contributed 3 more to the same repo with minor test updates, code refactoring etc. Also I came across the following to contribute JS interview questions and submitted 3 PRs for the same. 

%[https://github.com/devkodeio/javascript-interview-questions]

Overall I enjoyed the structured approach this time. 

![smirk](https://i.gifer.com/74Uh.gif)

## Thank you for your time :D




 