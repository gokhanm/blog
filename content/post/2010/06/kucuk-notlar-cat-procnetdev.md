---
title: "Küçük Notlar - $cat /proc/net/dev"
date: 2010-06-20T10:52:27+03:00
url: 2010/06/kucuk-notlar-cat-procnetdev
categories:
  - notlar
tags:
  - $cat /proc/net/dev
  - küçük notlar
  - tcp paket boyutlarını öğrenme
---

``sh
$ cat /proc/net/dev
```

```sh
Inter-|   Receive                                                |  Transmit
 face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed
  p2p1:       0       0    0    0    0     0          0         0        0       0    0    0    0     0       0          0
    lo:   42800     516    0    0    0     0          0         0    42800     516    0    0    0     0       0          0
wlp2s0: 532613005  380157    0    0    0     0          0         0 25444897  228607    0    0    0     0       0          0
```

komutu ile  giden ve gelen paketlerin boyutlarını öğrenebilirsiniz.

