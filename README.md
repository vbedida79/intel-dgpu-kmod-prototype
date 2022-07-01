# intel-dgpu-kmod-prototype
This prototype uses the KMMO (Kernel Module Management Operator), a Kubernetes operator that manages out of tree Kernel Modules via Module CRD API. In this repo, we provide a Module CRD to manage out of tree Intel dGPU kernel modules. This prototype runs on OCP-4.10.16 cluster. The supplied intel-dgpu module CRD successfully builds the intel-dgpu driver container and creates a daemon set to install driver container on selected node.

For more details on KMMO, refer to this link: https://github.com/qbarrand/oot-operator

Start by logging into an OCP cluster with a user that has cluster-adminstrator permissions:

1. Install KMMO: 

`$git clone -b insecure-image-check https://github.com/qbarrand/oot-operator.git`

`$make deploy IMG=quay.io/quba/kmmo:dd5a536`

2. Create project named intel-dgpu using the following command:

`$oc new-project intel-dgpu`

3. Create a image stream: 

`$oc apply -f intel_dgpu_image_stream.yaml`

4. Deploy the module CRD below to build driver container that contains all 5 graphics driver components.

`$ oc apply -f ooto_v1alpha1_module.yaml`

What happens behind the scenes when you apply the module CRD with the above command:

Module CRD looks for selected Intel dGPU nodes on cluster, and then pulls out kernel version for selected nodes. If driver container image is not already built for that particular kernel version, it will build the driver container for this node's kernel version.

Module CRD will create a kaniko job to build the driver container based on the dockerfile contents specified in ooto_v1alpha1_module.yaml and push driver container image to the in-cluster registry image stream created above. Finally, module CRD will create a daemon set to install driver container on selected Intel dGPU node. 

To Verify:

Check OCP webconsole -> Workloads-> Jobs: intel-dgpu-build for logs: There will be a pod associated with the job that builds the driver container.

Check OCP webconsole -> Builds-> ImageStream -> intel-dgpu-driver-container to see that the driver container image has been successfully pushed.

Finally, check OCP webconsole -> Workloads -> DaemonSets to see the logs and status of driver container being installed on selected Intel dGPU node.

