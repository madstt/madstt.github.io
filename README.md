Last week I had the opportunity to attend Campus Days 2014 in Copenhagen. A Microsoft hosted conference. In this post I'll give my feedback on the conference and, in particular, the content.

It was my first Campus Days conference, since Campus Days until this year had been focusing more on the DevOps/IT Pro facets. It now turns out that Danish Developer Conference has merged with Campus Days. This makes the conference a much better choice for developers.

Since august I had been in contact with one of the organizers, Technical Evangelist [Anders Lybecker](https://twitter.com/AndersLybecker), whether I should be giving a talk on [scriptcs](http://scriptcs.net). Unfortunately, I didn't make the cut, but was giving a user group talk at [CNUG](http://www.meetup.com/Copenhagen-Net-User-Group/events/211915142/) on Tuesday night instead. 

![Self](https://pbs.twimg.com/media/B3Td_4TIgAArxwc.jpg:medium)

### Sessions

Campus Days 2014 had 11 tracks(!), but in essence there two-three tracks that would be relevant for .Net developers. These tracks were App Development, Microsoft Azure & Web Development, and Visual Studio & Team Foundation Server. So in these three tracks I found my sessions.

[Mary Jo Foley](https://twitter.com/maryjofoley) from ZDNet gave an inspiring keynote on how she saw Microsoft in the past, today, and tomorrow. This was not the usual keynote, which gave a sort meh-feeling afterwards. With over 900 attendees, a large venue and Microsoft as host, I would expect them to be able to attract a larger speaker name.

I started out at session called Introduction to Project Roslyn with [Henrik Lykke Nielsen](https://twitter.com/dotHenrik). I think this was my third introduction to Roslyn, and none, this one included, has given me the everyday tool that I was promised. What I really mean by this is, that since I heard about Roslyn back i 2011, I was told that this would revolutionize the .Net developer's toolbox. I still miss this point. Henrik gave a good baseline introduction to Roslyn, and then, like the other 'introduction to' sessions I've attended, showed of how to make analysis and diagnostic tools for Visual Studio. This is a great feature, no doubt, but who would argue that this is a feature that the everyday .Net developer would use? I might not be too objective, but I think that what [scriptcs](http://scriptcs.net) does is were I see power of Roslyn unleashed - powerful and instant compilation of C# code.

![Henrik Lykke Nielsen](https://pbs.twimg.com/media/B3R1nD5IEAAsXw5.jpg:medium)

[Anders Lybecker](https://twitter.com/AndersLybecker) gave a nice session on [Azure Search](http://azure.microsoft.com/en-us/documentation/services/search/) full-text searching. Having experience with [Elastic Search](http://www.elasticsearch.org/) I think Azure's 'we'll do the configuration for you'-approach is something that might trigger a lot of newcomers into using these search engines. It is very straightforward to install and configure Elastic Search, but if you are new to full-text searching the Azure Search approach is very appealing.

[Mogens Heller Grabe](https://twitter.com/mookid8000) held his famous talk on CQRS and event sourcing. I've heard the talk before, but Mogens did some very neat things using [Azure Service Bus Relay Service](http://azure.microsoft.com/en-us/documentation/articles/service-bus-dotnet-how-to-use-relay/), which I've never seen before. With the relay service you can create hybrid applications that can run both on-promise and in the cloud, and still use the same infrastructure. The relay service makes it possible to by-pass firewall restrictions.

![Mogens Heller Grabe](https://pbs.twimg.com/media/B3SwNZoIQAEMLnQ.jpg:medium)

On the second day, I went to see [Morten Christensen](https://twitter.com/sitereactor) do a session on [Azure Management Libraries](http://www.bradygaster.com/post/getting-started-with-the-windows-azure-management-libraries). In essence the Azure Management Libraries is bunch of CLI tools for automating everything around managing your Azure instances. Being a CLI and automation fan-boy I found these tools very appealing, and something that I need to have in my toolbox. Morten did somehow mistime his talk, and did the session in 30 minutes, despite it being a 75 minutes slot. My own experience is that it is very difficult to hit the exact time, but a 45 minutes off is way too much. 30 minutes into a session slot, doesn't give the attendees a chance to find different session, since it's difficult catch up for missing 30 minutes.

In the next slot, I attended [Christian Horsdal](https://twitter.com/chr_horsdal) doing a talk on modulary web applications using OWIN/Katana. I've used [OWIN](http://owin.org/) for [OAuth2](http://oauth.net/) before, but Christian showed off some nice Func-of-Func examples, that really illustrated the OWIN pipeline workflow. Good talk, with a nice flow and good balance between slides and code.

Later I had the pleasure of enjoying [Christian Dalager](https://twitter.com/dalager)'s talk on [Azure WebJobs](http://azure.microsoft.com/en-us/documentation/articles/web-sites-create-web-jobs/). I've never had any scenario for WebJobs, but they are pretty cool. So, basically you can write scripts in your favorite script language, although scriptcs is still missing, and make your Azure Website make use of it to do some background job. Christian demoed a small Twitter-like app for sending emotional tweets, or emotweets. He showed how the site would benefit from the use of WebJobs. I really like Christian's presentation, but informative and funny.

![Christian Dalager](https://pbs.twimg.com/media/B3Xb-fxCEAAXl1e.jpg:medium)
