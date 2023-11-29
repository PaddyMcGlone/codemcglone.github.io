---
layout: post
title:  "Virtual networks, subnets, private endpoints and links"
date:   2023-11-29 17:00:00 +0000
---

## Virtual network (VNet)

>  “Azure virtual networks (Azure Vnet) plays an important role in building networks within the Azure infrastructure. It is a fundamental component of keeping your Azure resources in a private network where you can securely manage and connect to other external networks via the internet.” 
-Learning Microsoft Azure, O’Reilly media 

 

An Azure virtual network (Vnet) enables web applications to remain within a private network when hosted on the public cloud. Each Azure resource (compute/storage services) deployed within the vnet reside within a sub network (subnet). These subnets inbound and outbound connections to other resources both inside and outside of the vnet are controlled and filtered via the use of network security groups (NSG). 

 

> The public cloud refers to a form of cloud computing where compute and storage services are offered by a third-party provider. These services are owned and maintained by the third-party providers and are made available to the public over the internet. Examples of third-party providers are, Microsoft Azure, Amazon web services (AWS) and Google cloud platform (GCP). 

 

As per the description above, when deploying your web applications to shared infrastructure on the public cloud, virtual networks enable developers to keep their resources in an isolated and private network. When deployed to a resource group, a vnet enables resources to securely communicate with each other. A Vnet also offers the ability to connect to the public internet whilst denying this right to other resources to promote isolation. 

 

## Sub networks (subnets) 

When deploying a virtual network within your web application, it is possible to segment a virtual network into several isolated sub networks, also known as subnets. The process of breaking a network down into subnets empowers the developer to have more control over their web application networking. 

Components that would be typically sub divided into subnets are: 

- Web services 

- Azure serverless functions 

- Azure virtual machines 

- Databases (Azure SQL Server, Azure Cosmos) 

- Azure storage queue 

- Azure storage blob 

Each sub network within the virtual network is then allocated a portion of the virtual network address space for example, this address space could range from 10.1.0.0 to 10.1.34.0. 

Given this small example it is clear, using subnets allows developers to break large networks into small manageable chunks and gain greater control of the address range used within each sub network. 

 

## Network security groups 

Network security is applied to each subnet to protect from unauthorised access by controlling the network traffic which can enter or leave the sub network. When designing a virtual network, the development team must ensure that only legitimate network traffic is allowed within the network and child subnets. 

A network security group (NSG) is used to control both the inbound and outbound security rules and manage network traffic with a virtual network. The network traffic in a virtual network is then filtered between subnets using NSG’s. 

In a typical virtual network, an NSG is defined for each sub network. These network security groups are used to state which resources can output network requests to other resources within the virtual network. The security group is also used to inform a resource that they should expect network inbound traffic from a specific resource. 

This enables the developer to forensically control all the network traffic within the virtual network, applying a zero-trust policy to all network security groups via NSG security rules. One benefit of this approach is if one resource is compromised due to a known Azure resource vulnerability, this will have a limited impact on your network due to the NSG rules. 

 

Below is an example of an NSG in place within a virtual network. This NSG has been applied to a web service and states that the only inbound traffic that can enter the subnet can be from Azure Front door and all other inbound traffic is forbidden. This NSG allows the developer to lock down their web service to ensure it can only be reachable via Azure CDN/WAF, thereby eliminating potential security threats. 

![Alt text](/docs/assets/vnet1.png)

## Azure private endpoints and Azure private links 

Azure private endpoints and Azure private links enable web applications hosted within the public cloud the ability to access Azure PaaS services such as Azure storage and Azure SQL databases over a private endpoint within your virtual network. 

Azure private endpoints within virtual networks enable Azure PaaS services to be assigned an IP address on a sub network within the virtual network. Private endpoints enable the NIDA development team the ability to associate a PaaS service such as a storage account with a subnets private IP address. Thereby allowing resources within our virtual network the ability to connect to the PaaS service without leaving the boundaries of the virtual network 

Private links enable traffic between the virtual network and the Azure PaaS service to travel through the Microsoft backbone network and remove the need for public endpoints to the added to the service. It is also important to note that Azure private links do not with hold any customer data. 

 

 

  

 