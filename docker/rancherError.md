if error appear like : FATA[0136] [controlPlane] Failed to bring up Control  Plane: Failed to verify healthcheck: Failed to check <https://localhost:6443/healthz> for  service [kube-apiserver] on host

make sure /etc/docker/daemon.json exist and setting is right
{
   "insecure-registries" : ["your_ip:6443"]
}