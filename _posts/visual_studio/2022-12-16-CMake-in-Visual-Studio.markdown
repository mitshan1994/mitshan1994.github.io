---
layout: post
title:  "CMake in Visual Studio"
categories: VisualStudio
---

### Some Common Configs
Here are some common configs in **launch.vs.json**:
* `currentDir`
* `type`: `exe` or `dll`
* `projectTarget`: The target of this group of configs. The value must match the name in the **Startup Item** dropdown.
* `name`: The description of the launch group settings.
* `project`: Project file. Normally "CMakeLists.txt" and don't need to change.

### Debug Working Directory: `currentDir`
After choosing the debug target (cmake target), choose `Debug -> Debug and Launch Settings for ...` to open config file **launch.vs.json**. A default launch setting will be created.

Add `currentDir` with directory string as value, to change the default debug working directory. 
