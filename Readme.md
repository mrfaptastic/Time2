# Arduino Time Library

Time is a library that provides timekeeping functionality for Arduino.

Using the Arduino Library Manager, install "*TimeLib2* by *Faptastic*".

A primary goal was to enable date and time functionality that can be used with
a variety of external time sources with minimum differences required in sketch logic.

Example sketches illustrate how similar sketch code can be used with internet NTP time service.

## Functionality

To use the Time library in an Arduino sketch, include TimeLib2.h.

Unlike TimeLib, TimeLib2 uses a seperate class for each clock you wish to manage, so you can  have different clocks at different offsets / timezones.

```c
#include <TimeLib2.hpp>

TimeLib2 clock;

clock.setTime(<insert unix timestamp here>)
clock.setOffset(<insert unix timestamp here>)

time_t epoch = clock.getEpochSecond(); // historically called now(), but changed the name to align to java.time


```

The functions available from the TimeLib2 class include

```c
hour();            // the hour now  (0-23)
minute();          // the minute now (0-59)
second();          // the second now (0-59)
day();             // the day now (1-31)
weekday();         // day of the week (1-7), Sunday is day 1
month();           // the month now (1-12)
year();            // the full four digit year: (2009, 2010 etc)
```

there are also functions to return the hour in 12-hour format

```c
hourFormat12();    // the hour now in 12 hour format
isAM();            // returns true if time now is AM
isPM();            // returns true if time now is PM

getEpochSecond();             // returns the current time as seconds since Jan 1 1970
```

The time and date functions can take an optional parameter for the time. This prevents
errors if the time rolls over between elements. For example, if a new minute begins
between getting the minute and second, the values will be inconsistent. Using the
following functions eliminates this problem

```c
time_t t = now(); // store the current time in time variable t
hour(t);          // returns the hour for the given time t
minute(t);        // returns the minute for the given time t
second(t);        // returns the second for the given time t
day(t);           // the day for the given time t
weekday(t);       // day of the week for the given time t
month(t);         // the month for the given time t
year(t);          // the year for the given time t
```

Functions for managing the timer services are:

```c
setTime(t);                      // set the system time to the give time t
setTime(hr,min,sec,day,mnth,yr); // alternative to above, yr is 2 or 4 digit yr
                                 // (2010 or 10 sets year to 2010)
setOffset(adjustment);          // adjust system time by adding the adjustment value
timeStatus();                    // indicates if time has been set and recently synchronized
                                 // returns one of the following enumerations:
timeNotSet                       // the time has never been set, the clock started on Jan 1, 1970
timeNeedsSync                    // the time had been set but a sync attempt did not succeed
timeSet                          // the time is set and is synced
```

Time and Date values are not valid if the status is `timeNotSet`. Otherwise, values can be used but
the returned time may have drifted if the status is `timeNeedsSync`. 	

```c
setSyncProvider(getTimeFunction);  // set the external time provider
setSyncInterval(interval);         // set the number of seconds between re-sync
```

There are many convenience macros in the `TimeLib.hpp` file for time constants and conversion
of time units.

To use the library, copy the download to the Library directory.

## Example

Refer to the 'examples' folder.

## Technical Notes

Internal system time is based on the standard Unix `time_t`.
The value is the number of seconds since Jan 1, 1970.
System time begins at zero when the sketch starts.

The internal time can be automatically synchronized at regular intervals to an external time source.
This is enabled by calling the `setSyncProvider(provider)` function - the provider argument is
the address of a function that returns the current time as a `time_t`.
See the sketches in the examples directory for usage.

The default interval for re-syncing the time is 5 minutes but can be changed by calling the
`setSyncInterval(interval)` method to set the number of seconds between re-sync attempts.

The Time library defines a structure for holding time elements that is a compact version of the C `tm` structure.
All the members of the Arduino `tm` structure are bytes and the year is offset from 1970.
Convenience macros provide conversion to and from the Arduino format.

Low-level functions to convert between system time and individual time elements are provided:

```c
breakTime(time, &tm);  // break time_t into elements stored in tm struct
makeTime(&tm);         // return time_t from elements stored in tm struct
```
# Credits

Based on a fork of: https://github.com/PaulStoffregen/Time
