---
title: 小米5 64GB高配版QCN
url: 271.html
id: 271
categories:
  - Linux
date: 2016-12-20 18:03:24
tags:
---

前段时间写过一个博文，是关于小米5 CM编译的文章，我在测试的时候不小心把IMEI弄丢了，导致手机没信号。

  

虽然在网上找到了米5 32GB版的qcn文件，可是我并没有刷，我担心不能用，所以就送修了，修好后回来第一件事就用了QPST备份了QCN，在这里分享一下QCN

下载：[MI5_64GB.qcn](http://ljkgpxs.xyz/?attachment_id=274)

这个QCN中的IMEI我已经修改了，请各位根据自己的IMEI自行修改，修改之前，要把IMEI转换格式，这里有一个Windows下用的格式转换器：[戳我下载](http://ljkgpxs.xyz/?attachment_id=272)  

第一个IMEI的位置开头是在qcn文件的2F48，qcn文件里我已经改成了11 11 11 11 11 11 11 11，请把转换后的IMEI填进去

第二个IMEI位置在1F698，我也改成了11 11 11 11 11 11 11 11，很容易辨别，把自己的第二个IMEI填进去

之后就可以用工具刷入了，建议参考这篇文章进行操作：[Xiaomi mi5 efs backup and restore methods](http://forum.xda-developers.com/mi-5/how-to/xiaomi-mi5-efs-backup-restore-methods-t3432730)