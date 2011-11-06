How I Control My Mac Mini From My Laptop
================================================================================

When I'm in my living room, watching TV on my Mac mini, while doing work on my laptop, rather than having to reach for a mouse or keyboard, I like to control the mini using my laptop. In this post I'm going to describe the setup I'm currently using. I use Macs at home, so this description is going to be Mac centric, but it should be adaptable to other OS's.

The open source software that makes this easy is a command line tool called [Synergy][1]. There are several GUI wrappers that rely on Synergy for their core functionality, such as [QuickSynergy][2]. Synergy is designed to allow you to seamlessly move your mouse from th edge of one desktop to another. What I'm really interested in is using a keyboard shortcut to switch between desktops. None of the GUI applications I tried provided for this functionality. I was able to come up with a solution by properly configuring the Synergy command line tools. Here are the steps I took.


Install Synergy
--------------------------------------------------------------------------------

The first step is to install Synergy. I prefer [Homebrew][3], but you can check the [Synergy Documentation][4] to explore other options.

    brew install synergy

Install synergy on both the laptop and the mini.


Configure Synergy
--------------------------------------------------------------------------------

Synergy can be configured via .synergy.conf file in your home directory, see the [documentation][5]. Here's my .synergy.conf

    section: screens
      laptop.local:
      mini.local:
    end

    section: links
      laptop.local:
        right = mini.local
      mini.local:
        right = laptop.local
    end

    section: options
      relativeMouseMoves = true
      switchCorners = all
      switchCornerSize = 450
      keystroke(alt+control+m) = switchInDirection(right), lockCursorToScreen(on)
      keystroke(alt+control+Space) = keystroke(Space, mini.local)
    end

In this example, the hostname of my laptop is 'laptop.local' and the hostname of the mini is 'mini.local'.


[1]: http://synergy-foss.org/
[2]: htte://code.google.com/p/quicksynergy/
[3]: http://mxcl.github.com/homebrew/
[4]: http://synergy-foss.org/tracker/projects/synergy/wiki/Docs
[5]: http://synergy2.sourceforge.net/configuration.html
