# Google Summer of Code 2019 with Samba

## Improve smbcmp, the capture diff tool

Mairo Paul Rufus

- Mentor : Aurélien Aptel 

### Abstract
Smbcmp is a cli tool for making diffs between two pcap files containing SMB packets and rendering them using curses. The first part of the project was to make better diffs by using the pdml output of Tshark and the second part was to add a GUI and port it to windows.
[Read full initial proposal](https://drive.google.com/open?id=1GTEwFOPJCv53DQpQ8lLEa7qpnHRQpMzq)

***

### Important Links
#### Repositories:
- https://github.com/RMPR/smbcmp
- https://github.com/aaptel/smbcmp

#### Samba-technical weekly reports
- [week 1 & 2](https://lists.samba.org/archive/samba-technical/2019-June/133801.html)
- [week 3](https://lists.samba.org/archive/samba-technical/2019-July/133877.html)
- [week 4](https://lists.samba.org/archive/samba-technical/2019-July/133915.html)
- [week 5](https://lists.samba.org/archive/samba-technical/2019-July/133928.html)
- [weeks 6 & 7](https://lists.samba.org/archive/samba-technical/2019-July/133987.html)
- [week 8](https://lists.samba.org/archive/samba-technical/2019-August/134038.html)
- [week 9](https://lists.samba.org/archive/samba-technical/2019-August/134064.html)
- [week 10](https://lists.samba.org/archive/samba-technical/2019-August/134093.html)
- [week 11](https://RMPR.github.io/gsoc_2019)

***
### Let's start from the beginning 
All started with this very small [pull request](https://github.com/aaptel/smbcmp/pull/2) where I was still trying to figure out what the code does, I had no prior experience with smb* protocol but a little with wireshark and it's cli tool tshark.

### Decisions 
After 2 weeks or so of setting up dev environnement and making frequents goings and comings between source code and python documentation (the tool didn't include any external dependency at the time) I decided to give up on rewriting the whole tool in another language since improvement is more focused on new features than code structure or whatever.

### Implementation time
This step consisted of two phases: one where I didn't really understand what needed to be done, the second one where I think I understood better.

#### Add colors to summary panels
This is a typical example of me not really understanding what needed to be done, instead of highlighting **all** the similar packets pairs in the summaries views, I highlighted only the selected packets, which is not useful, finally I gave up on this feature as it would have been really resource intensive for a low outcome on the user side.

#### Use the pdml output
This part take most of my time, after fiddling a bit with a [pdml parser](https://github.com/wireshark/wireshark/blob/master/tools/WiresharkXML.py) included in the wireshark github repository which was written using python 2 (I had to convert to python 3) and the json output of tshark which wasn't really human readable, I ended up changing my approach, so I decided to give [xmldiff](https://github.com/Shoobx/xmldiff) a try, since it was really promising, long story short, it didn't suit the needs of the project and at the end of the day, we had to come up with our own implementation of pdml parsing based on [trials](https://github.com/Shoobx/xmldiff/issues/47) and errors using a XML parsing library: lxml when possible and ElementTree otherwise, see [this]( https://lxml.de/performance.html) benchmark.
The pull request corresponding is https://github.com/aaptel/smbcmp/pull/5

#### GUI for smbcmp
This part is my favourite because I worked as fast as what I'm used to, sign that I understood a little more about the project and all that started by choosing a framework for GUI implementation, basically I decided to choose one out of the big three :
- [wxPython (phoenix)](https://wxpython.org/)
- [Pyside 2 aka Qt for Python](https://wiki.qt.io/Qt_for_Python)
- [Tkinter](https://docs.python.org/2/library/tkinter.html)

I ended up choosing wxPython because of its fully open source licence (not the case for qt with its dual licencing), the documentation available and the fact that it's a wrapper around native widgets (not as ugly as Tkinter) for all supported platforms: Windows, Linux, MacOS.

##### Samples of smbcmp-gui progression

- **Wireframing**

    ![Responsive](https://raw.githubusercontent.com/RMPR/gsoc_2019/master/res/init.gif)

- **Permute**

    ![permutation](https://raw.githubusercontent.com/RMPR/gsoc_2019/master/res/perm.gif)

- **Diffs v1**

    ![First Diffs](https://raw.githubusercontent.com/RMPR/gsoc_2019/master/res/diffs.gif)

- **Demo**

    ![demo](https://raw.githubusercontent.com/RMPR/gsoc_2019/master/res/final.gif)
    
The code for this part is available on my [branch](https://github.com/RMPR/smbcmp/commits/smbcmp-module)

#### Port to windows 
Porting to Windows was a relatively simple task as smbcmp doesn't have many dependencies (python, curses, tshark and wxwidget4) and they are all available on Windows. My mentor and I have produced a working proof-of-concept Windows build which isn't publicly available yet but that will soon be properly released.

***
### Next Step

 At this stage, the software provides most of the features required for an acceptable use, but there are features that would make it even smoother for the GUI users (add ignore rules, support for encrypted packets, search, ...)

For development purpose, it feel that it may be useful to add some automated tests.

I would love to work on these in future even when this GSoC ends, fix more bugs as they come up and present my work at the next [sambaxp](https://sambaxp.org/) (International SAMBA conference for users and developers).

***
### Credits

Firstly any of this would not have been possible without my mentor and other members of the community who gave me a [warm welcome](https://lists.samba.org/archive/samba-technical/2019-May/133529.html). So a huge shout-out to them and particularly to my mentor for helping me whenever I needed and for providing some guidance. While I was working, my mentor would remain active and provide me some useful feedback. This really kept me busy throughout the summer as I always had things to investigate.

Secondly, this is a Google program so thanks Google for the opportunity and the exposure to Open source.

Finally, I would thank any of my fellow gsocers, particularly WSCP, Sarfaraz Iraqui, Arpit, Liuba Orlova, ... while interacting with them I also learnt a bunch of useful things. 

***
### Final Note
 I made a lot of mistakes: messing with the github repos by pushing to my master branch, messing with my OS by installing nvidia drivers while not really relevant at that time (I had to set up the dev environment a second time after that), writing not the most readable code (use of plain eq to compare strings, static classes when not needed), lack of motivation sometimes, being irregular in posting weekly reports on the mailing list, ... this list is not exhaustive, and for all this I just want to apologize.
 
#### SMB2 Specification
https://winprotocoldoc.blob.core.windows.net/productionwindowsarchives/MS-SMB2/%5bMS-SMB2%5d.pdf

[![Built with Spacemacs](https://cdn.rawgit.com/syl20bnr/spacemacs/442d025779da2f62fc86c2082703697714db6514/assets/spacemacs-badge.svg)](http://spacemacs.org)
