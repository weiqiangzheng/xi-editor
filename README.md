<h1 align="center">
  <a href="https://github.com/google/xi-editor"><img src="icons/xi-editor.png" alt="Xi Editor" width="256" height="256"/></a><br>
  <a href="https://github.com/google/xi-editor">Xi Editor</a>
</h1>

<p align="center"><em>(pronounced "Zigh")</em></p>

<h4 align="center">A modern editor with a backend written in Rust.</h4>

The xi editor project is an attempt to build a high quality text editor,
using modern software engineering techniques. It is initially built for
Mac OS X, using Cocoa for the user interface. There are also frontends for
other operating systems available from third-party developers.

Goals include:

* ***Incredibly high performance***. All editing operations should commit and paint
  in under 16ms. The editor should never make you wait for anything.

* ***Beauty***. The editor should fit well on a modern desktop, and not look like a
  throwback from the ’80s or ’90s. Text drawing should be done with the best
  technology available (Core Text on Mac, DirectWrite on Windows, etc.), and
  support Unicode fully.

* ***Reliability***. Crashing, hanging, or losing work should never happen.

* ***Developer friendliness***. It should be easy to customize xi editor, whether
  by adding plug-ins or hacking on the core.

Please refer to the [November 2017 roadmap](https://github.com/google/xi-editor/issues/437)
to learn more about planned features.

Screenshot (will need to be updated as syntax coloring and UI polish is added):

![xi screenshot](/doc/img/xi-mac-screenshot.png?raw=true)

## Getting started

This repository is the core only. You'll also need a front-end, from the list
below.

### Building the core

Xi targets 'recent stable Rust'. We recommend installing via [rustup](https://www.rustup.rs). The current minimum supported version is 1.20.

To build the xi editor from the root directory of this repo:

```
> cd rust
> cargo build
```

Here are some other front-ends in various stages of development:

* [xi-mac](https://github.com/google/xi-mac), the official macOS front-end.

* [fuchsia/xi](https://fuchsia.googlesource.com/xi), a front-end in Flutter for Fuchsia,
and might work on other Flutter targets.

* [xi-gtk](https://github.com/eyelash/xi-gtk), a GTK+ front-end.

* [xi-tui](https://github.com/little-dude/xi-tui), a text UI.

* [xi-electron](https://github.com/acheronfail/xi-electron), a cross-platform front-end based on web-technologies.

* [gxi](https://github.com/bvinc/gxi), a GTK+ front-end written in Rust.

* [xi-win](https://github.com/google/xi-win), an experimental Windows front-end written in Rust.

* [kod](https://github.com/linde12/kod), a terminal frontend written in Golang.

* [xi-qt](https://github.com/sw5cc/xi-qt), a Qt front-end.

The following are currently inactive, based on earlier versions of the front-end
protocol, but perhaps could be revitalized:

* [xi_glium](https://github.com/potocpav/xi_glium), an
experimental GL-based front-end in Rust.

* [XiEditorVS2015](https://github.com/Sineaggi/XiEditorVS2015), C#.

* [xi-android](https://github.com/adrientetar/xi-android), an Android frontend.

There are notes (I wouldn’t call it
documentation at this point) on the protocol at
[frontend.md](doc/frontend.md). If you're working on a front-end, feel free to
send a PR to add it to the above list.

## Design decisions

Here are some of the design decisions, and motivation why they should
contribute to the above goals:

* ***Separation into front-end and back-end modules***. The front-end is responsible for presenting the user interface and
  drawing a screen full of text. The back-end (also known as “core”) holds the file buffers and is
  responsible for all potentially expensive editing operations.

* ***Native UI***. Cross-platform UI toolkits never look and feel quite right. The
  best technology for building a UI is the native framework of the platform.
  On Mac, that’s Cocoa.

* ***Rust***. The back-end needs to be extremely performant. In particular, it
  should use little more memory than the buffers being edited. That level of
  performance is possible in C++, but Rust offers a much more reliable, and
  in many ways, higher level programming platform.

* ***A persistent rope data structure***. Persistent ropes are efficient even for
  very large files. In addition, they present a simple interface to their
  clients - conceptually, they're a sequence of characters just like a string,
  and the client need not be aware of any internal structure.

* ***Asynchronous operations***. The editor should never, ever block and prevent the
  user from getting their work done. For example, autosave will spawn a
  thread with a snapshot of the current editor buffer (the persistent rope
  data structure is copy-on-write so this operation is nearly free), which can
  then proceed to write out to disk at its leisure, while the buffer is still
  fully editable.

* ***Plug-ins over scripting***. Most text editors have an associated scripting
  language for extending functionality. However, these languages are usually
  both more arcane and less powerful than “real” languages. The xi editor will
  communicate with plugins through pipes, letting them be written in any
  language, and making it easier to integrate with other systems such as
  version control, deeper static analyzers of code, etc.

* ***JSON***. The protocol for front-end / back-end communication, as well as
  between the back-end and plug-ins, is based on simple JSON messages. I
  considered binary formats, but the actual improvement in performance would
  be completely in the noise. Using JSON considerably lowers friction for
  developing plug-ins, as it’s available out of the box for most modern
  languages, and there are plenty of the libraries available for the other
  ones.

## Current status

This is still a project in its early stages. The Mac build has basic editing
functionality (it was used to write this README), but looks very spare and
is still missing essentials such as auto-indent. At the moment, it’s expected
that its main community will be developers interested in hacking on a text
editor.

## Authors

The main author is Raph Levien.

## Contributions

We gladly accept contributions via GitHub pull requests, as long as the author
has signed the Google Contributor License. Please see
[CONTRIBUTING.md](CONTRIBUTING.md) for more details.

If you are interested in contributing but not sure where to start, there is
an active IRC channel at #xi on irc.mozilla.org. There is also a subreddit at
[/r/xi_editor](https://www.reddit.com/r/xi_editor/).

### Disclaimer

This is not an official Google product (experimental or otherwise), it
is just code that happens to be owned by Google.
