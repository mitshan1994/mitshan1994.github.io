---
layout: post
title:  "Transparent Rounded Corner of Widgets"
categories: qt
---

For customized widgets (derived from QWidget, QDialog, for example), we may want to set border radius and make rounded corner transparent. We can achieve this, by:
1. Override `paintEvent(...)`, add Qt style sheet support to customized widgets.
2. Set translucent attribute by: `setAttribute(Qt::WA_TranslucentBackground);`. Maybe we also need to set `Qt::FramelessWindowHint` window flags for dialogs.
3. Set border radius of the widget, using `border-radius` style sheet.

Then, we get a round-corner widget.
