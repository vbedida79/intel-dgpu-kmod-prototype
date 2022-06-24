# intel-dgpu-kmod-prototype
This prototype runs on OCP-4.10.17 cluster. 

after you login the cluster with cluster-adminstrator user

Run dGPU drivers building 

`$ oc apply -f 0000-buildconfig.yaml`

Check OCP webconsole->builds->project: intel-dgpu-kmod->intel-dgpu-kmod-driver-build-1->log
for the building log
