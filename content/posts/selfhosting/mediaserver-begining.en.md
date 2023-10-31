---
title: "From &ldquo;I'll just try out Jellyfin&rdquo; to &ldquo;I'll build my own media server&rdquo;"
date: 2023-10-31T13:41:07+01:00
draft: false
toc: true
tocNum: false
tags: 
    - Selfhosted
    - Mediaserver
    - Proxmox
    - Jellyfin
---

## The Backstory

Actually, I've always thought, why do I need my own media server?
Everything I want to watch can be streamed on Netflix, Disney, Paramount or whereever. 

However, every now and then I came across films that I wanted to watch that were not streamed anywhere.
Especially older films are usually not available. Then I just bought the BluRay. So what's the point of a media server?

I regularly listen to the [Selfhosted Podcast][1] and they talk about [Jellyfin][2] in almost every episode.
Jellyfin is software that can bring self-hosted movie files to almost any device in a streaming app.
At first you don't need a server for it, you can just install it under Windows and start right away. So I thought I'd give it a try.

Of course, you need media files that Jellyfin can play. In my memory, ripping BluRay was always witchcraft and mighty complicated.

Spoiler: Of course, it isn't any more.

With [MakeMKV][3] there is a simple tool to make a backup copy of your purchased BluRays and DVDs. 
You just have to make sure that the drive is certified for UHD if you want to copy 4K films.

All in all, the first test setup was extremely easy to set up!
If you stick to the suggested naming from the Jellyfin documentation, you don't really have to do anything other than put the files in a folder,
that has been made known to Jellyfin. The software then fetches metadata, cover images, etc. from the internet and presents everything in a convenient app.

## The Rabbithole

After moving some films to the hard drive, I quickly realised that I needed more memory. Much more memory!

I've been thinking about replacing my Synology NAS for a while now. 
It still serves its purpose as network storage for files, but is not powerful enough to do more with it.

So back to the beginning: do you need a media server? No! Do I want to build one anyway? Yes!

And that was the beginning of the confusing part of the research. Because there are thousands of articles on the Internet on the subject of self-hosting.
My first port of call was the site [Perfect Media Server][4], which I knew from the [Selfhosted Podcast][1] already mentioned.
Unfortunately, this page is also a bit outdated in some places, so I have spent many hours on various pages and forums.

## What's next?

In the meantime, I have ordered hardware to build a server and have settled on a basic software stack.
I have chosen Proxmox as the basic operating system because I believe it is the best way to expand the server.
I will install Jellyfin as an LXC container and combine the hard drives with MergerFS.

I will try to record as much as possible here in the blog, also to help myself remember.

---

Translated with www.DeepL.com/Translator (free version)

[1]: http://selfhosted.show "Selfhosted Podcast"
[2]: http://jellyfin.org "Jellyfin Homepage"
[3]: https://www.makemkv.com/ "MakeMKV Homepage"
[4]: https://perfectmediaserver.com/ "Perfect Media Server Homepage"