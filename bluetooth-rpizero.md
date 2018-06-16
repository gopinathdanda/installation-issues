# Connecting Bluetooth Speaker to RPi Zero W (headless)

I was trying to connect a Bose Soundlink Mini II Bluetooth Speaker to my Raspberry Pi Zero W, which comes with built-in bluetooth. 
I was using the RPi without a screen (headless) over the WiFi. You can find a lot of tutorials on how to run an RPi as a headless device, so I won't be covering that here.

The idea is to connect to the speaker via the built-in bluetooth and then make it available to pulseaudio, which is a sound system for POSIX OSes.
After many difficulties and wading in the web for a long long (long) time, I was able to get it done. Here is how I did it.

## Installing pulseaudio bluetooth module and bluez

Connect to your RPi over SSH (intructions should be there with headless setup tutorials).

I first installed pulseaudio bluetooth module and bluez which apparently don't come preinstalled with Debian Jessie anymore.

<pre>
sudo apt-get install pulseaudio-module-bluetooth pulseaudio-utils bluez bluez-tools
</pre>

## Permission to use pulseaudio

This was the main issue I was encountering when trying to connect. I was able to find and connect (procedure described later), but the connection would be cut off almost immediately
giving the following error:

<pre>
Attempting to connect to XX:XX:XX:XX:XX:XX
Failed to connect: org.bluez.Error.Failed
</pre>

As a simple web search will reveal, this is a very common issue but everyone's solution differs. For me it was a problem of permissions.

You have to add your username to the lp group.

<pre>
sudo adduser $USER lp
</pre>

## Autoconnect when switching audio device

Add the following line at the end of your pulseaudio default file (/etc/pulse/default.pa), which is loaded everytime you restart pulseaudio.

<pre>
# Automatically switch audio device on connect
load-module module-switch-on-connect
</pre>

## Restart system and pulseaudio

This is the solution to most of the problems in this field.

<pre>
sudo reboot
</pre>

Once the RPi reboots, connect to it via SSH and start pulseaudio.

<pre>
pulseaudio --start
</pre>

A useful command is the kill command for pulseaudio, which you might need in case you want to restart it during a session.

<pre>
pulseaudio -k
</pre>

Alternatively, to start pulseaudio automatically whenever you login, you can add the following line to you ~/.bash_profile file:

<pre>
pulseaudio --start
</pre>
 
## Connecting to your speaker

Start your bluetooth console:

<pre>
sudo bluetooth -a
</pre>

It should show you your bluetooth card details. Then turn on your bluetooth speaker and start scanning in the RPi bluetooth console:

<pre>
[bluetooth]# scan on
</pre>

Your bluetooth speaker should show up in a while. This is what I got.

<pre>
Discovery started
[CHG] Controller YY:YY:YY:YY:YY:YY Discovering: yes
[NEW] Device XX:XX:XX:XX:XX:XX Bose Mini II SoundLink
</pre>

Here, XX:XX:XX:XX:XX:XX is the address of your speaker.
You can now turn off your scan:

<pre>
[bluetooth]# scan off
</pre>

Note that now you can autocomplete your Device address which has been scanned. So now trust, pair and connect to your speaker:

<pre>
[bluetooth]# trust XX:XX:XX:XX:XX:XX
[bluetooth]# pair XX:XX:XX:XX:XX:XX
[bluetooth]# connect XX:XX:XX:XX:XX:XX
</pre>

It should connect easily. If it shows the org.bluez.Error.Failed error, then restart your pulseaudio after quitting bluetoothctl, and then retry connecting.

<pre>
[bluetooth]# quit
pulseaudio -k
pulseaudio --start
sudo bluetoothctl -a
[bluetooth]# connect XX:XX:XX:XX:XX:XX
</pre>

In case you run into issues, you can get details of the error by running:

<pre>
sudo systemctl status bluetooth.service
</pre>

You can also restart just the bluetooth module using:

<pre>
sudo systemctl restart bluetooth
</pre>
