JCenterizer

# NO LONGER NECESSARY

https://jfrog.com/blog/into-the-sunset-bintray-jcenter-gocenter-and-chartcenter/

> UPDATE 4/27/2021: We listened to the community and will keep JCenter as a read-only repository indefinitely. Our customers and the community can continue to rely on JCenter as a reliable mirror for Java packages.


The easiest to maintain code is the code you don't write, so with JCenter committing to keep the repo up for old dependencies this is no longer needed and I will not spend any further time on the idea unless/until that changes.


## Original README:

Automated workaround for popular dependencies only available on jcenter/bintray

Nothing here yet except the idea, but it is inevitable.

PRs happily and gratefully accepted.


----

Had a brainstorm on a solution that could be made to work similar to jetifier - a small centralized solution that adds forward-compatibility to projects dependent on old versions and can't upgrade for whatever reason. Realized after posting it that it would be better here:

https://github.com/facebook/yoga/issues/1065#issuecomment-823283295

----

@d4vidi [that's an interesting approach](https://github.com/facebook/yoga/issues/1065#issuecomment-823268654), you are on to something there. This is a potential start towards something automated for people that can't update.

Examine this if you have time: https://github.com/ankidroid/Anki-Android/blob/master/AnkiDroid/robolectricDownloader.gradle - the idea is to pre-download maven artifacts you'll need later

I wrote that in order to have external dependency download de-flaked in an E2E CI type pipeline where the downloads were failing builds and giving me a fail signal outside of my code changes. With that and an exponential-back-off-retry I have great signal to noise.

But - if the general idea of "you can have a gradle task that downloads dependencies before your real build" is combined with the idea of "react-native jcenter dependencies may be downloaded"...

1- store the problematic in-use artifacts on the official react-native github somewhere (or on their respective project pages) as downloadable things - have a known list of them, I think there are only a handful (yoga android-annotations, 2 flipper plugins, fbjni)

2- write a downloader script for them like the above

3- integrate that in to patch releases of RN CLI?

It could be just as terrible + functional as https://github.com/mikehardy/jetifier :-), which is and was kind of gross but also just glossed over the whole AndroidX migration for everyone at once and made it go away...

----

If done well it requires no external approval or waiting actually - a repo could simply get them now even if it wouldn't be officially blessed, write the task and throw them in the local ~/.m2 and integration of the package is to add an apply ... statement in your build script (something semi-normal for packages to ask) and make sure mavenLocal is in your repo list in build.gradle, so as a proof-of-concept it could be demonstrated prior to involving the official repos (so the artifacts are more official) or the RN CLI (so the task is included / default-on like jetifier for everyone)

Note that it may be useful to develop this capability regardless of the dependencies we know about being fixed, as there is always some other thing we did not know about. And if we have this capability then someone somewhere can get a copy of the artifact, post it in the downloadable spot, we can add it to the npm package for an update to the list and everyone is auto-fixed again until versions are actually updated (similar to jetifier which did have some missing edge cases at the start until adoption was 100% and is still a lifesaver for folks on RN59 etc)
