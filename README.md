# medicine-alarm-clock
An alarm clock and accompanying android app, that cannot be switched off until it is opened.

## Ideas

### StandAlone

The alarm clock itself will be a standalone entity. The app will primarily be for extending the alarm functionality to outside of the visual range. Like, when the person is downstairs or something. When the alarm triggers, a light will flash (maybe an alarm will sound, but probably not), the alarm will not stop until the clock is opened, and only opening the clock will stop the alarm.

### WiFi connectivity

the clock is connected to the local WiFi. The app will only trigger an alarm once it is connected to the WiFi network the clock is on. Alternatively, the clock could act as

mDNS:
  Hopefully I can implement bonjour service inside of the app, which will let me give the arduino an mDNS name. If so, this could give me a lightweight way of finding the medicine clocks on the network, without having to cycle through all IP addresses. I could build a website to interact with the clock on their computer, which could be a convenient way for the user to adjust schedules.

IP:
  If I can't implement bonjour, the app will have to cycle through all the IP addresses on the network to find the ones that are clocks.

## Energy

energy usage shoud be minimised as much as possible to maximise battery life, which will be a challenge as the esp uses a lot of current. putting the chip into a low frequency and limiting antenna power are the obvious ways of reducing power, but there are a couple of other options:

1. the esp updates a server with a timestamp every time an alarm is turned off. this lets the device switch the modem off basically all the time, and only turn it on when the clock is opened. This could also mean that the alarm clock runs off of an arduino that instructs an esp8266, or maybe that the core functionality is handled by an esp32s coprocesser. 

2. esp only turns wifi on during an alarm period. For ruggedness sake, the wifi could turn off after a few hours, or when the app connects to the device.

While the first approach is obviously the better one and not actually that expensive ($7 a month on heroku, and that will cover me and my family and several thousand more alarm clocks), the second approach is actually a really good one for when the clock can't connect to the internet (i.e. if you've taken it out to work or camping or whatever).

## User Requirments

alarm clock:
* it's an alarm clock with multiple alarms every day
* it should be possible to set a start time, end time, and number of alarms, and the other alarms are filled out (to the nearest 10 minutes)
* the alarms should be visual. perhaps a timeline where the user can click and drag alarms around
* it should be possible to set each alarm individually, by entering numbers and days
* you should be able to set the days that the alarm goes off (perhaps each alarm is a profile that gets days associated with it?)
* the clocks should have a mode where the radio stays on, so that the settings/times can be changed
* the clock should have a button indicating that the medicine has been taken early
* the clock should only update the server when opened during an alarm (perhaps there should be a button informing the clock not to update the server, and no taken-early button?)

in app:
* you should be able to change the settings of each clock
* the app should be able to update each clock's settings, and if it can't reach the clock, keep notifying the user until it can.
* the app should be able to connect directly to the clock for when there isn't a network
* future: there should be different alarm profiles, that are switched automatically based on location, time of day, if the device is on wifi, etc.
* distant future: a parent should be able to manage and update a child's app remotely
* the alarm should be snoozable, with an easy to set snooze length
* on snooze, the app should post a notification that can be clicked on to adjust the length of the current snooze.

server:
* the alarm clock should have a static IP, and update the server with it's IP every time it connects.
* the server should store the last few timestamps. (maybe as a list that can be parsed into a FIFO array)


## Diagrams

### Alarm Timeline

I'm picturing container with tabs for alarms

```diagrams.code
# starts with an empty timeline
user->timeline: "clicks on timeline"
timeline->DOM: "requests click position,
timeline start, and timeline stop"
DOM-->timeline: "returns positions"
timeline->timeline: "interpolates click position,
rounding to nearest 15 minutes"
timeline->alarm: "creates new alarm object with set time"
```

### Basic Alarm Process

The alarm stuff should be handled by the arduino; the esp should handle the scheduling.

```diagrams.code
mP->mP: "sets external interuppt triggered by clock being opened"
mP->mP: "sets sleep timer"
mP->mP: "goes to sleep"
# after period of sleep
mP->RTC: "gets time"
RTC->mP: "returns time"
mP->mP: "checks if time is after nextAlarm"
# if alarm should be triggered
mP->LED: "flashes LED"
mP->mP: "goes to sleep"
user->clock: "opens clock"
clock->mP: "wakes arduino"
mP->LED: "stops LED"
mP->mP: "sets nextAlarm to be next alarm in schedule"
mP->mP: "goes to sleep"
```
