# aks-cluster

######################
# Create The Cluster #
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
    --node-count 3 \
    --node-vm-size Standard_D4s_v3 \
    --generate-ssh-keys \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3

az aks get-credentials \
    --resource-group $CLUSTER_NAME \
    --name $CLUSTER_NAME

#######################
# Destroy the cluster #
#######################

az group delete \
    --name $CLUSTER_NAME \
    --yes

kubectl config delete-cluster $CLUSTER_NAME

kubectl config delete-context $CLUSTER_NAME

kubectl config unset \
    users.clusterUser_$CLUSTER_NAME_$CLUSTER_NAME
