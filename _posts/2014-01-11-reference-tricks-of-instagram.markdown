---
layout: post
title: Reference&#58; Instagram&#39;s tricks for responsiveness
---

A perfect design for any web application from the developer perspective is all about scalability! ... well, not really.
I would rather say that a perfect design is all about **the user**, and a perfect user experience has multiple variables, including **responsiveness**.

Have you ever noticed how fast your attachments are downloaded from Gmail? In fact, it starts when the user clicks on the download button first time, even before he chooses the destination folder.
Obviously, this feature comes with a cost. The file transfer is started (and very often completed) even before the user has given his ultimate confirmation on the *Save as* button, consuming resources from both client and server.
However, the simple assumption that the user *will* accept the file is sufficient to highly improve his perspective of responsiveness. **Users don't like to wait**, so it is better to have the file read-to-open right after choosing the destination folder.

Instagram implements similar tricks behind its fancy features, and these are well summarized in this post from FastCompany: [The 3 White Lies Behind Instagram's Lightning Speed](http://www.fastcodesign.com/1669788/the-3-white-lies-behind-instagrams-lightning-speed).
Pictures taken from Instagram are uploaded and hidden while the user is fulfilling caption and other options. When the user proceeds, the picture is already there and he is free to navigate somewhere else.

There is always a possibility for the user to give up before his final commit, meaning that preloaded resources could be just wasted.
This trade-off is indeed specific for every application. How often the user gives up in the middle of the process? Is there any step at which the user is more likely to abort the operation? What could be performed in advance while the application is *apparently* waiting for the user's inputs?

Often, statistics about users' behaviour are very helpful as a prediction tool, allied to a good sense about the eventual delays in your software.
So, time to work that out, get a bit more open-minded about resource optimization and make sure that the main part of your application - *the user* - has a smooth and pleasant experience!
