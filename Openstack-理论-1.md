# 理论-1

[**https://releases.openstack.org**](https://releases.openstack.org)        **##查看具体某个组件的版本，基于哪个版本开发的**

1.  **Openstack是什么**
    

*   openstack是虚拟机、裸金属和容器的云基础架构
    
*   openstack可以控制整个数据中心的大型计算、存储和网络资源池
    
*   openstack通过一组相互关联的服务提供基础设施及服务（IaaS）解决方案
    
*   是模块化架构、无中心化架构、无状态化架构，每个模块都是单独部署的
    

1.  **工作原理**
    

*   openstack本身不会虚拟化资源，但会使用虚拟化资源构建云
    
*   openstack不是虚拟化，只是系统的控制面，不包括系统的数据面组件，如Hypervisor、存储、网络设备等
    

1.  **九大组件**
    

1.  **界面管理服务Horizon**
    

*   提供图形界面
    
*   提供最终用户的字符界面
    
*   可以做云的基础管理，可以创建用户、租户、配额
    

1.  **计算资源nova**
    

*   Controller+Compute（控制节点+计算节点）
    
*   计算节点就是KVM主机，也是物理机宿主机
    
*   控制节点是提供了各种组件的api接口，nova-api、cinder-api、glance-api，api是个访问入口
    

3.  镜像服务glance
    
4.  对象存储swift
    
5.   块存储cinder
    
6.  软件定义网络neutron
    
7.  编排服务heat
    

8.  计算服务，监控各个主机资源使用情况，计费ceilometer
    

9.  身份验证keystone
    

**四、部署工具**

1.  packstack
    
2.  devstack
    
3.  ansible
    
4.  开源方式部署
    
5.  TripleO