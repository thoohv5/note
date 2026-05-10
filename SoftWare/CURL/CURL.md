---
title: CURL
date: 2026-04-07
tags:
  - 软件工具
  - CURL
type: note
status: complete
---

## CURL

```bash
curl -w "%{time_namelookup}::%{time_connect}::%{time_starttransfer}::%{time_total}::%{speed_download}" "\n"
```

[CURL参数](CURL/CURL参数2016fa6b0e267747b4848f9748e0f2fec5.csv

```bash
curl -o /dev/null -s -w %{time_namelookup}::%{time_connect}::%{time_starttransfer}::%{time_total}::%{speed_download}"\n" 'https://patient-medication.medlinker.com/v1/service-pack/package-order/useItemList?packageOrderId=12244&_q=1589851853438' \
  -H 'Connection: keep-alive' \
  -H 'Accept: application/json, text/plain, */*' \
  -H 'User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1' \
  -H 'Origin: https://web.medlinker.com' \
  -H 'Sec-Fetch-Site: same-site' \
  -H 'Sec-Fetch-Mode: cors' \
  -H 'Sec-Fetch-Dest: empty' \
  -H 'Referer: https://web.medlinker.com/ih/user/servicePackageOrder' \
  -H 'Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,zh-TW;q=0.7' \
  -H 'Cookie: x_platform=web; platform=web; x_wechat_type=; UM_distinctid=17203902d5161e-09c84e16cb9174-30667d00-13c680-17203902d52a7a; gr_user_id=63e1e0cb-5096-4191-a07b-6e33313b64e6; sess=56399899__67A4HEMWUc3IEFhYlc6bkDrvk5FfGVd20CimSo9qSgpOxClVmn9q41uyNvZtszRa; ad6251cfbfcc4d7c_gr_session_id=7cf10252-2e96-4cf7-a10f-5ed91efa42a2; ad6251cfbfcc4d7c_gr_session_id_7cf10252-2e96-4cf7-a10f-5ed91efa42a2=true' \
  --compressed
```