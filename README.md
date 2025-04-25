# Create kind cluster
kind create cluster --name jklab6

# Add Helm repo and install NFS server
helm repo add nfs-ganesha-server-and-external-provisioner https://kubernetes-sigs.github.io/nfs-ganesha-server-and-external-provisioner/
helm repo update
helm install nfs-server nfs-ganesha-server-and-external-provisioner/nfs-server-provisioner \                                            
  --set persistence.enabled=true \
  --set persistence.size=10Gi \
  --set storageClass.name=nfs \
  --set storageClass.allowVolumeExpansion=true

# Apply PVC
kubectl apply -f pvc.yaml

# Apply the job that writes sample content
kubectl apply -f job.yaml

# Deploy nginx and expose it
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Port forward to access in browser
kubectl port-forward service/nginx-service 8080:80