# Setting keyboard layout

#### TL:DR
Set virtual console layout/keymap
```bash
localectl list-keymap
localectl set-keymap --no-convert br-abnt2
```
Configure Qtile layout/keymap
```python
nvim .conf/qtile/config.py
from libqtile.backend.wayland import InputConfig
wl_input_rules = {"type:keyboard":InputConfig(kb_layout:"br")}
```

### Justification
During Arch installation keyboard layout is pretty stragightforward, and very well documented here [Arch Wiki](https://wiki.archlinux.org/title/Linux_console/Keyboard_configuration) (arch wiki is your friend), but after deciding to use wayland for my GUI backend and QTile as my tiling manager, things diding look right (in this case I was using a brazillian - br-abnt2 keyboard). 

---
## Configuration
With the install media plugged in, one of the first things you can/should do is configure your locale. [Arch Wiki Installation Guide section 1.5](https://wiki.archlinux.org/title/installation_guide#Set_the_console_keyboard_layout_and_font) guides you through it.

Check available layouts, in case you don't know the naming scheme for yout particular keyboard, or in case you know at least one part of the layout/keymap name, pipe a grep with that part:
```
localectl list-keymap
```
or
```
locale list-keymap | grep -i <search_term>
```
> [!NOTE]
> These keymaps are available at /usr/share/kbd/keymap

Set your wanted layout by either editing the virtual console configuration file: 
```
<text_editor_of_choice> /etc/vconsole.config
KEYMAP=<keymap_code>
``` 
or use the locale control utility to do it:
```
localectl set-keymap --no-convert <keymap_code>
```
and _voilà_ you are done! (if you don't want to use a GUI, that is). 

I also wanted to use a tiling window manager, and I thought it would use the vconsole.conf file as default, but that is not the case. For QTile, the tiling wm I chose, it was also kind of straightforward (once I knew where to look, before that I tried a bunch of things). Since I'm using Wayland, X11/Xorg methods technically are not the way to go. Although I've seen some posts of it working, it didn't seem right. 

Configuration on QTile is available through the `config.py` file, created during set up ([QTile setup/installation](https://docs.qtile.org/en/stable/)). Meaning you just need to adjust the file calling the right API function, this is done using [`InputConfig`](https://docs.qtile.org/en/v0.24.0/manual/wayland.html#inputconfig), part of the wayland-specific backend library ([wayland inputs doc](https://docs.qtile.org/en/v0.24.0/_modules/libqtile/backend/wayland/inputs.html)):
```python
<text_editor_of_choice> <path_to_.config_dir>/qtile/config.py
# Add to import section, if not already present
from libqtile.backend.wayland import InputConfig
wl_input_rules = {
    "type:keyboard":InputConfig(kb_layout:"<layout_code>")
}
```
> [!NOTE]
> You could technically use the wayland backend core `set_keymap` command trhough the [qtile shell](https://docs.qtile.org/en/stable/manual/commands/shell/qtile-shell.html#qtile-shell) to achieve the same results ([back end core objects docs](https://docs.qtile.org/en/latest/manual/commands/api/backend.html#wayland-backend))

> [!WARNING]
> For my brazilian fellows, `kb_layout:"br"` should be fine, there was this one time where I entered the variant `kb_variant:"abnt2"`, and the system assumed the default `us` layout. Not sure it is a problem, but if setting the keyboard to `br-abnt2` does not work _**on Qtile**_ (not for vconsole config) try using just the `br` indicator