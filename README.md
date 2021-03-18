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

## Diagrams

### Basic Alarm Process

The alarm stuff should be handled by the arduino; the esp should handle the scheduling.

```
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
