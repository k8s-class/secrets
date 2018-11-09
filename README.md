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
