---
title: "ThemeChanger"
date: 2021-12-06T07:51:57Z
draft: true
---
ThemeChanger is an application that I made, one of my more ambitious projects so far. It is made for linux systems (and it should work on other friends like BSDs), and it does what it says: it changes themes. It is made to work with various desktop environments or none at all. While there is simmilar app called `lxappearance` which is also a GUI theme changer, my app has a bunch of extra features.

## How I got the idea for this app
As I started using linux more and more, I eventually learned about tiling window managers, and started using them instead of regular desktop environments. I was a little bit too obsessed with ensuring a consistent look&feel for my desktop, and the available GUI tools had some limitations that I felt could be addressed by a new application that I could write. So I started working on a such application: I attempted multiple programming languages, but ended up with Python, as I could do stuff easier with it.

## Features of the app
- Sets GTK themes.
- Sets icon themes.
- Sets cursor themes.
- Sets Kvantum themes, if support is detected.
- Can set different themes for GTK 2, GTK 4 (useful if the currently selected theme for GTK 3 doesn't have a version for any of these versions).
- All these themes can be selected using a searchable theme list, which makes managing a large theme collection easier. The first 3 have also thumbails for each theme so that they can be easily identified.
- Supports live reloading using a variety of mechanisms: either the DE-independent `xsettingsd`, or the various solutions provided by each DE (note that setting different GTK themes for different versions can't work at all in this case).
- Lets the user make and edit custom GTK CSS with live, in-app preview.
- Sets various options related to fonts widgets, etc.
- Lets the user quickly install theme archives to their user installation paths.

## How the development went
On linux, the main GUI toolkit, for which themes are made is GTK. As such, I decided that my application should be written using this toolkit. I attempted various languages (that have bindings to GTK - many have them, and these bindings have minimal dependencies besides what one is expected to have in a linux installation), and after months of painful research and failed attempts in Rust and Vala, the Python app got published on GitHub for the first time in July 2021, though it definitely wasn't _done_ yet: it had some bugs, and some missing features that I wanted.

After fixing some early bugs, I started working on adding a new feature: thumbnails for all the available GTK, icon, cursor themes. The GTK and icon themes thumbnails were easily added, as in the theme folder, a GTK theme may have a `thumbnail.png` that can be loaded and displayed with ease (for those that don't have it, a dummy image is displayed), and the icon themes have icons which can also be easily displayed. The cursor themes, on the other hand, aren't so easy to get displayed: the cursor are stored in a specific format, decodable by `libxcursor`, a library used by the theme changers in XFCE and MATE for cursor themes previews, though I couldn't really understand their implementations of this feature. I even thought at that time to rewrite once again the app in a language with some sort of access to `libxcursor`, but I let that feature for another time.  I eventually managed to just use the C code of the XFCE theme changer (they're an open source project, after all) in a C Python module after some time.

While the searchable theme list made the app unique in a good sense, it turned out that it made the app print tons of stacktraces, and I had to figure out the nasty code that produced this stacktrace, that I managed to fix, alongside other hidden issues.

Alongside GTK, there's another big GUI toolkit for linux: QT. While the GTK themes are somewhat standardised for each version, QT's theming is messier. A popular method of theming QT is Kvantum, for which many GTK themes have a counterpart. Adding support for setting its themes wasn't the hardest feature to be implemented, though there's some stuff that can be further implemented for a more complete theme changing experience.

The next feature to get implemented is live reloading. In linux, this is possible using XSETTINGS, a protocol through which various values related to look&feel can get set and updated instantaneousely across the apps that use them. To make XSETTINGS work, you need a daemon that takes care of these values. There are a few daemons available, ranging from the DE-independent `xsettingsd` to the sollutions provided by each DE. Each daemon has its own method of updating the values, and I had to implement methods of doing that for each daemon. I first implemented support for `xsettingsd`, then gradually for the daemons which come with the DEs of linux, with methods of determining which daemon is running, if any.

This is where the app stands at the moment of writing this post. I have some plans for the future, though.

## Plans for the future
First, I plan to add a methid to get thenes online, from the dedicated sites like [gnome-look.com] and friends. While their sites' UX really sucks, they supposedly provide some APIs that can be used, though I couldn't yet wrap my head around how they work. Hopefully I will acheive that eventually...

This is a big milestone to be acheived. I also plan on adding some more theming options to my app, like whether clicking the scrollbar will move the content directly to the selected position or page by page. I also want all these options to be applied to Kvantum too, which requires copying a theme file into Kvantum's config folder, which requires storing for each theme its path, which is also useful for another feature I want to add: the ability to remove locally installed themes.

Besides these, I feel like the code I've written isn't the cleanest, most well-organised there is (after all, this is, like, my first serious programming project that I share with the public), and I feel like I should make something about it sometime, maybe...
