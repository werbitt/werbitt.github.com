How I Control My Mac Mini From My Laptop
================================================================================

When I'm in my living room, watching TV on my Mac mini, while doing work on my laptop, rather than having to reach for a mouse or keyboard, I like to control the mini using my laptop. In this post I'm going to describe the setup I'm currently using. I use Macs at home, so this description is going to be Mac centric, but it should be adaptable to other OS's.

The open source software that makes this easy is a command line tool called [Synergy][1]. There are several GUI wrappers that rely on Synergy for their core functionality, such as [QuickSynergy][2]. Synergy is designed to allow you to seamlessly move your mouse from th edge of one desktop to another. What I'm really interested in is using a keyboard shortcut to switch between desktops. None of the GUI applications I tried provided for this functionality. I was able to come up with a solution by properly configuring the Synergy command line tool. Here are the steps I took.


Install Synergy
--------------------------------------------------------------------------------

The first step is to install Synergy. My preferred method is to use [Homebrew][3], but you can check the [Synergy Documentation][4] to explore other options. If you decide to use Homebrew, all you need to do is type the following at the command line (assuming you've installed Homebrew).

    brew install synergy

Install synergy on both the laptop and the mini.


Configure Synergy
--------------------------------------------------------------------------------

Synergy can be configured via .synergy.conf file in your home directory, see the [documentation][5]. The configuration file needs to be on the Synergy Server, the one with the keyboard you want to use. Here's an example of a .synergy.conf that allows you to switch between screens using a keystroke instead of window edges.

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
      switchCorners = all
      switchCornerSize = 9999
      keystroke(alt+control+m) = switchInDirection(right), lockCursorToScreen(on)
      keystroke(alt+control+Space) = keystroke(Space, mini.local)
    end

The configuration file is split into three sections: *screens*, *links*, and *options*. 

The *screens* section is where you declare the hostnames of all of your computers followed by a colon. The *links* section is used to establish the screen edges that act as portals between your various desktops. It's necessary to establish these relationships even though we'll be using a keystroke to switch screens. The *options* section is where I get Synergy to behave exaclty the way I want. I'll explain my settings here.

    switchCorners = all
    switchCornerSize = 9999

The switchCorner option is used to prevent screen switching when your mouse is in a given corner. In the *links* section we set the right side of both screens to act as the portal to the other desktop. By setting the swithCornerSize to be greater than half the screen height, we've effictevly blocked off the entire edge, so the first part of our goal is achieved.


    keystroke(alt+control+m) = switchInDirection(right), lockCursorToScreen(on)
    keystroke(alt+control+Space) = keystroke(Space, mini.local)

Here I'm defining some keyboard shortcuts that are available globally when Synergy is running. Somewhat unexpectedly, on a Mac, 'alt' is equal to the Command key. 

The first shortcut is necessary, it's the only way we'll be switching screens. Pressing Command+Control+M toggles between the two screens. It also locks the cursor to the screen because it seems less hacky than the switchCorner trick. 

The second keyboard shortcut is really just for convenience. Pressing Command+Control+Space, always sends a spacebar keypress to the mini, which is useful for pausing and unpausing video. You can easily set up other shortcuts to control volume, move between tracks, etc.


Running Synergy Automatically
--------------------------------------------------------------------------------

The next step is to set up both computers to run Synergy on startup. On a Mac this is done with [launchd][6].

[1]: http://synergy-foss.org/
[2]: htte://code.google.com/p/quicksynergy/
[3]: http://mxcl.github.com/homebrew/
[4]: http://synergy-foss.org/tracker/projects/synergy/wiki/Docs
[5]: http://synergy2.sourceforge.net/configuration.html
[6]: http://developer.apple.com/library/mac/#documentation/Darwin/Reference/ManPages/man8/launchd.8.html
