# Janus-tweaks

This holds the stuff i needed to tweak from [janus](https://github.com/carlhuda/janus) (not much) in order to have the
same experience i had with my previous installation [vimfiles](https://github.com/joseicosta/vimfiles), which some of us shared at work.

There are still some things to add (like better snippets for Rails), but
most of the stuff is already present in janus.

So, here's what I'm adding:

* Bufexplorer plugin, by default mapped to `<C-B>`.
* Railscast theme by Nick Moffitt, which supplies 256-color terminal equivalents for most of the colors.
* Local configuration files (will override ~/.vimrc.local and ~/.gvimrc.local if present).

The idea is to stick to janus as much as possible and most of the
stuff I will add may come from pull request to janus itself (yes, I'm anxious).
So my idea is that this repository goes from small to non-existent :)

## Installation

0. Install [janus](https://github.com/carlhuda/janus)
1. `git clone git://github.com/joseicosta/janus-tweaks.git`
2. `cd janus-tweaks`
3. `rake`
