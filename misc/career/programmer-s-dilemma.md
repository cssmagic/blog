# [译] 程序员的困境

> * Original: [Programmer's Dilemma - I.M.H.O. - Medium](https://medium.com/i-m-h-o/231d7499a75)
> * Translated by: [cssmagic](https://github.com/cssmagic)

![Programmer's Dilemma](https://f.cloud.github.com/assets/1231359/984558/21a13e74-08a2-11e3-8d1a-775a371e71bc.jpg)

Recently I interviewed tens of candidates for a kernel programmer's position. These candidates are from big, good companies, which are famous for chips or embedded OS/systems. Many of them claimed they have at least 10 years on-job experience on kernel. Their resumes look fairly shiny -- all kinds of related projects, buzz words and awards...

最近我为一个内核程序员的职位面试了十几个候选人。这些候选人都来自一些不错的大公司，这些公司在芯片或嵌入式操作系统领域十分有名。这些候选人大多声称自己在内核方面有着十年的在职工作经验。他们的简历看起来非常耀眼——各种相关的项目、术语和奖项……

But most of them cannot answer a really basic question: **When we call the standard malloc function, what happens in kernel?**

但他们几乎无人能够回答一个非常基础的问题： **当我们调用标准的 malloc 函数时，内核中会发生什么？**。

Don't be astonished. When I ask one of the candidate to write a simple LRU cache framework based on glib hash functions, he firstly claimed he had never used glib -- that's what I expected -- I showed the glib hash api page and explained the APIs to him in detail, then after almost an hour he wrote only a few lines of messy code.

先别吃惊。当我要求其中一位候选人基于 glib hash 函数写一个简单的 LRU 缓存框架时，他先是表示他从来没有用过 glib——这正是我所期望的——于是我帮他打开了 glib hash API 的页面，并向他详细讲解了这些 API；然后大约一个小时以后，他只写出几行凌乱的代码。

I don't know if the situation is similar in other countries, but in China, or more specifically, in Beijing, this is reality. "Senior" programmers who worked for big, famous foreign companies for years cannot justify themselves in simple, fundamental problems.

我不知道其它国家是否也有类似的情况，但在中国，或者更精确一些，在北京，这就是现状。那些在不错的大公司里工作了多年的“资深”程序员们无法在一些简单的、基本的问题上证明自己。

***

**Why did this happen?**

**这到底是怎么回事？**

The more I think about it, the more I believe it is caused not only by themselves but also by the companies they worked for. These companies usually provide stable stack of code, which has no significant changes for years. The technologies around the code wraps up people's skills, so that they just need to follow the existing path, rather than to be creative. If you happened to work for such kind of code for a long period and did not reach to the outer world a lot, one day you will find yourself to be in a pathetic position -- they called you "**EXPERT**" inside the team or company, yet you cannot find an equally good job in the market unfortunately.

当我在这个问题上思索得越多，我就更加相信，这不仅有他们自身的原因，同时也归咎于他们所供职的这些公司。这些公司通常提供了一个稳定的代码堆，往往几年都不会有大更新。这些代码的专有技术把人们的技能框进一个定式，以致于他们只需要遵循现有的路径，而不需要发挥创意。如果你碰巧为这类代码工作，而且与世隔绝了很长一段时间，那么有一天你会发现你自己已经陷入一个可悲的位置——他们在团队或公司内称呼你为“**专家**”，但不幸的是，你无法在市场上找到一份同等待遇的工作。

This is so called "**Expert Trap**". From day to day, we programmers dreamed of being an expert inside the team/company; however, when that day really comes we trapped ourselves. The more we dig into existing code, the deeper we trapped into it. We gradually lose our ability to write complete projects from scratch, because the existing code is so stable (so big/so profitable). What's the worse, if our major work is just to maintain the existing code with little feature development, after a while, no matter how much code we've read and studies, we will find we cannot write code -- even if the problem is as simple as a graduate school assignment. This is the programmer's dilemma: **we make our living by coding, but the big companies who fed us tend to destroy our ability to make a living.**

这就叫作“**专家陷阱**”。日复一日，程序员们都渴望在团队或公司内成为一名专家；但是，当那一天真正到来时，我们却早已作茧自缚。我们在既有代码中钻得越深，我们自己就陷得越深。既有代码是如此稳定（如此宠大、如此好用），让我们渐渐地失去了从无到有独立编写完整项目的能力。更糟糕的是，如果我们的主要工作就是维护这些既有代码、很少开发新功能，那么过不了多久，无论研读了多少代码，我们都会发现自己不会写代码了——哪怕是一个像毕业大作业那样简单的任务。这就是程序员的困境：**我们以编码为生，但那些养活我们的大公司却在无形中磨灭了我们的生存技能。**

***

**How to get away from this dilemma?**

**如何打破这种困境？**

For personal --

对于个人：

First of all, **Do your own personal projects**. You need to "sharpen your saw" continuously. If the job itself cannot help you do so, pick up the problems you want to concur and conquer it in your personal time. By doing so, most likely you will learn new things. If you publish your personal projects, say in github, you may get chances to know people who may pull you away from your existing position.

首先，**打造你自己的私人项目**。你需要不断地打磨自己的技艺。如果工作本身并不能帮助你做到这一点，就捡起那些你感兴趣的问题，然后用你的私人时间去攻克它。通过这个方法，你应该会学到新东西。如果把你的私人项目发布出去，比如在 GitHub 上，你说不定会认识一些人，帮助你大踏步地向前迈进。

**Do not stay in a same team for more than two years**. Force yourself to move around, even if in the same organization, same company, you will face new challenges and new technologies. Try to do job interviews every 18 months. You don't need to change your job, but you can see what does the market require and how you fit into it.

**不要在一个团队中停留超过两年**。强迫你自己四处转转，哪怕在是同一家公司内，你会面对新的挑战和新的技术。试着每隔 18 个月就出去面试工作。你并不需要真的换工作，但是这能让你看到真实的市场需求，以及怎样与时俱进。

For team/company --

对于团队和公司：

**Give pressures and challenges to the employees.** Rotate the jobs, let the "experts" have chance to broaden their skills. Start new projects, feed the warriors with battles.

**给予员工压力和挑战**。实行轮岗制度，让“专家”们有机会拓展他们的技能。启动新项目，用战役来磨炼你的勇士。

**Hold hackathon periodically.** This will help to build a culture that embrace innovation and creation. People will be motivated by their peers -- "gee, that bustard can write such a beautiful framework for 24 hours, I gotta work hard".

**周期性地举办黑客马拉松活动**。这有助于营造一种崇尚创新和创意的企业文化，人们会受到同伴的激励——“擦，这个混蛋居然可以在 24 小时内写出这么漂亮的框架，我也得加把劲儿了！”

***

### 相关链接

* [题图来源](http://www.flickr.com/photos/thomasthomas/258931782/)
* [Hacker News 上的相关评论](https://news.ycombinator.com/item?id=6221117)

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/23)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
