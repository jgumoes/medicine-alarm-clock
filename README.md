# medicine-alarm-clock
An alarm clock and accompanying android app, that cannot be switched off until it is opened.

## Ideas

### WiFi connectivity

the clock is connected to the local WiFi. The app will only trigger an alarm once it is connected to the WiFi network the clock is on. 

mDNS:
  Hopefully I can implement bonjour service inside of the app, which will let me give the arduino an mDNS name. If so, I could build a website to interact with the clock on their computer, which could be a convenient way for the user to adjust schedules.

AP:
  If I can't implement bonjour, the clock will have to cycle through all the IP addresses on the network to find the ones that are clocks.
