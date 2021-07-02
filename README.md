# aks-cluster

######################
  Create The Cluster 
######################

az login

az provider register -n Microsoft.Network

az provider register -n Microsoft.Storage

az provider register -n Microsoft.Compute

az provider register -n Microsoft.ContainerService

export CLUSTER_NAME=test

az group create \
    --name $CLUSTER_NAME \
    --location francecentral

#######Manual Node######

az aks create \
    --resource-group $CLUSTER_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --node-vm-size Standard_D4s_v3 \
    --generate-ssh-keys
    
    
    
####Autoscale Nodes#####

az aks create \
--resource-group $CLUSTER_NAME \
--name $CLUSTER_NAME \
--location francecentral \
--load-balancer-sku standard \
--kubernetes-version 1.19.11 \
--node-count 2 \
--node-vm-size Standard_D2s_v3 \
--node-osdisk-size 30 \
--zones {1,2} \
--enable-managed-identity \
--enable-addons monitoring \
--generate-ssh-keys \
--enable-cluster-autoscaler \
--min-count 2 \
--max-count 2

az aks get-credentials \
    --resource-group $CLUSTER_NAME \
    --name $CLUSTER_NAME


#################
  List Nodes AZ 
#################

kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"


#######################
 Destroy the cluster  
#######################

az group delete \
    --name $CLUSTER_NAME \
    --yes

kubectl config delete-cluster $CLUSTER_NAME

kubectl config delete-context $CLUSTER_NAME

kubectl config unset \
    users.clusterUser_$CLUSTER_NAME_$CLUSTER_NAME
