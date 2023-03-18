---
layout: post
title:  "Debian Install and Config"
categories: linux
---

Some notes of Debian, installation and configs. I use Debian 10 (Buster) version.

## Use Online Source, but not iso
Deselect cd sources in software & update center.

## Add sudoers
```
# su to root first.
# Replace "username" to yours.
usermod -aG sudo username
```

## Vmware Tools
```
apt-get install open-vm-tools
apt-get install open-vm-tools-desktop
```

## Gnome

### Config some behaviour/display
`gnome-tweaks` command can be used to config some behaviours of gnome, and manage installed extensions.

### Extensions
Right click on "Show Applications" icon can show some settings of extensions.

#### Dash to Dock
`https://micheleg.github.io/dash-to-dock/index.html`

`https://extensions.gnome.org/extension/307/dash-to-dock/`

