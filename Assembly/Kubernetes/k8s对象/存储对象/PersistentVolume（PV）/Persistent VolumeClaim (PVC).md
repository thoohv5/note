# Persistent VolumeClaim (PVC)

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