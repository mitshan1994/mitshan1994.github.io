---
layout: post
title:  "QScrollBar Stylesheets"
categories: qt
---

Here is an example of `QScrollBar` stylesheet, vertical bar is used:
```
QScrollBar {
  background: #F6F6F6;
  border: 1px solid #c6c6c6;
}
QScrollBar:vertical{
  margin-top: 20px;
  margin-bottom: 20px;
  width: 20px;
}
QScrollBar::handle {
  background: #549cff;
  border-radius: 8px;
  subcontrol-origin: border;
}
QScrollBar::sub-line:vertical {
  border: 0px solid grey;
  background: #549cff;
  height: 20px;
  subcontrol-position: top;
  subcontrol-origin: margin;
  border-image: url(hover.png) stretch;
}
QScrollBar::sub-line:vertical:hover {
  border-image: url(hover.png) stretch;
}
QScrollBar::sub-line:vertical:pressed {
  border-image: url(pressed.png) stretch;
}
QScrollBar::add-line:vertical {
  border: 0px solid grey;
  background: #0000ff;
  height: 20px;
  subcontrol-position: bottom;
  subcontrol-origin: margin;
}
```
