---
title: Persistent VolumeClaim (PVC)
date: 2026-04-07
tags:
  - 基础设施
  - K8s
type: note
status: incomplete
---

## Persistent VolumeClaim (PVC)

- 示例YAML
    
    ```yaml
    kind: PersistentVolumeClaim
    apiVersion: v1
    meradara:
    	name: myclain
    spec:
    	accessModes:
    		- Readwriteonce
    	resources:
    		recuests:
    			storage: 8Gi
    ```