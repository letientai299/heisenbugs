# Heisenbugs

[Heisenbug][heisenbug_catb] is accepted as the [hardest type of
bug][hardest_bugs], because, by definition, it's computer bug that disappears or
alters its characteristics when an attempt is made to study it. This is a
compiled list of such historical bugs.

To save your time, each bugs some with a short summary of what happens and the
root cause. But, for maximize effect, you should check the bug's link.

---

## [We can't send email more than 500 miles][500_miles]

Email server application was unintentionally downgraded. Some configurations for
the previous (newer) version are not applied, and was reset to zero, include the
timeout to connect to remote SMTP server. Hence, the server abort the connect
call after 3 milliseconds, which is only enough to send and receive packet
within ~558 miles.

## [A unit test which only failed on Sundays][unit_test_sunday]

A _faulty unit test_ for a custom date time parser that expect different result
on Sunday compare to other days of week. The test assume that the parser can
make a round trip for any input: parse and then format it again must yield the
original input. Unfortunately, one of the input is _overflowed_, and yield
different result once it's formatted.

## [OpenOffice can't print on Tuesdays][open_office_tuesday]

OpenOffice produces a postscript document before send it to [cups][ubuntu_cups] for
printing. The postscript document will contains a line like:

```
%%CreationDate: (Tue Mar 3 19:47:42 2009)
```

`cups` use [file][unix_file] to identify if the file is a printable format. And
`file` mislabels the postscript document as Erlang JAM file, because its
internal database of Erlang JAM magic entry is incorrect (space is not
escaped). The Erlang magic entry is:

```
4 string Tue Jan 22 14:32:44 MET 1991 Erlang JAM file - version 4.2
```

## [Webpack Doesn't Work on Monday (On Windows)][webpack_monday]

[webpack-cli][gh_webpack_cli] v3.3.4 has some changes to the code that show the
donation banner, to show it only on Monday. This is an attempt to make the
banner appears less frequently (as people previously complain about it). The
new implementation uses `process.getuid()`, which is not available on Windows,
causing webpack to crash only on Monday.

## [A file reliably cauases printer jam][printer_jam]

A hardware related The drum in that laser printer rotate every 3 seconds. If the
image from the file to be printed need more than 3 seconds to be renderd, the
drum will stop, and it will start again once next image is ready. The faulty
file took slightly longer than 3 seconds to render. So the drum need not only
need to stop, but also had to start again when it was not fully stopped. That
causes the printer jam.

## [Why does JavaScript only work after opening developer tools in IE once?][ie_console_only_when_debug]

The console object is only activated when the Dev Toolbar is opened. Prior to
that, calling the console object will result in it being reported as undefined.
After the toolbar has been opened, the console will exist (even if the toolbar
is subsequently closed), so your console calls will then work.

## [Debugging an evil Go runtime bug (2017)][go_evil_runtime_bug]

[node_exporter][node_exporter], a tool to export machine metric from prometheus,
crashes magically on just some machine. Further debugging show that it has
something to do with the kernel. Turn out that Go allocate very small stack (104
bytes at the time), which is not enough for the kernel.

---

## Relevant readings

Those stories are for strange things related to compute system, but can't be
categorize as bugs.

- [A story about magic][magic_story]: a switch that no body knows what it does,
  but the computer will crash if the swich is flipped.
- [Type of bugs][bug_types]: four types of bugs, named after popular
  scientists, with some interesting in the classification.

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
[open_office_tuesday]: https://bugs.launchpad.net/ubuntu/+source/cupsys/+bug/255161
[file_erlang_jam_bug]: https://bugs.launchpad.net/ubuntu/+source/file/+bug/248619
[unix_file]: https://en.wikipedia.org/wiki/File_(command)
[ubuntu_cups]: https://help.ubuntu.com/lts/serverguide/cups.html
[printer_jam]: https://nedbatchelder.com/blog/200811/print_this_file_your_printer_will_jam.html
[ie_console_only_when_debug]: https://stackoverflow.com/q/7742781/3869533
[magic_story]: http://catb.org/jargon/html/magic-story.html
[go_evil_runtime_bug]: https://marcan.st/2017/12/debugging-an-evil-go-runtime-bug
[node_exporter]: https://github.com/prometheus/node_exporter
[bug_types]: https://opensourceforu.com/2010/10/joy-of-programming-types-of-bugs/
