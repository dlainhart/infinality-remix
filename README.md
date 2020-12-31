
# Infinality Remix #

:warning: **Deprecation Warning**: On new versions of FreeType, the Infinality patches cause various rendering glitches and/or
segfaults (see issue #15). Only the `fontconfig-infinality-remix` package will be updated going forward for a limited time.
Please consider alternatives to Infinality as using older versions of `freetype` can be a security hazard.

If anyone with FreeType experience would like to maintain `freetype-infinality-remix` please get in-touch!

## Background ##
The Infinality set of patches for the FreeType font rendering infrastructure has provided improved font rendering on
Linux based desktops for over a decade. However, over time, porting the patches to the latest libraries has become
more and more difficult as the two codebases diverge. Often times entire versions of FreeType are left unpatched,
sometimes resulting in broken desktops and a poor user experience.

Until 4K monitors are the norm, good font smoothing should remain an important priority for the Linux desktop.

## Project Goals ##

The Infinality Remix repository has been created to address this problem. This repository is based off the
```infinality-ultimate-bundle``` and has the following goals:

  1. Porting, updating, and refactoring the Infinality patch set to remain compatible with upstream FreeType and Fontconfig.

  2. Cleaning up and modularizing the Fontconfig configuration files.

  3. Investigating and finding solutions to font rendering issues to improve the visual quality and consistency of text on Linux.


## What's Provided ##

The following packages are provided:

| Package | Description | Status |
|---------|-------------|--------|
| freetype2-infinality-remix | FreeType with the Infinality patch set. | Done |
| fontconfig-infinality-remix | Fontconfig with the Infinality patch set and base configuration. | Done |
| cairo-infinality-remix | Cairo with the Infinality patch set applied. | Done |
| infinality-remix-config-base-fonts | Infinality Remix Fontconfig tweaks for popular fonts found in the official repositories and AUR. | TBD |
| infinality-remix-config-web-fonts | Infinality Remix Fontconfig tweaks for popular Web fonts. | TBD |

## Installation ##

1. Remove all previous Infinality FreeType, Fontconfig, and Cairo packages and replace with the stock libraries.


2. Remove any broken Infinality symlinks in /etc/fonts/conf.d/.


3. Install the FreeType, Fontconfig, and Cairo packages, in that order:

   * From the AUR:

		```
		yay -S freetype2-infinality-remix fontconfig-infinality-remix cairo-infinality-remix
		```
   
   * From GitHub:
   
	    ```    
	    git clone https://github.com/pdeljanov/infinality-remix.git
	
	    cd infinality-remix/freetype2-infinality-remix
	    makepkg -sci
	
	    cd ../fontconfig-infinality-remix
	    makepkg -sci
	
	    cd ../cairo-infinality-remix
	    makepkg -sci
	    ```

4. Ensure your desktop environment's font rendering settings are correct:
    * Anti-aliased font rendering is **enabled**.
    * **RGB** sub-pixel rendering is enabled.
    * **Slight** hinting is highly-recommended.
    * Set your DPI correctly. Use a [DPI Calculator](https://www.sven.de/dpi/) to calculate a proper DPI for your system. For single monitor systems, you can let the DPI be auto-detected, but for multi-monitor systems you should set the DPI to that of your primary (or most used) monitor.


5. Logout and back in of your desktop session for changes to take effect immediately.


6. Verify through Xft that non-DE aware applications will use the recommended rendering settings. Run `xrdb -query | grep Xft`, and your
output should be similar to:

    ```
    Xft.antialias:  1
    Xft.autohint:   0
    Xft.dpi:        96
    Xft.hinting:    1
    Xft.hintstyle:  hintslight
    Xft.lcdfilter:  lcddefault
    Xft.rgba:       rgb
    ```
To change these settings, edit `/etc/X11/xinit/xinitrc.d/xft-settings.sh` for global changes. These settings will be overwritten by your local `~/.Xresources` file, so you must remove it, or make sure the settings are the same. *This setting only affects X11 applications.*

## Recommended Fonts ##

The following fonts are recommended and should be installed for a good experience:

 * Caladea (`ttf-caladea`)
 * Carlito (`ttf-carlito`)
 * DejaVu (`ttf-dejavu`)
 * Impallari Cantora (`aur/ttf-impallari-cantora`)
 * Liberation (`ttf-liberation`)
 * Noto (`noto-fonts`)
 * Open Sans (`ttf-opensans`)
 * Overpass (`otf-overpass`)
 * Roboto (`ttf-roboto`)
 * TeX Gyre (`tex-gyre-fonts`)
 * Ubuntu (`ttf-ubuntu-font-family`)
 * Courier Prime (`aur/ttf-courier-prime`)
 * Gelasio (`aur/ttf-gelasio-ib`)
 * Merriweather (`aur/ttf-merriweather`)
 * Source Sans Pro (`aur/ttf-source-sans-pro-ibx`)
 * Signika (`aur/ttf-signika`)


### Optional ###

The following fonts look excellent, but are not under permissive licenses.

 * Consolas
 * San Francisco Pro Display/Text

## Customization ##

For more precise font rendering tweaks, edit `/etc/X11/xinit/xinitrc.d/infinality-settings.sh`.

 * The `INFINALITY_FT` variable may be used to set a preset style. Options are listed within `infinality-settings.sh`. The `ultimate3` setting is recommended, but `ultimate5` gives a "macOS" look and pairs well with Apple's San Francisco system font.

 * The `INFINALITY_FT_*` variables may be used to create your own style or tweak the selected preset style.

Changes made here will only apply to new desktop sessions.

***Note:***  It is recommended you test your customizations with a QT or GTK application. See caveats below for more information.

## Notable Differences from Infinality Ultimate ##

 * Stem Darkening for TrueType fonts is enabled by default. Only QT-based applications enabled this option resulting in inconsistency with non-QT applications. It is now enabled by default for the `ultimate[1-5]` styles, for more consistent rendering across applications.
 * Autohint is force enabled by default in most circumstances.
 * Improved font substitutions using the `remix` set of FontConfig configuration files. The existing `free`, `ms`, and `combi` sets remain, but `remix` is the default.

## Just Want Infinality? ##

If you're an Infinality package maintainer and you just want the latest Infinality patch without this remix rubbish, simply apply the `0002-infinality-<VERSION>-<DATE>.patch` patch file.

## Caveats ##

 1. Chrome, and Chrome-based browsers ship with their own version of FreeType for web content. Only the browser shell (tab bar, address bar, menu, etc.) is rendered with the system's FreeType library. Therefore, only text in the browser shell will look different when using Infinality Remix.
 2. There are *a lot* of potential configuration files that affect font selection and rendering. Ideally you should make your settings as global as possible and remove any redundant user-local configuration files, or at the very least make sure they all agree on the same values, and with your DE. The following list are font configuration files going from user-local to global in scope:
    * `~/.config/fontconfig/fonts.conf`
    * `~/.config/fontconfig/conf.d/`
    * `~/.xsettingsd`
    * `~/.Xresources`
    * `/etc/fonts/conf.d/local.conf`
    * `/etc/fonts/conf.d/`
    * `/etc/X11/xinit/xinitrc.d/xft-settings.sh`
    * `/etc/X11/xinit/xinitrc.d/infinality-settings.sh`
 
## Contributing ##

Contributions are welcome on GitHub.

## Acknowledgements ##

This repository is based off of the work of the original Infinality author and Boohoomil who maintained the infinality-ultimate bundle.
