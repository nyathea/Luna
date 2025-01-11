# Luna - Bluesky Client (Based on Cawbird 1.5)

## Introduction

WIP

## Shortcuts

| Key                | Description                                                                                                                                 |
| :-----:            | :-----------                                                                                                                                |
| `Ctrl + t`         | Compose Tweet                                                                                                                               |
| `Back`             | Go one page back (this can be triggered via the back button on the keyboard, the back thumb button on the mouse or  `Alt + Left`)           |
| `Forward`          | Go one page forward (this can be triggered via the forward button on the keyboard, the forward thumb button on the mouse or  `Alt + Right`) |
| `Alt + num`        | Go to page `num` (between 1 and 7 at the moment)                                                                                            |
| `Ctrl + Shift + s` | Show/Hide topbar                                                                                                                            |
| `Ctrl + p`         | Show account settings                                                                                                                       |
| `Ctrl + k`         | Show account list                                                                                                                           |
| `Ctrl + Shift + p` | Show application settings                                                                                                                   |

When a tweet is focused (via keynav):

* `r`  - reply
* `tt` - retweet
* `f`  - favorite
* `q`  - quote
* `dd` - delete
* `Return` - Show tweet details
* `k` - Print tweet details to stdout (debug builds)

## Compiling Cawbird

### Preparation

Twitter clients need keys and secrets so that Twitter can go through the OAuth process. Cawbird used to ship
with a standard set of  but has always supported custom keys through schema settings. However, that wasn't convenient for software builds. Cawbird now supports:

a) per-user tokens and secrets (so each user uses a different "app")

b) configuration of the default token and secret at build time

What this means for developers is that you need to supply two build options with the key and the secret before the software will build. To stop them being trivially identifiable, we base64 encode them.

If you wish to build your own "micro-fork" of the application then register at [developer.twitter.com](https://developer.twitter.com/) and create an application. To base64 encode the keys you can run `echo -n "<value>" | base64`.

Reasons you may wish to micro-fork Cawbird:

* You want to package a modified version with your own patches (as IBBoard used to do with Corebird)
* You want to appear retro and use the old Corebird keys to confuse people
* You want to check whether you're getting hit by Twitter limiting *applications*
(not just users - all users of the app in aggregate) to 100,000 calls to some endpoints ([docs](https://developer.twitter.com/en/docs/twitter-api/v1/tweets/timelines/api-reference/get-statuses-mentions_timeline))

Alternatively you can continue using the default keys by using the values `VmY5dG9yRFcyWk93MzJEZmhVdEk5Y3NMOA==` and `MThCRXIxbWRESDQ2Y0podzVtVU13SGUyVGlCRXhPb3BFRHhGYlB6ZkpybG5GdXZaSjI=` respectively.

### Compiling

Cawbird uses the Meson build system rather than the more archaic autoconf/make combination. Building is as simple as:

```Bash
meson build -Dconsumer_key_base64=<your-base64-key> -Dconsumer_secret_base64=<your-base64-secret>
ninja -C build
```

If you want to test translations locally then you will also need to:

* pass `-Dlocaltextdomain=true` to meson
* run `ninja -C build cawbird-gmo` to generate the binary `.mo` translations
* run `for file in po/*.gmo; do mkdir -p "${file/.gmo}/LC_MESSAGES/"; cp $file "${file/.gmo}/LC_MESSAGES/cawbird.mo"; done` to put the `.mo` files in the expected places
* run `pushd build; ./cawbird; popd` to run Cawbird from the build directory
  * to test a different language, run `cd build; LANGUAGE=aa_BB ./cawbird` with the appropriate language code

Note that executing `build/cawbird` may result in one of the following errors:

```Bash
Settings schema 'uk.co.ibboard.cawbird' is not installed

Settings schema 'uk.co.ibboard.cawbird' does not contain a key named 'foo'
```

To fix this, use the schemas from the build directory:

```Bash
GSETTINGS_SCHEMA_DIR=build/data/ GSETTINGS_BACKEND='memory' build/cawbird
```

Cawbird installs its application icon into `/usr/share/icons/hicolor/`, so an appropriate call to `gtk-update-icon-cache` might be needed.

### Build Dependencies

* `gtk+-3.0 >= 3.22`
* `glib-2.0 >= 2.44`
* `json-glib-1.0`
* `sqlite3`
* `libsoup-2.4`
* `librest-0.7`
* `liboauth`
* `gettext >= 0.19.7`
* `vala >= 0.28` (makedep)
* `meson` (makedep)
* `gst-plugins-base-1.0` (for playbin, disable by passing `-Dvideo=false` to Meson)
* `gst-plugins-bad-1.0 >= 1.6` or `gst-plugins-good-1.0` (disable by passing `-Dvideo=false` to Meson, default enabled)
  * Requires the `element-gtksink` feature, provided by `gstreamer1.0-gtk` on Ubuntu-based systems,
    `gstreamer1-plugins-bad-free-gtk` on older RPM-based systems and `gstreamer1-plugins-good-gtk` on
    newer RPM-based systems
* `gst-libav-1.0` (disable by passing `-Dvideo=false` to Meson, default enabled)
* `gspell-1 >= 1.2` (for spellchecking, disable by passing `-Dspellcheck=false` to Meson, default enabled)

Note that the above packages are just rough estimations, the actual package names on your distribution may vary and may require additional repositories (e.g. RPMFusion in Fedora, or Packman in openSUSE)

If you pass `-Dvideo=false` to the Meson script, you don't need any gstreamer dependency but won't be able to view any videos.

## Copyright

Cawbird is released under the GPL v3 (or later) - see [COPYING](./COPYING) for more details.

The [video fallback image](data/symbolic/apps/cawbird-video-placeholder.svg) is a Creative Commons "CC-BY-3.0" licensed work [by Iris Li](https://thenounproject.com/term/film-reel/20395/).

## Social Media
* [@CawbirdClient on Twitter](https://twitter.com/cawbirdclient)
* <a rel="me" href="https://mastodon.social/@cawbird">@Cawbird on Mastodon.social</a>

## Footnotes

<a name="footnote1"></a>1: [home:IBBoard:desktop](https://build.opensuse.org/project/show/home:IBBoard:desktop)
