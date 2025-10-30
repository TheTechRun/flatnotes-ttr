<p align="center">
  <img src="docs/logo.svg" width="300px"></img>
</p>
<p align="center">
  <img alt="Docker Pulls" src="https://img.shields.io/docker/pulls/dullage/flatnotes?style=for-the-badge">
</p>

**THIS IS A FORK WITH SUBDIRECTORIES ENABLED. See: [SUBDIRECTORY_SUPPORT.md](https://github.com/TheTechRun/flatnotes-ttr/blob/develop/SUBDIRECTORY_SUPPORT.md)**

A self-hosted, database-less note-taking web app that utilises a flat folder of markdown files for storage.

Log into the [demo site](https://demo.flatnotes.io) and take a look around. *Note: This site resets every 15 minutes.*

## Contents

* [Design Principle](#design-principle)
* [Features](#features)
* [Getting Started](#getting-started)
  * [Hosted](#hosted)
  * [Self Hosted](#self-hosted)
* [Roadmap](#roadmap)
* [Contributing](#contributing)
* [Sponsorship](#sponsorship)
* [Thanks](#thanks)

## Design Principle

flatnotes is designed to be a distraction-free note-taking app that puts your note content first. This means:

* A clean and simple user interface.
* No folders, notebooks or anything like that. Just all of your notes, backed by powerful search and tagging functionality.
* Quick access to a full-text search from anywhere in the app (keyboard shortcut "/").

Another key design principle is not to take your notes hostage. Your notes are just markdown files. There's no database, proprietary formatting, complicated folder structures or anything like that. You're free at any point to just move the files elsewhere and use another app.

Equally, the only thing flatnotes caches is the search index and that's incrementally synced on every search (and when flatnotes first starts). This means that you're free to add, edit & delete the markdown files outside of flatnotes even whilst flatnotes is running.

## Features

* Mobile responsive web interface.
* Raw/WYSIWYG markdown editor modes.
* Advanced search functionality.
* Note "tagging" functionality.
* Customisable home page.
* Wikilink support to easily link to other notes (`[[My Other Note]]`).
* Light/dark themes.
* Multiple authentication options (none, read-only, username/password, 2FA).
* Restful API.

See [the wiki](https://github.com/dullage/flatnotes/wiki) for more details.

## Getting Started

### Hosted

A quick and easy way to get started with flatnotes is to host it on PikaPods. Just click the button below and follow the instructions.

[![PikaPods](https://www.pikapods.com/static/run-button-34.svg)](https://www.pikapods.com/pods?run=flatnotes)


### Self Hosted

If you want to host flatnotes yourself, then the recommendation is to use Docker.

See: [DOCKER_INSTALL.md](https://github.com/TheTechRun/flatnotes-ttr/blob/develop/DOCKER_INSTALL.md)

If you're interested in contributing to flatnotes, then please read the [CONTRIBUTING.md](CONTRIBUTING.md) file.

## Sponsorship

## Thanks

A special thanks to 2 fantastic open-source projects that make flatnotes possible.

* [Whoosh](https://whoosh.readthedocs.io/en/latest/intro.html) - A fast, pure Python search engine library.
* [TOAST UI Editor](https://ui.toast.com/tui-editor) - A GFM Markdown and WYSIWYG editor for the browser.