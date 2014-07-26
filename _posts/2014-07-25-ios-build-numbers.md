---
layout: post
title: An iOS build numbering strategy
comments: true
---

## The problem

Xcode provides us with two fields for versioning an app - the version number and the build number. The Version is customer facing and displayed in the App Store.

```{Major Version}.{Minor Version}.{Revision}```

The Build is used internally for things like crash reports, event tracking, and user agents. It is typically a monotonically incrementing integer.

How do we unify the two?

<!---more--->

## The solution

At [Vango](http://www.vangoart.com) I typically pull together an ad hoc build once a day for our team to test internally. Before I roll a build, I bump the build number using the letters of the alphabet. So 100a is followed by 100b is followed by 100c, and so on. The first three digits of the build number always match the verion, in this case 1.0.0.

When I am preparing to submit a release build to the App Store, I drop the letter from the build number so it matches the version number exactly. Apple does not allow a build number to contain letters when you submit.

The next version of the app becomes 1.1.0 and the build becomes 110a. Wash, rinse, repeat.

## Pros

If you are tracking user agent in server logs or [mixpanel](http://www.mixpanel.com), you no longer have to scramble to convert between build numbers and version numbers in your head. For example:

> babyrhino/441b (iPad; iOS 7.0.4; Scale/2.00)

It is easy to decode the first part - this is the second (**b**) ad hoc build of version 4.4.1. The user agent is sent as a header with every HTTP request.

Another advantage is that it makes communicating with the team much easier. For example:

> "Hey Joe, what version are you testing with?"
"Oh I have 100c."
"Okay, let me look up your data."

## Cons

This strategy does not work past 9 minor versions or revisions because it assumes single digits for each. It will work past 9 major versions however. So 9.9.0 will be followed by 10.0.0.

You also lose the total count of builds. Each version will have a letter of the alphabet so you cannot go past 26 ad hoc builds for a given Version. I would argue that you probably should have shipped it by then anyway.

## Extra Credit

We also display the Version and Build in a settings bundle so everyone can easily look them up. Here is the code to make that happen:

{% highlight objective-c %}
@implementation AppDelegate
+ (void)initialize {
    NSDictionary *info = [[NSBundle mainBundle] infoDictionary];
    NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
    [defaults setValue:info[@"CFBundleShortVersionString"] forKey:@"settings_version_number"];
    [defaults setValue:info[@"CFBundleVersion"] forKey:@"settings_build_number"];
    [defaults synchronize];
}
@end
{% endhighlight %}

The NSUserDefaults keys are configured in the Settings bundle and you have to run the app once to see the correct values.

For more information, see the [Apple documentation](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Preferences/Preferences.html).
