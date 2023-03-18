---
layout: post
title:  "Useful Commands on Linux"
categories: linux
---

### List Listening Ports
```bash
sudo lsof -i -P -n | grep LISTEN

-i : internet openned files
-P : show port number, not resolve
-n : show ip, not resolve
```

```bash
sudo netstat -tulpn | grep LISTEN
```

### Show System Package Version
```
apt-cache show <package-name>

# Example.
apt-cache show cmake
```

