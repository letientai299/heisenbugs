# Heisenbugs

[Heisenbug][heisenbug_catb] is accepted as the [hardest type of
bug][hardest_bugs], because, by definition, it's computer bug that disappears or
alters its characteristics when an attempt is made to study it. This is a
compiled list of such historical bugs.

To save your time, each bugs some with a short summary of what happens and the
root cause. But, for maximize effect, you should check the bug's link.

## Table of contents

- [Calendar and date bugs](#calendar-and-date-bugs)
  - [A unit test which only failed on Sundays](#a-unit-test-which-only-failed-on-sundays)
  - [OpenOffice can't print on Tuesdays](#openoffice-cant-print-on-tuesdays)
  - [Webpack Doesn't Work on Monday (On Windows)](#webpack-doesnt-work-on-monday-on-windows)
  - [Every Zune 30 froze on Dec 31, 2008](#every-zune-30-froze-on-dec-31-2008)
- [Accumulation over time](#accumulation-over-time)
  - [Patriot missile clock drift (1991)](#patriot-missile-clock-drift-1991)
  - [Boeing 787 must reboot every 248 days](#boeing-787-must-reboot-every-248-days)
- [Environment and physics](#environment-and-physics)
  - [We can't send email more than 500 miles](#we-cant-send-email-more-than-500-miles)
  - [The code worked differently when the moon was full](#the-code-worked-differently-when-the-moon-was-full)
  - [Debugging an evil Go runtime bug (2017)](#debugging-an-evil-go-runtime-bug-2017)
- [Timing and race conditions](#timing-and-race-conditions)
  - [A file reliably cauases printer jam](#a-file-reliably-cauases-printer-jam)
  - [The car allergic to vanilla ice cream](#the-car-allergic-to-vanilla-ice-cream)
  - [Therac-25: a race condition killed patients (1985–1987)](#therac-25-a-race-condition-killed-patients-19851987)
- [Observer effect](#observer-effect)
  - [Why does JavaScript only work after opening developer tools in IE once?](#why-does-javascript-only-work-after-opening-developer-tools-in-ie-once)
  - [When you see a heisenbug in C, suspect your optimizer](#when-you-see-a-heisenbug-in-c-suspect-your-optimizer)
- [Relevant readings](#relevant-readings)

## Calendar and date bugs

### [A unit test which only failed on Sundays][unit_test_sunday]

A _faulty unit test_ for a custom date time parser that expect different result
on Sunday compare to other days of week. The test assume that the parser can
make a round trip for any input: parse and then format it again must yield the
original input. Unfortunately, one of the input is _overflowed_, and yield
different result once it's formatted.

### [OpenOffice can't print on Tuesdays][open_office_tuesday]

OpenOffice produces a postscript document before send it to [cups][ubuntu_cups]
for printing. The postscript document will contains a line like:

```text
%%CreationDate: (Tue Mar 3 19:47:42 2009)
```

`cups` use [file][unix_file] to identify if the file is a printable format. And
`file` mislabels the postscript document as Erlang JAM file, because its
internal database of Erlang JAM magic entry is incorrect (space is not escaped).
The Erlang magic entry is:

```text
4 string Tue Jan 22 14:32:44 MET 1991 Erlang JAM file - version 4.2
```

### [Webpack Doesn't Work on Monday (On Windows)][webpack_monday]

[webpack-cli][gh_webpack_cli] v3.3.4 has some changes to the code that show the
donation banner, to show it only on Monday. This is an attempt to make the
banner appears less frequently (as people previously complain about it). The new
implementation uses `process.getuid()`, which is not available on Windows,
causing webpack to crash only on Monday.

### [Every Zune 30 froze on Dec 31, 2008][zune_bug]

Every [Zune 30][zune_wiki] in the world bricked simultaneously on New Year's
Eve 2008. The Freescale clock driver had a `while (days > 365)` loop that, on
the 366th day of a leap year, could never exit — `days` was not greater than
365, but the nested `if (days > 366)` also didn't match. The infinite loop
drained the battery. Waiting until January 1 "fixed" it. A worldwide
synchronized freeze with a 4-year recurrence cycle.

## Accumulation over time

### [Patriot missile clock drift (1991)][patriot_missile]

The MIM-104 Patriot's system clock drifted by 0.34 seconds after 100 hours of
continuous operation. The cause: floating-point truncation of 1/10 in a 24-bit
fixed-point register. After 100 hours, the tracking radar's range gate shifted
687 meters — enough to miss an incoming Scud missile. A software fix was shipped
on February 26, one day after the failed interception.

### [Boeing 787 must reboot every 248 days][boeing_787]

A signed 32-bit counter in the generator control units (GCUs) incremented every
10 ms overflows after 248 days of continuous power. All four GCUs enter failsafe
mode simultaneously, risking total loss of AC electrical power mid-flight. The
FAA issued an [Airworthiness Directive][boeing_faa] mandating power cycling
every 120 days until a software fix was deployed.

## Environment and physics

### [We can't send email more than 500 miles][500_miles]

Email server application was unintentionally downgraded. Some configurations for
the previous (newer) version are not applied, and was reset to zero, include the
timeout to connect to remote SMTP server. Hence, the server abort the connect
call after 3 milliseconds, which is only enough to send and receive packet
within ~558 miles.

### [The code worked differently when the moon was full][cern_moon]

Particle physics experiments at CERN's [LEP][lep] accelerator produced different
results depending on the phase of the moon. The moon's tidal pull physically
deformed the 27 km circumference ring by ~1 mm, shifting beam energy. To this
day, [LHC][lhc] operators must correct proton orbits twice daily to compensate
for tidal forces. The effect is largest during full moon and new moon, when the
sun and moon align their gravitational pull.

### [Debugging an evil Go runtime bug (2017)][go_evil_runtime_bug]

[node_exporter][node_exporter], a tool to export machine metric from prometheus,
crashes magically on just some machine. Further debugging show that it has
something to do with the kernel. Turn out that Go allocate very small stack (104
bytes at the time), which is not enough for the kernel.

## Timing and race conditions

### [A file reliably cauases printer jam][printer_jam]

A hardware related The drum in that laser printer rotate every 3 seconds. If the
image from the file to be printed need more than 3 seconds to be renderd, the
drum will stop, and it will start again once next image is ready. The faulty
file took slightly longer than 3 seconds to render. So the drum need not only
need to stop, but also had to start again when it was not fully stopped. That
causes the printer jam.

### [The car allergic to vanilla ice cream][vanilla_ice_cream]

A Pontiac owner reported that his car wouldn't start after buying vanilla ice
cream, but started fine after any other flavor. An engineer discovered vanilla —
the most popular flavor — was stocked at the front of the store, so the trip was
shorter. The engine didn't have enough time to cool, causing [vapor
lock][vapor_lock]. The bug's trigger was time, not flavor. Likely an urban
legend ([Snopes rates it unverified][snopes_vanilla]), but it's been told since
at least 1978 and is enshrined in [software folklore][sw_folklore].

### [Therac-25: a race condition killed patients (1985–1987)][therac_25]

The [Therac-25][therac_wiki] radiation therapy machine overdosed patients due to
a race condition that only triggered when the operator typed corrections fast
enough — within ~8 seconds. The manufacturer couldn't reproduce it for over 2
years, because testing changed the timing. The same bug existed in the
predecessor Therac-20, but was masked by hardware interlocks that were removed
in the 25.

## Observer effect

### [Why does JavaScript only work after opening developer tools in IE once?][ie_console_only_when_debug]

The console object is only activated when the Dev Toolbar is opened. Prior to
that, calling the console object will result in it being reported as undefined.
After the toolbar has been opened, the console will exist (even if the toolbar
is subsequently closed), so your console calls will then work.

### [When you see a heisenbug in C, suspect your optimizer][hercules_heisenbug]

Jay Maynard's [Hercules][hercules] IBM 360 emulator segfaulted on a particular
instruction, but building with `-g` (debug symbols) or enabling internal tracing
made the bug vanish — a textbook heisenbug. The culprit was GCC's `-O3`
optimization: the compiler reordered or eliminated code that the program
depended on, but only in release builds. Adding debug flags changed the compiled
output enough to hide the fault.

---

## Relevant readings

Those stories are for strange things related to compute system, but can't be
categorize as bugs.

- [A story about magic][magic_story]: a switch that no body knows what it does,
  but the computer will crash if the swich is flipped.
- [Type of bugs][bug_types]: four types of bugs, named after popular scientists,
  with some interesting in the classification.
- [Software Folklore][sw_folklore]: a collection of weird bug stories with
  symptoms beyond belief.

## Contribution

Any PR for new bugs or stories like these, or typo, grammar fixes, are welcome.

## Final words

I can't say it better than [Ned Batchelder][printer_jam], so, I copy his words
here to remind us all again:

> - Just because a bug seems impossible doesn’t mean it is.
> - Abstractions are everywhere, and they can be broken. As a software guy, I
>   believed that getting the paper into the output tray was a solved problem.

<!--
External links, use this style to make the code clean and avoid duplicated
links.
-->

[heisenbug_catb]: http://www.catb.org/jargon/html/H/heisenbug.html
[webpack_monday]: https://github.com/webpack/webpack-cli/issues/962
[hardest_bugs]: https://stackoverflow.com/a/570377/3869533
[gh_webpack_cli]: https://github.com/webpack/webpack-cli
[500_miles]: http://web.mit.edu/jemorris/humor/500-miles
[unit_test_sunday]: https://qntm.org/unit
[open_office_tuesday]:
  https://bugs.launchpad.net/ubuntu/+source/cupsys/+bug/255161
[file_erlang_jam_bug]:
  https://bugs.launchpad.net/ubuntu/+source/file/+bug/248619
[unix_file]: https://en.wikipedia.org/wiki/File_(command)
[ubuntu_cups]: https://help.ubuntu.com/lts/serverguide/cups.html
[printer_jam]:
  https://nedbatchelder.com/blog/200811/print_this_file_your_printer_will_jam.html
[ie_console_only_when_debug]: https://stackoverflow.com/q/7742781/3869533
[magic_story]: http://catb.org/jargon/html/magic-story.html
[go_evil_runtime_bug]:
  https://marcan.st/2017/12/debugging-an-evil-go-runtime-bug
[node_exporter]: https://github.com/prometheus/node_exporter
[bug_types]:
  https://opensourceforu.com/2010/10/joy-of-programming-types-of-bugs/
[cern_moon]:
  https://www.hanselman.com/blog/the-code-worked-differently-when-the-moon-was-full
[lep]: https://en.wikipedia.org/wiki/Large_Electron%E2%80%93Positron_Collider
[lhc]: https://en.wikipedia.org/wiki/Large_Hadron_Collider
[zune_bug]: http://bit-player.org/2009/the-zune-bug
[zune_wiki]: https://en.wikipedia.org/wiki/Zune_30
[vanilla_ice_cream]: https://www.cs.cmu.edu/~wkw/humour/carproblems.txt
[vapor_lock]: https://en.wikipedia.org/wiki/Vapor_lock
[snopes_vanilla]: https://www.snopes.com/fact-check/cone-of-silence/
[sw_folklore]: https://beza1e1.tuxen.de/lore/
[therac_25]:
  https://www.cs.columbia.edu/~junfeng/08fa-e6998/sched/readings/therac25.pdf
[therac_wiki]: https://en.wikipedia.org/wiki/Therac-25
[patriot_missile]: https://www-users.cse.umn.edu/~arnold/disasters/patriot.html
[boeing_787]:
  https://www.theregister.com/2015/05/01/787_software_bug_can_shut_down_planes_generators/
[boeing_faa]:
  https://www.engadget.com/2015-05-01-boeing-787-dreamliner-software-bug.html
[hercules_heisenbug]: http://esr.ibiblio.org/?p=1705
[hercules]: https://github.com/SDL-Hercules-390/hyperion
