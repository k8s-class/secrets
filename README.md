# Secrets

### One of the first secrets you will want to create is a docker-registry secret for ACR.

```
az acr show --name epicacr --query loginServer --output tsv
export ACR_LOGIN_SERVER=$(az acr show --name epicacr --query loginServer --output tsv)
export ACR_REGISTRY_ID=$(az acr show --name epicacr --query id --output tsv)
export SP_PASSWD=$(az ad sp create-for-rbac --name epicacr --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)
export CLIENT_ID=$(az ad sp show --id http://epicacr --query appId --output tsv)
kubectl create secret docker-registry acr-auth --docker-server $ACR_LOGIN_SERVER --docker-username $CLIENT_ID --docker-password $SP_PASSWD --docker-email jim.knott@thinkahead.com
```

### Create a deployment and test the creds.
```
kubectl create image-pull-secrets.yaml
kubectl get pods
kubectl describe pod <pod-name>

vents:
  Type    Reason     Age   From                             Message
  ----    ------     ----  ----                             -------
  Normal  Scheduled  57s   default-scheduler                Successfully assigned default/acr-auth-example-7896dc5b7b-wg7jq to aks-default-24143415-2
  Normal  Pulling    55s   kubelet, aks-default-24143415-2  pulling image "epicacr.azurecr.io/basicnodeapp"
  Normal  Pulled     34s   kubelet, aks-default-24143415-2  Successfully pulled image "epicacr.azurecr.io/basicnodeapp"
  Normal  Created    31s   kubelet, aks-default-24143415-2  Created container
  Normal  Started    31s   kubelet, aks-default-24143415-2  Started container
```









### For basic secrets you need to encode them in base64.
```
To genereate secrets using a yaml file

$ echo -n "root" | base64
cm9vdA==

$ echo -n "password" | base64
cGFzc3dvcmQ=
```

### Then enter this data into the yaml file.

```
apiVersion: v1
kind: Secret
metadata:
  name: your-secrets-name
type: Opaque
data:
  username: cm9vdA==
  password: cGFzc3dvcmQ=
```
