---
layout: post
title:  "CMake in Visual Studio Code"
categories: VisualStudio
---

Some notes on CMake extension in vs code.

### Debug Working Directory

Edit **.vscode/settings.json** under workspace folder, add the following to set debug working directory:

```
{
    ...
  
    "cmake.debugConfig": {
        "cwd": "${workspaceFolder}/bin/debug"
    }
}
```

