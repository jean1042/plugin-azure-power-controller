# plugin-azure-cloud-power-controller
![Microsoft Azure Cloud Services](https://spaceone-custom-assets.s3.ap-northeast-2.amazonaws.com/console-assets/icons/azure-cloud-services.svg)
**Plugin to control ```Start / Deallocate``` state and virtual machine scale sets for Azure Cloud Services; Virtual Machine, Virtual Machine Scale Sets, Azure DB**

Control ```Start / Deallocate``` State and autoscaling policies with reserved schedule from Azure Platform. 


Find us also at [Dockerhub](https://hub.docker.com/repository/docker/spaceone/google-cloud-power-controller)
> Latest stable version : 1.0

Please contact us if you need any further information. (<support@spaceone.dev>)

---
## Contents

* Table of Contents
    * [Authentication Overview](#authentication-overview)
        * [Virtual Machines](#virtual-machines)
            * [Instance](#compute-vminstance)
            * [Instance Group](#instance-group)
        * [CLOUD SQL](#cloud-sql)
            * [Instance](#instance)
     * [Running Sate Overview](#running-sate-overview)
        * [ComputeEngine VM Instance](#computeengine-vm-instance)
            * ['OFF' state Condition](#off-state-condition)
            * [Default Schema](#default-schema)
        * [Instance Group](#instance-group)
            * ['OFF' state Condition](#off-state-condition)
            * [Default Schema](#default-schema)
        * [CLOUD SQL](#cloud-sql)
            * ['OFF' state Condition](#off-state-condition)
            * [Default Schema](#default-schema)
       
---

### Authentication Overview
Registered service account on SpaceONE must have certain permissions to proceed action control 
Please, set authentication privilege for followings:


#### Virtual Machines

- ##### [Virtual Machines](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachines)
    - Scopes
        - https://docs.microsoft.com/en-us/rest/api/compute/virtualmachines/list
        - https://docs.microsoft.com/en-us/rest/api/compute/virtualmachines/poweroff
        - https://docs.microsoft.com/en-us/rest/api/compute/virtualmachines/start
        - https://docs.microsoft.com/en-us/rest/api/compute/virtualmachines/get
        - https://docs.microsoft.com/en-us/rest/api/compute/virtualmachines/deallocate
        
    - Actions
        - Microsoft.Compute/virtualMachines/read	
        - Microsoft.Compute/virtualMachines/write	
        - Microsoft.Compute/virtualMachines/start/action	
        - Microsoft.Compute/virtualMachines/powerOff/action	
        - Microsoft.Compute/virtualMachines/restart/action
        - Microsoft.Compute/virtualMachines/deallocate/action	
        - Microsoft.Compute/virtualMachines/instanceView/read
        - Microsoft.Compute/virtualMachines/extensions/read		
        
- ##### [Virtual Machine Scale Sets](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets)
    - Scopes
        - https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/list
        - https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/deallocate
        - https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/poweroff
        - https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/start
      
    - Actions
        - Microsoft.Compute/virtualMachineScaleSets/read	
        - Microsoft.Compute/virtualMachineScaleSets/start/action	
        - Microsoft.Compute/virtualMachineScaleSets/powerOff/action
        - Microsoft.Compute/virtualMachineScaleSets/restart/action	
        - Microsoft.Compute/virtualMachineScaleSets/deallocate/action
        - Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read		

### Running Sate Overview     

Plugin for Google Cloud to Check Running status
- Collecting ComputeEngine Instance State
- Collecting Cloud SQL Instance State
- Collecting Instance Group State


#### ComputeEngine VM Instance
- cloud_service_group: ComputeEngine
- cloud_service_type: Instance
- provider: google_cloud
###### 'OFF' state Condition:
- data.power_state.status = 'STOPPED'
###### Default Schema
~~~
"data": {
   "google_cloud":{
      "self_link":"https://www.googleapis.com/compute/v1/projects/google-cloud-project/zones/us-east1-b/instances/google-cloud-compute-instance-01"
   },
   "compute":{
      "instance_state":"RUNNING"
   },
   "power_state":{
      "status":"STOPPED"
   }
}
~~~
---
#### Instance Group
- cloud_service_group: ComputeEngine
- cloud_service_type: InstanceGroup
- provider: google_cloud
###### 'OFF' state Condition: 
 - data.instance_group_manager.target_size = 1 (will be deprecated)
 - data.size = 1
 - data.auto_scaler.autoscaling_policy.mode = 'OFF'
 - data.instance_group_type = 'STATELESS'
###### Default Schema
~~~
"data": {
   "instance_group_manager":{
      "target_size":1
   },
   "auto_scaler":{
      "recommended_size":1,
      "autoscaling_policy":{
         "max_num_replicas":5,
         "min_num_replicas":1,
         "mode":"OFF"
      }
   },
   "size": 1,
   "instance_group_type":"STATELESS",
   "self_link":"https://www.googleapis.com/compute/v1/projects/bluese-cloudone-20200113/zones/asia-northeast3-a/instanceGroups/instance-group-magnaged-zonal-mode-on"
}
~~~
---
#### Cloud SQL
- cloud_service_group: CloudSQL
- cloud_service_type: Instance
- provider: google_cloud
###### 'OFF' state Condition:
- data.power_state.status = 'STOPPED'
###### Default Schema
~~~
"data": {
   "self_link":"https://sqladmin.googleapis.com/sql/v1beta4/projects/google-cloud-project/instances/cloud-sql-instance-name",
   "power_state":{
      "status":"STOPPED"
   }
} 
~~~