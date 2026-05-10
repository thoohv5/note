---
title: 时区（time zone）
date: 2026-04-07
tags:
  - 理论
  - Time
type: note
status: complete
---

## 时区（time zone）

- **DST：Daylight Saving Time**中文名叫“夏令时”，一般在天亮早的夏季人为将时间调快一小时，可以使人早起早睡，减少照明量，以充分利用光照资源，从而节约照明用电。中国**1986-1991**年实行夏令时，1992年废除。
- **CST：China Standard Time**（老外认为有其他含义，中国就这个缩写），中国标准时。
- **GMT：Greenwich Mean Time**，格林威治标准时，地球每15°经度 被分为一个时区，共分为24个时区，相邻时区相差一小时；例: 中国北京位于东八区。

### **Asia/Shanghai与GMT-8的区别**

开发和运维经常见到的Asia/Shanghai与GMT-8的是什么意思呢？

GMT-8是东八区，北京时间和东八区一致。

Asia/Shanghai是已地区命名的地区标准时，在中国叫CST。这个地区标准时会兼容历史各个时间节点。中国1986-1991年实行夏令时，夏天和冬天差1个小时，Asia/Shanghai会兼容这个时间段。

结论：1992年以后，在中国，GMT-8和Asia/Shanghai是一样的时间，1986-1991之间，夏天会有一小时时差。