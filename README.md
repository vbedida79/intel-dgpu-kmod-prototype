# intel-dgpu-kmod-prototype
This prototype runs on OCP-4.10.16 cluster. The supplied intel-dgpu module CRD successfully builds the intel-dgpu driver container and creates a daemon set to install driver container on selected node.

After you login to the cluster with a user that has cluster-adminstrator permissions:

Create project named intel-dgpu using the following command:
`$oc new-project intel-dgpu`

Create a image stream using either approach a) or b) below:

a) Go to cluster console -> Builds -> ImageStreams -> Create ImageStream -> copy the following contents into the yaml and select create
kind: ImageStream
metadata:
  labels:
    app: intel-dgpu-driver-container
  name: intel-dgpu-driver-container
  namespace: intel-dgpu
spec: {}

b) `$oc apply -f intel_dgpu_image_stream.yaml`

Deploy the module CRD below to build driver container that contains all 5 graphics driver components.

`$ oc apply -f ooto_v1alpha1_module.yaml`

What happens behind the scenes when you run the create and apply the module CRD with the above command:

Module CRD looks for selected Intel dGPU nodes on cluster, pull out kernel version, if driver container image is not already built for that particular kernel version, it will build the driver container for this node's kernel version.

Module CRD will create a kaniko job to build the driver container based on the dockerfile contents specified in ooto_v1alpha1_module.yaml and push driver container image to the in-cluster registry image stream defined above. Finally, module CRD will create a daemon set to install driver container on selected Intel dGPU node. 

Check OCP webconsole -> Workloads-> Jobs: intel-dgpu-build for logs: There will be a pod associated with the job.
Check OCP webconsole -> Builds-> ImageStream -> intel-dgpu-driver-container to see that the driver container image has been successfully pushed.
Finally, check OCP webconsole -> Workloads -> DaemonSets to see the logs and status of driver container being installed on selected Intel dGPU node.

