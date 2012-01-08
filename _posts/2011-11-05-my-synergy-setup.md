Control This
================================================================================


I'd like to quickly document the way I'm using [Synergy][1] to control my HTPC using my laptop.

I'm not a big fan of [Synergy's][1] default method of using a screen edge to move between desktops.  It's important to me that I can switch control between PCs using a keystroke. 

My current setup is a Macbook Air and a Mac Mini HTPC, so this will be a Mac-centric post. Since [Synergy][1] is cross platform this should work with any setup with small adjustments.


Install Synergy
--------------------------------------------------------------------------------

The first step is to install Synergy. My preferred method is to use [Homebrew][2], but you can check the [Synergy Documentation][3] to explore other options. If you decide to use Homebrew, all you need to do is type the following at the command line (assuming you've installed Homebrew).

    brew install synergy

Install synergy on both the laptop and the mini.


Configure Synergy
--------------------------------------------------------------------------------

Synergy can be configured via a .synergy.conf file in the home directory on the laptop. Here's an example of a .synergy.conf that allows you to switch between screens using a keystroke instead of window edges. See the [documentation][4] for more options.

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

The next step is to set up both computers to run Synergy on startup. On a Mac this is done with [launchd][5].

We need to create a net.sourceforge.synergy.plist file on both the laptop and the Mac mini in the ~/Library/LaunchAgents directory

On the laptop:

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
      <key>OnDemand</key>
      <false/>
      <key>KeepAlive</key>
      <false/>
      <key>RunAtLoad</key>
      <true/>
      <key>Label</key>
      <string>net.sourceforge.synergy.plist</string>
      <key>ProgramArguments</key>
      <array>
        <string>/usr/local/bin/synergys</string>
        <string>--no-daemon</string>
        <string>--no-restart</string>
        <string>--name</string>
        <string>laptop.local</string>
        <string>--debug</string>
        <string>WARNING</string>
      </array>
      <key>ServiceDescription</key>
      <string>Synergy Server</string>
    </dict>
    </plist>


On the Mac mini:

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
    <plist version="1.0">
    <dict>
      <key>OnDemand</key>
      <false/>
      <key>KeepAlive</key>
      <false/>
      <key>RunAtLoad</key>
      <true/>
      <key>Label</key>
      <string>net.sourceforge.synergy.plist</string>
      <key>ProgramArguments</key>
      <array>
        <string>/usr/local/bin/synergyc</string>
        <string>--no-daemon</string>
        <string>--no-restart</string>
        <string>--name</string>
        <string>mini.local</string>
        <string>--debug</string>
        <string>WARNING</string>
        <string>laptop.local</string>
      </array>
      <key>ServiceDescription</key>
      <string>Synergy Client</string>
    </dict>
    </plist>

Lastly, on both the laptap and the mini navigate to the ~/Library/LaunchAgents directory in Terminal and enter the following:

    launchctl load net.sourceforge.synergy.plist

That's it, now we can use the laptop to control the HTPC and switch back and forth using a single keystroke.

[1]: http://synergy-foss.org/
[2]: http://mxcl.github.com/homebrew/
[3]: http://synergy-foss.org/tracker/projects/synergy/wiki/Docs
[4]: http://synergy2.sourceforge.net/configuration.html
[5]: http://developer.apple.com/library/mac/#documentation/Darwin/Reference/ManPages/man8/launchd.8.html
