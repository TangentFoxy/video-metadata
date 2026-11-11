# microlog - The best Lua logging library ever made!

Watch [on YouTube](https://www.youtube.com/watch?v=lTwpKIBiPRs) or below:

<p><video controls style="width:100%;">
  <source src="../videos/microlog.mp4" type="video/mp4">
</video></p>

00:00 A brief history of logging  
02:19 What is common today  
03:28 Why microlog is better

View it on GitHub: https://github.com/TangentFoxy/lua-microlog

## Description

Links are kept outside of my YouTube descriptions after [YouTube threatened me over linking to the New York Times](../YouTube-threat.md).

PayPal: [https://paypal.me/Guard13007](https://paypal.me/Guard13007)  
Blog: [https://blog.tangentfox.com/](https://blog.tangentfox.com/)  
My Patreon (probably doesn't work): [https://www.patreon.com/guard13007](https://www.patreon.com/guard13007)

## Script

The script has been broken into sections so that it can also be published as a blog post.

### A brief history of logging
I'm expecting the audience for this video will include people who don't know much about logging, or need a refresher so they can understand my point of view as to why microlog, my Lua logging library, is the best logging library ever made. If you just want to skip to what makes my library unique, there'll be an on-screen timecode now to tell you where to skip to, as well as a chapter in YouTube's interface.

The full script as well as several footnotes and a few relevant sources are linked to from the description, including why multiple slides have asterisks and daggers. I didn't focus too much on that because it's irrelevant to my primary goal here, but it's still interesting and I recommend it if you're curious.

For as long as humans could communicate, some form of recordkeeping existed, but the earliest example I could find where something was hypothesized to be a form of log is the Isahngo bone[^isahngo], from 20,000 years ago. (Older artifacts with tool marks of unknown purpose do exist.)

In programming, the earliest logging (1960's) was done with print statements whenever the programmer felt they were necessary, and programs had to be explicitly connected to input and output devices. Unix changed this by introducing standard input, output, and error (1970's). The idea was for data to flow from input to output, and for any diagnostic data or problems to go to a second output stream to prevent interfering with operations.

It became unmanageable immediately. In the 1980's, syslog was created to audit logs and debug issues. It was highly successful, but unstandardized all the way until 2001. The severity levels I'm showing on-screen are defined by the standard, so I can't even say when they were in-use or how many were in-use.

In 1993 with Windows NT, Microsoft made their own form of logging management. Microsoft *does* love to ignore standards, but in this case, there wasn't one to adhere to. I think it's the worst version, because it relies on a custom binary format instead of text.

Further inconsistencies compounded. The examples shown here are taken straight from the relevant manuals. Notably, Java and .NET haven't changed their logging levels ever[^net-has-2-level-systems] (Java's levels seem *particularly* confusing to me), but Log4j 2 introduced custom log levels. And can I just take a moment to complain about Microsoft's naming and versioning. .NET Framework 2.0 came out in 2005, but the .NET Standard 2.0 somehow wsn't released until 2017 even though .NET Standard is the API that .NET implementations have to adhere to, and .NET is both a generic term for these and its own specific newer implementation of .NET. The announcement for the .NET Standard 2.0 also doesn't say it is supported until .NET Framework 4.6.1 and later, but the manual for log levels says they were supported in .NET Framework 2.0, which is why I said it's from 2005 here, but Microsoft's announcement implies it didn't exist until 2015, because that's when the version where the standard is adhered to was released, which is also still before the standard was released.

(Make sure an on-screen indicator of how long I rant for is included, with "skip autistic ranting -> \[timecode]" above it.)

(Footnotes to on-screen content:)
- \* I am using descriptions instead of their ~~internal names~~ identifiers because they wouldn't fit on the slide otherwise.  
- † Pre-1.0 versions of Log4j existed in 1999.[^log4j-release][^log4j-1-manual]

In the 2010's, there was a push towards structured logging. The idea was to put data in formats easily parsed by machines, such as JSON, as part of the Big Data movement. This didn't change much, although there was recognition of need for better filtering of log messages.

(Footnotes to on-screen content:)
- \* Logstash's documentation doesn't specify levels, and says that it is based on Log4j's log levels.[^logstash-levels]  
- † I'm just looking at its current GitHub wiki and presuming these were always defined this way. Even if this is newer, it's not better.

### What is common today

If you search for standard or common logging levels, you're going to encounter trace, debug, info, warn, error, and fatal. As far as I know, trace and fatal aren't even really used, and no one acknowledges the idea of custom logging levels. There are plenty of services that sell the idea of managing your logs more effectively, but they are band-aid on top of a deeper problem - bad logging.

My problem with the majority of logging libraries is three-fold.

1. First, they are often too verbose,
   and no one should want code that looks like Java.
2. Second, they violate the single-responsibility principle (I'm using that incorrectly, oops): By bundling console logging, application logging, and log management through network-enabled distribution of logs, they are more complex than necessary for most applications. Complexity is not free. Code size gets larger, the possibility of errors and thus attack surface for malicious actors is increased, and they can cause confusion[^confusion-example] while developing.
3. Third, they are largely proscriptive. There are specific log levels to adhere to, which may or may not mesh with your conception of code or what you want to do with it.[^security-example] On a smaller, more personal level, this can also make it hard to quickly diagnose highly specific issues, as you may be flooded with a large number of logs for various things unrelated to what you're trying to debug.

### Why `microlog` is better

For example, recently I had to diagnose a weird issue where extremely rarely, the wrong file would be selected. This is an example of how my logging library works if you don't use it optimally. Every debug message is printed and stored, but the resolved file path is mixed in with other messages. If there weren't custom log levels, it would be difficult to check, or you'd have to use up a log level just for those messages, or you'd have to go through the code commenting out logging, which is usually a bad idea.

Instead, I hide debug messages, and create a new log level just for files.

It's also sometimes useful to display some information grouped separately from where it came from. In this example, debug messages are neither stored nor printed, info messages are printed immediately, and warning messages are only stored initially. When log is called with only a string, all messages stored at that log level are printed.

- Anything can be sent as a log message. Lua's `tostring()` function is used, so the `__tostring` metamethod can be used to customize how tables are printed.
- Log messages are stored in memory, so you can set a per log level limit how many are kept to prevent that from growing out of hand.
- Alternately, you can periodically clear logs or do anything else with them by calling `log()` with a function.
- You can also call `log()` with `false` to disable all logging and clear stored messages.

And finally, you can check which logging levels are enabled so that you don't run logging specific code when it isn't necessary.

ANSI color support should be pretty easy to add as an optional feature, but it's not that important to me, so I haven't done it.

Since this is the best logging library ever, it should make it easier for you to switch to it from another, but it currently doesn't. They all tend to use a different structure for calling functions, which makes it a little complex to implement, but I think I can do it. I am only using this so far though, so I haven't had a need to do that yet.

While I think this is the best logging library, I can admit that being able to automatically push logs elsewhere is useful, and other libraries do that. I think I can make this library have an option to allow you to configure another library to handle your log messages after they've been generated here. This would allow you to get their benefits while keeping the verbosity of your logging lower with microlog. Again, because I'm not using any of these right now, it's low on my priority list.

Thanks for watching, I hope you found it informative or at least entertaining.

Hi, I've had no income for over a year and am facing the threat of homelessness. If you like what I make, or just want to support a person, you know, not dying, anything you can spare would help a lot. Right now my biggest concern is groceries. I have had some help and assurances that my home will not be taken away for about two months, but I cannot know what will happen then, and in the meantime, I need to eat.

---

### Related but undiscussed topics
- Logging combined with or versus metrics and tracing. Logging sort of operates at a middleground between high-level metrics and low-level tracing. Depending on your needs, the other options may complement or replace logging.
- I mentioned centralized logging indirectly, but it's not part of my objective / area of responsibility. I mentioned platforms designed for centralized logging a lot here, but didn't really talk about the point of having that kind of.. single point of ~~failure~~ management and filtering.
- Filtering / Verbosity of logging. I'm focused very much on the immediate working utility of logs, and the ability to quickly change what is being logged or viewed. A more complex project might need to think harder about how to decide what to log.. though that's part of my point in making log levels a dynamic thing instead of only a single-axis slider. By making each log level toggleable, there is less concern about a logging level being too aggressive or too minimal. You can choose exactly what kinds of things to log for what purposes, and leave which things get stored or not for later desicion-making.

#### Further reading
Despite his use of Go, I do agree with [[Thom Shutt]]'s description of how developers use logging levels[^minimal-logging]. They're more placeholders for things the developer doesn't want to think about than useful messages in most codebases. I strongly disagree with his solution, because it eliminates the usefulness of logging.

[[Jeff Dwyer]]'s response[^levels-useless] is *very* amusing to me, because it feels like missing the root of the problem for the symptoms. Instead of focusing on whether or not it's easy to change the slider of what logging messages you deal with, control should be more granular. Log levels should be meaningful names rather than points on a scale. That's basically my goal here.

[^Isahngo]: On Wikipedia, the [Isahngo bone](https://en.wikipedia.org/wiki/Ishango_bone#Interpretations) is listed as the oldest artifact with markings that could be a form of recordkeeping. However, an older bone known as the "[Wolf bone](https://en.wikipedia.org/wiki/Doln%C3%AD_V%C4%9Bstonice_(archaeological_site)#Tally_stick)" is referenced as possibly being an older example, and the [Lebombo bone](https://en.wikipedia.org/wiki/Lebombo_bone) is referenced as an *even older* example. I did not look into the Wolf bone, but the sources Wikipedia cites as speculating over meaning in the marks do not mention the bone. I've decided to leave it at referencing the Isahngo bone because there's a picture to go along with it, and archaeological detail is not the point of this piece.
[^Log4j-1-manual]: The [Log4j v1 manual](https://logging.apache.org/log4j/1.x/manual.html) was actually kind of hard to find.
[^Log4j-release]: The [pre-1.0 versions of Apache Log4j were actually released in 1999](https://logging.apache.org/log4j/1.x/changes-report.html), but 1.0 was released in 2001. Which is more correct? I don't care right now.
[^NET-has-2-level-systems]: .NET has [System.Diagnostics.SourceLevels](https://learn.microsoft.com/en-us/dotnet/api/system.diagnostics.sourcelevels?view=net-10.0) from 2005 and [Microsoft.Extensions.Logging.LogLevel](https://learn.microsoft.com/en-us/dotnet/api/microsoft.extensions.logging.loglevel?view=net-11.0-pp) from 2016.
[^logstash-levels]: Logstash's documentation doesn't specify log levels, and states that it uses Log4j's levels. The [earliest version](https://github.com/elastic/logstash/releases/tag/v1.0.0)'s documentation doesn't mention levels anywhere [except in command-line verbosity of logging](https://github.com/elastic/logstash/blob/07404dbc06aca4fce3713633157d3abf55e5aa73/docs/flags.md), which I find kind of ironic. The very issues it should be fixing are also badly addressed by this CLI parameter.
[^confusion-example]: An example of the confusion caused by proscriptive logging levels: [When to use the different log levels](https://stackoverflow.com/questions/2031163/when-to-use-the-different-log-levels) (Stack Overflow)
[^security-example]: As an example, [here's a guide I stumbled across](https://betterstack.com/community/guides/logging/log-levels-explained/#creating-and-using-custom-levels) where a security custom level is used to highlight security-related events, something none of the common or standard logging levels has a place for!
[^minimal-logging]: [5 (Opinionated) Steps to Minimalist Logging in Go](https://thomshutt.com/opinionated-logging-in-go.html) by [[Thom Shutt]]
[^levels-useless]: [Log Levels Are Useless, Or Are They…](https://quonfig.com/blog/log-levels-debate) by [[Jeff Dwyer]]
