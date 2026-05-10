---

excalidraw-plugin: parsed
tags:
  - excalidraw

---
==⚠  Switch to EXCALIDRAW VIEW in the MORE OPTIONS menu of this document. ⚠==


## Text Elements
pg_ctl ^h0IGloOi

postmaster ^1c0v9Lsb

SIGTERM ^cnxnLGKT

Shutdown:SmartShutdown
connsAllowed:ALLOW_SUPERUSER_CONNS
pmState:PM_RUN ^JHGQXfc2

pmdie ^yEitkzh7

PostmasterStateMachine ^LmypHwx1

wait exit ^9MlVXBno

检查是否正在备份，如果没有正在备份时，设置connsAllowed值为ALLOW_NO_CONNS
如果connsAllowed值为ALLOW_NO_CONNS时，检查是否有客户端连接（BACKEND_TYPE_NORMAL），
如果没有客户端连接（BACKEND_TYPE_NORMAL），设置pmState值为PM_STOP_BACKENDS，开始退出后台backends，直到状态机pmState设置为PM_NO_CHILDREN，并调用ExitPostmaster退出。
如果有客户端连接（BACKEND_TYPE_NORMAL），本次不处理，在下次收到客户端连接退出后发送的SIGCHLD信号后继续处理。
如果connsAllowed值不为ALLOW_NO_CONNS时，本次不处理，在下次收到客户端连接退出后发送的SIGCHLD信号后继续处理。 ^QtL9Vxwk

%%
## Drawing
```json
{
	"type": "excalidraw",
	"version": 2,
	"source": "https://github.com/zsviczian/obsidian-excalidraw-plugin/releases/tag/2.0.3",
	"elements": [
		{
			"type": "rectangle",
			"version": 2,
			"versionNonce": 1759448773,
			"isDeleted": false,
			"id": "xG8-N-iD-YgjfUotnTwaK",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -104.7896728515625,
			"y": -1411.2413482666016,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 0.16162109375,
			"height": 0.161590576171875,
			"seed": 551732069,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [],
			"updated": 1701061717144,
			"link": null,
			"locked": false
		},
		{
			"type": "rectangle",
			"version": 127,
			"versionNonce": 1917055222,
			"isDeleted": false,
			"id": "9mAlvfyZxEliTPwsfhmHS",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -394.0647888183594,
			"y": -1462.1961364746094,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 85.96990966796875,
			"height": 77.89399719238281,
			"seed": 481253957,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "h0IGloOi"
				},
				{
					"id": "4nGEbcEP9j9lD7K1WMAbW",
					"type": "arrow"
				},
				{
					"id": "bohMWebDz0psnZAuNpM3X",
					"type": "arrow"
				}
			],
			"updated": 1701063053589,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 37,
			"versionNonce": 147937803,
			"isDeleted": false,
			"id": "h0IGloOi",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -382.5798034667969,
			"y": -1435.749137878418,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 62.99993896484375,
			"height": 25,
			"seed": 926353445,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1701061882558,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "pg_ctl",
			"rawText": "pg_ctl",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "9mAlvfyZxEliTPwsfhmHS",
			"originalText": "pg_ctl",
			"lineHeight": 1.25,
			"baseline": 18
		},
		{
			"type": "rectangle",
			"version": 191,
			"versionNonce": 1080818038,
			"isDeleted": false,
			"id": "Npi96BNNhfbveSPARzTjA",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -88.40689086914062,
			"y": -1414.2237014770508,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 85.96990966796875,
			"height": 77.89399719238281,
			"seed": 1433843269,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "1c0v9Lsb"
				},
				{
					"id": "4nGEbcEP9j9lD7K1WMAbW",
					"type": "arrow"
				}
			],
			"updated": 1701062878567,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 127,
			"versionNonce": 1607712069,
			"isDeleted": false,
			"id": "1c0v9Lsb",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -79.9719009399414,
			"y": -1400.2767028808594,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 69.09992980957031,
			"height": 50,
			"seed": 271689125,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1701061897716,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "postma\nster",
			"rawText": "postmaster",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "Npi96BNNhfbveSPARzTjA",
			"originalText": "postmaster",
			"lineHeight": 1.25,
			"baseline": 43
		},
		{
			"type": "arrow",
			"version": 160,
			"versionNonce": 455291318,
			"isDeleted": false,
			"id": "4nGEbcEP9j9lD7K1WMAbW",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -304.59002685546875,
			"y": -1424.9721984863281,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 212.75140380859375,
			"height": 51.014373779296875,
			"seed": 724991019,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"boundElements": [
				{
					"type": "text",
					"id": "cnxnLGKT"
				}
			],
			"updated": 1701062928570,
			"link": null,
			"locked": false,
			"startBinding": {
				"elementId": "9mAlvfyZxEliTPwsfhmHS",
				"gap": 3.504852294921875,
				"focus": -0.26130962237478406
			},
			"endBinding": {
				"elementId": "Npi96BNNhfbveSPARzTjA",
				"gap": 3.431732177734375,
				"focus": -0.25274758480297294
			},
			"lastCommittedPoint": null,
			"startArrowhead": null,
			"endArrowhead": "arrow",
			"points": [
				[
					0,
					0
				],
				[
					212.75140380859375,
					51.014373779296875
				]
			]
		},
		{
			"type": "text",
			"version": 8,
			"versionNonce": 1609702885,
			"isDeleted": false,
			"id": "cnxnLGKT",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -246.85430145263672,
			"y": -1411.9650115966797,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 97.27995300292969,
			"height": 25,
			"seed": 1862228811,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1701061917945,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "SIGTERM",
			"rawText": "SIGTERM",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "4nGEbcEP9j9lD7K1WMAbW",
			"originalText": "SIGTERM",
			"lineHeight": 1.25,
			"baseline": 18
		},
		{
			"type": "text",
			"version": 159,
			"versionNonce": 1509770090,
			"isDeleted": false,
			"id": "JHGQXfc2",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": 31.485181403882564,
			"y": -1280.4994590065696,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 409.77972412109375,
			"height": 75,
			"seed": 1975641765,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1701062991090,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Shutdown:SmartShutdown\nconnsAllowed:ALLOW_SUPERUSER_CONNS\npmState:PM_RUN",
			"rawText": "Shutdown:SmartShutdown\nconnsAllowed:ALLOW_SUPERUSER_CONNS\npmState:PM_RUN",
			"textAlign": "left",
			"verticalAlign": "top",
			"containerId": null,
			"originalText": "Shutdown:SmartShutdown\nconnsAllowed:ALLOW_SUPERUSER_CONNS\npmState:PM_RUN",
			"lineHeight": 1.25,
			"baseline": 68
		},
		{
			"type": "rectangle",
			"version": 353,
			"versionNonce": 1712219178,
			"isDeleted": false,
			"id": "yWgVEDwayi2TdHdF1KTFw",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -87.78137001141795,
			"y": -1283.1837673765244,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 85.96990966796875,
			"height": 77.89399719238281,
			"seed": 1655553194,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "yEitkzh7"
				},
				{
					"id": "x_PD0xx4xn78j9nv_68Qb",
					"type": "arrow"
				},
				{
					"id": "dQmseGEsJroKGPDnuWT1Q",
					"type": "arrow"
				}
			],
			"updated": 1701063033620,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 292,
			"versionNonce": 1807072054,
			"isDeleted": false,
			"id": "yEitkzh7",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -69.40638527020701,
			"y": -1256.736768780333,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 49.219940185546875,
			"height": 25,
			"seed": 2011469674,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1701062876049,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "pmdie",
			"rawText": "pmdie",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "yWgVEDwayi2TdHdF1KTFw",
			"originalText": "pmdie",
			"lineHeight": 1.25,
			"baseline": 18
		},
		{
			"id": "x_PD0xx4xn78j9nv_68Qb",
			"type": "arrow",
			"x": -47.38767198377843,
			"y": -1336.894738862009,
			"width": 0.2741403290719404,
			"height": 49.73026160037898,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 368266858,
			"version": 171,
			"versionNonce": 1316981494,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1701062928571,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					0.2741403290719404,
					49.73026160037898
				]
			],
			"lastCommittedPoint": [
				0.2741403290719404,
				49.73026160037898
			],
			"startBinding": null,
			"endBinding": {
				"elementId": "yWgVEDwayi2TdHdF1KTFw",
				"gap": 3.9807098851056253,
				"focus": -0.04815954385012739
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"type": "rectangle",
			"version": 430,
			"versionNonce": 1136683946,
			"isDeleted": false,
			"id": "dq-rZ4QQwDezyI0OJxIs7",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -88.18505283575547,
			"y": -1138.8078056104257,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 85.96990966796875,
			"height": 110,
			"seed": 694892202,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 3
			},
			"boundElements": [
				{
					"type": "text",
					"id": "LmypHwx1"
				}
			],
			"updated": 1701062941786,
			"link": null,
			"locked": false
		},
		{
			"type": "text",
			"version": 395,
			"versionNonce": 972968938,
			"isDeleted": false,
			"id": "LmypHwx1",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"angle": 0,
			"x": -82.34007450323594,
			"y": -1133.8078056104257,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"width": 74.27995300292969,
			"height": 100,
			"seed": 1021497706,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"boundElements": [],
			"updated": 1701062942464,
			"link": null,
			"locked": false,
			"fontSize": 20,
			"fontFamily": 1,
			"text": "Postma\nsterSt\nateMac\nhine",
			"rawText": "PostmasterStateMachine",
			"textAlign": "center",
			"verticalAlign": "middle",
			"containerId": "dq-rZ4QQwDezyI0OJxIs7",
			"originalText": "PostmasterStateMachine",
			"lineHeight": 1.25,
			"baseline": 93
		},
		{
			"id": "dQmseGEsJroKGPDnuWT1Q",
			"type": "arrow",
			"x": -44.462756323268025,
			"y": -1204.5524869976616,
			"width": 0.8820874763257507,
			"height": 66.257842092803,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1067717238,
			"version": 84,
			"versionNonce": 1866604906,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1701063033620,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-0.8820874763257507,
					66.257842092803
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "yWgVEDwayi2TdHdF1KTFw",
				"focus": -0.01981391612059607,
				"gap": 1
			},
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "bohMWebDz0psnZAuNpM3X",
			"type": "arrow",
			"x": -306.8119809918287,
			"y": -1426.6863685376723,
			"width": 85.77385179924238,
			"height": 69.15438565340901,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 838371818,
			"version": 426,
			"versionNonce": 1451092982,
			"isDeleted": false,
			"boundElements": [
				{
					"type": "text",
					"id": "9MlVXBno"
				}
			],
			"updated": 1701063061103,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-43.675037730823874,
					-69.15438565340901
				],
				[
					-85.77385179924238,
					-3.7068130030775137
				]
			],
			"lastCommittedPoint": null,
			"startBinding": {
				"elementId": "9mAlvfyZxEliTPwsfhmHS",
				"focus": 0.6871428103348238,
				"gap": 1.2828981585619204
			},
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "9MlVXBno",
			"type": "text",
			"x": -395.0169793549768,
			"y": -1508.3407541910813,
			"width": 89.05992126464844,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 665912682,
			"version": 10,
			"versionNonce": 328839862,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1701063060027,
			"link": null,
			"locked": false,
			"text": "wait exit",
			"rawText": "wait exit",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "bohMWebDz0psnZAuNpM3X",
			"originalText": "wait exit",
			"lineHeight": 1.25
		},
		{
			"id": "QtL9Vxwk",
			"type": "text",
			"x": 29.87625143004624,
			"y": -1135.7674010305695,
			"width": 1861.5191650390625,
			"height": 125,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 2053003754,
			"version": 150,
			"versionNonce": 1638506346,
			"isDeleted": false,
			"boundElements": null,
			"updated": 1701063213575,
			"link": null,
			"locked": false,
			"text": "检查是否正在备份，如果没有正在备份时，设置connsAllowed值为ALLOW_NO_CONNS\n如果connsAllowed值为ALLOW_NO_CONNS时，检查是否有客户端连接（BACKEND_TYPE_NORMAL），\n如果没有客户端连接（BACKEND_TYPE_NORMAL），设置pmState值为PM_STOP_BACKENDS，开始退出后台backends，直到状态机pmState设置为PM_NO_CHILDREN，并调用ExitPostmaster退出。\n如果有客户端连接（BACKEND_TYPE_NORMAL），本次不处理，在下次收到客户端连接退出后发送的SIGCHLD信号后继续处理。\n如果connsAllowed值不为ALLOW_NO_CONNS时，本次不处理，在下次收到客户端连接退出后发送的SIGCHLD信号后继续处理。",
			"rawText": "检查是否正在备份，如果没有正在备份时，设置connsAllowed值为ALLOW_NO_CONNS\n如果connsAllowed值为ALLOW_NO_CONNS时，检查是否有客户端连接（BACKEND_TYPE_NORMAL），\n如果没有客户端连接（BACKEND_TYPE_NORMAL），设置pmState值为PM_STOP_BACKENDS，开始退出后台backends，直到状态机pmState设置为PM_NO_CHILDREN，并调用ExitPostmaster退出。\n如果有客户端连接（BACKEND_TYPE_NORMAL），本次不处理，在下次收到客户端连接退出后发送的SIGCHLD信号后继续处理。\n如果connsAllowed值不为ALLOW_NO_CONNS时，本次不处理，在下次收到客户端连接退出后发送的SIGCHLD信号后继续处理。",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 118,
			"containerId": null,
			"originalText": "检查是否正在备份，如果没有正在备份时，设置connsAllowed值为ALLOW_NO_CONNS\n如果connsAllowed值为ALLOW_NO_CONNS时，检查是否有客户端连接（BACKEND_TYPE_NORMAL），\n如果没有客户端连接（BACKEND_TYPE_NORMAL），设置pmState值为PM_STOP_BACKENDS，开始退出后台backends，直到状态机pmState设置为PM_NO_CHILDREN，并调用ExitPostmaster退出。\n如果有客户端连接（BACKEND_TYPE_NORMAL），本次不处理，在下次收到客户端连接退出后发送的SIGCHLD信号后继续处理。\n如果connsAllowed值不为ALLOW_NO_CONNS时，本次不处理，在下次收到客户端连接退出后发送的SIGCHLD信号后继续处理。",
			"lineHeight": 1.25
		},
		{
			"id": "6tIItYqGOIYYbKkGRYegl",
			"type": "arrow",
			"x": -308.1219156657149,
			"y": -1427.5994174841676,
			"width": 66.63718249278446,
			"height": 68.99933786103202,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 1390461238,
			"version": 420,
			"versionNonce": 2028921258,
			"isDeleted": true,
			"boundElements": [
				{
					"type": "text",
					"id": "FLwhCoaN"
				}
			],
			"updated": 1701063037850,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-47.55863074100381,
					-68.99933786103202
				],
				[
					-66.63718249278446,
					-37.146776856973474
				]
			],
			"lastCommittedPoint": null,
			"startBinding": null,
			"endBinding": {
				"elementId": "9mAlvfyZxEliTPwsfhmHS",
				"gap": 2.550057866531631,
				"focus": -0.7319025797197036
			},
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "FLwhCoaN",
			"type": "text",
			"x": -400.2105070390429,
			"y": -1509.0987553451996,
			"width": 89.05992126464844,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 1180368438,
			"version": 11,
			"versionNonce": 2066277878,
			"isDeleted": true,
			"boundElements": null,
			"updated": 1701063037850,
			"link": null,
			"locked": false,
			"text": "wait exit",
			"rawText": "wait exit",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "6tIItYqGOIYYbKkGRYegl",
			"originalText": "wait exit",
			"lineHeight": 1.25
		},
		{
			"id": "BwLX8MAlEapuOxwhVCGnp",
			"type": "arrow",
			"x": -46.157351197794526,
			"y": -1204.7665354410801,
			"width": 231.75089518229169,
			"height": 179.79181463068176,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": {
				"type": 2
			},
			"seed": 329183466,
			"version": 654,
			"versionNonce": 2058653610,
			"isDeleted": true,
			"boundElements": [],
			"updated": 1701063025151,
			"link": null,
			"locked": false,
			"points": [
				[
					0,
					0
				],
				[
					-1.134810014204561,
					63.20752692945075
				],
				[
					-42.8598484848485,
					-43.313691110322
				],
				[
					40.34183386600375,
					76.2255859375
				],
				[
					44.70359108664769,
					-44.55325964725375
				],
				[
					85.478330669981,
					52.04826586174249
				],
				[
					140.75139825994313,
					78.59608043323851
				],
				[
					-1.761141690340935,
					67.92798591382575
				],
				[
					188.8910466974432,
					135.238554983428
				]
			],
			"lastCommittedPoint": [
				188.8910466974432,
				135.238554983428
			],
			"startBinding": {
				"elementId": "yWgVEDwayi2TdHdF1KTFw",
				"focus": 0.014932141843875348,
				"gap": 1
			},
			"endBinding": null,
			"startArrowhead": null,
			"endArrowhead": "arrow"
		},
		{
			"id": "qAFgpn9o",
			"type": "text",
			"x": -6.453760111146835,
			"y": -1261.8197950883339,
			"width": 10,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 358941162,
			"version": 2,
			"versionNonce": 1909554550,
			"isDeleted": true,
			"boundElements": null,
			"updated": 1701063023348,
			"link": null,
			"locked": false,
			"text": "",
			"rawText": "",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "center",
			"verticalAlign": "middle",
			"baseline": 18,
			"containerId": "BwLX8MAlEapuOxwhVCGnp",
			"originalText": "",
			"lineHeight": 1.25
		},
		{
			"id": "o8XNMv6s",
			"type": "text",
			"x": 51.08837264216743,
			"y": -1124.252249515418,
			"width": 10,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 277898218,
			"version": 2,
			"versionNonce": 1432699126,
			"isDeleted": true,
			"boundElements": null,
			"updated": 1701063072754,
			"link": null,
			"locked": false,
			"text": "",
			"rawText": "",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "",
			"lineHeight": 1.25
		},
		{
			"id": "A5zre5KR",
			"type": "text",
			"x": 29.270190823985615,
			"y": -1126.6764919396605,
			"width": 10,
			"height": 25,
			"angle": 0,
			"strokeColor": "#1e1e1e",
			"backgroundColor": "transparent",
			"fillStyle": "solid",
			"strokeWidth": 2,
			"strokeStyle": "solid",
			"roughness": 1,
			"opacity": 100,
			"groupIds": [],
			"frameId": null,
			"roundness": null,
			"seed": 55778486,
			"version": 2,
			"versionNonce": 1028844650,
			"isDeleted": true,
			"boundElements": null,
			"updated": 1701063075921,
			"link": null,
			"locked": false,
			"text": "",
			"rawText": "",
			"fontSize": 20,
			"fontFamily": 1,
			"textAlign": "left",
			"verticalAlign": "top",
			"baseline": 18,
			"containerId": null,
			"originalText": "",
			"lineHeight": 1.25
		}
	],
	"appState": {
		"theme": "light",
		"viewBackgroundColor": "#ffffff",
		"currentItemStrokeColor": "#1e1e1e",
		"currentItemBackgroundColor": "transparent",
		"currentItemFillStyle": "solid",
		"currentItemStrokeWidth": 2,
		"currentItemStrokeStyle": "solid",
		"currentItemRoughness": 1,
		"currentItemOpacity": 100,
		"currentItemFontFamily": 1,
		"currentItemFontSize": 20,
		"currentItemTextAlign": "left",
		"currentItemStartArrowhead": null,
		"currentItemEndArrowhead": "arrow",
		"scrollX": 320.33586978207524,
		"scrollY": 1646.1310373942058,
		"zoom": {
			"value": 0.9999999999999996
		},
		"currentItemRoundness": "round",
		"gridSize": null,
		"gridColor": {
			"Bold": "#C9C9C9FF",
			"Regular": "#EDEDEDFF"
		},
		"currentStrokeOptions": null,
		"previousGridSize": null,
		"frameRendering": {
			"enabled": true,
			"clip": true,
			"name": true,
			"outline": true
		}
	},
	"files": {}
}
```
%%