# Finding Orphan Commits (git)

Watch [on YouTube](https://www.youtube.com/watch?v=OEioHTvFlnE) or below:

<p><video controls style="width:100%;">
  <source src="../videos/finding-orphan-commits.mp4" type="video/mp4">
</video></p>

## Description

Links are kept outside of my YouTube descriptions after [YouTube threatened me over linking to the New York Times](../YouTube-threat.md):  
1. My Blog: [Learning in Progress: Equality Has Many Definitions](https://blog.tangentfox.com/equality-has-many-definitions/)
2. YouTube: [Git Will Finally Make Sense After This](https://www.youtube.com/watch?v=Ala6PHlYjmw) by [LearnThatStack](https://www.youtube.com/@LearnThatStack)
3. Help me financially: <a href="https://paypal.me/guard13007">PayPal.me/Guard13007</a>

For information on how to use git: [https://www.youtube.com/watch?v=WumOmMA6hDI](https://www.youtube.com/watch?v=WumOmMA6hDI)  
Or, make chili: [https://www.youtube.com/watch?v=4v2gmNWGHm0](https://www.youtube.com/watch?v=4v2gmNWGHm0)

Blog: [https://blog.tangentfox.com/](https://blog.tangentfox.com/)  
My Patreon (probably doesn't work): [https://www.patreon.com/guard13007](https://www.patreon.com/guard13007)

## Script

A little over a year ago, I published a blog post about equality and the history of trying to achieve it. That blog post was based on notes I took almost two years ago, of which I lost several individual notes.

Today, YouTube recommended this very short video to me explaining how git works. I thought I understood it pretty well already, but this helped me realize I was mistaken, and what to do about those lost notes.

Note that orphaned commits are removed periodically from a repository, so this kind of recovery is only possible recently after a mistake was made, or if you've kept an untouched copy of the repo around like I have.

First, we need a list of commits to check.

In my case, there are hundreds of these, so I copy-pasted the output into an editor and formatted it into a list of hashes in Lua.

I started manually checking all of these by checking them out, (meanwhile forgetting that I made an ordered list and not a key-value hashtable, making these counting steps and using `pairs` completely pointless).

In this case, I know the file name of where the changes were lost, so I should've made a list of files changed within each of these commits. So I did that.

Then I searched through every commit to find only the ones that touched the notes on Equality.

For each of those, I checked out the commit so I could look for the missing data.

Unfortunately, as I've long suspected, the issue was with how I backed up data through git to begin with, and the changes were never committed before being overwritten. But hey, hopefully this can help someone else in a similar situation in the future.

Also, I am mortified to announce that just like two years ago, I am again in danger of becoming homeless due to the economy being used as an excuse by companies to fire people. If you can afford to help me out, I would appreciate it.
