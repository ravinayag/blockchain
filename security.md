---

copyright:
  years: 2019, 2020
lastupdated: "2020-02-10"

keywords: security, encryption, storage, tls, iam, roles, keys

subcollection: blockchain

---

{:external: target="_blank" .external}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:term: .term}
{:tip: .tip}
{:pre: .pre}

# Security
{: #ibp-security}

{{site.data.keyword.blockchainfull}} Platform provides a scalable, highly reliable platform that helps customers deploy applications and data quickly and securely. This document provides information about securing your {{site.data.keyword.blockchainfull_notm}} Platform service instance, where the blockchain console runs, and best practices for securing the linked Kubernetes cluster where the blockchain nodes are deployed.
{:shortdesc}

## Security on the {{site.data.keyword.blockchainfull_notm}} Platform console
{: #ibp-security-ibp}

**Audience:** Tasks in this section are typically performed by **blockchain network operators**.  

Configuration of an {{site.data.keyword.blockchainfull_notm}} Platform network includes deploying the blockchain console and then linking it to either a new or existing customer Kubernetes cluster. The blockchain console can then be used to create blockchain nodes that reside in the customer Kubernetes cluster.   

Considerations include:
- [IAM (Identity and Access Management)](#ibp-security-ibp-iam)
- [Ports](#ibp-security-ibp-ports)
- [Key management](#ibp-security-ibp-keys)
- [Membership service providers (MSPs)](#ibp-security-ibp-msp)
- [Access control lists (ACLs)](#ibp-security-ibp-acls)
- [API authentication](#ibp-security-ibp-apis)

### IAM (Identity and Access Management)
{: #ibp-security-ibp-iam}


Identity and access management allows the owner of a console to control which users have access to the console and their privileges within it. The process for managing user access depends on whether the console is running in {{site.data.keyword.cloud_notm}} or {{site.data.keyword.cloud_notm}} Private.

####  IAM on {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}}
{: #ibp-security-ibp-iam-saas}

Identity and access management on {{site.data.keyword.cloud_notm}} is controlled by using the `IAM` service. Every user that accesses the blockchain console must have an IBMid account and be assigned an access policy in the IAM service. This policy determines what actions the user can perform within the console. Blockchain-specific permissions (for example, which users can create components) are based on the IAM roles which are mapped to blockchain permissions in this [Role to permissions mapping table](/docs/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-role-mapping).

When the {{site.data.keyword.blockchainfull_notm}} Platform console is provisioned, the email address of the {{site.data.keyword.cloud_notm}} account owner is set as the console administrator, known in IAM as the **Manager** role. All new access requests will be sent to this user. This console administrator can then grant other IBMid users access to the console by using the IAM UI. For more information about IAM on {{site.data.keyword.cloud_notm}}, see [What is IAM](/docs/iam?topic=iam-iamoverview#iamoverview){: external}. For the steps required to add new users, see [Adding and removing users from the console](/docs/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-add-remove).

#### IAM on {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud
{: #ibp-security-ibp-iam-icp}

Identity and access management for {{site.data.keyword.cloud_notm}} Private is built into the blockchain console and includes local console authentication and role management. As with the {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}}, the user who provisions the {{site.data.keyword.blockchainfull_notm}} Platform console in {{site.data.keyword.cloud_notm}} Private is designated as the console administrator, also known as the **Manager**. This administrator can then add and grant other users access to the console by using the **Users** tab. It is also possible to change the console administrator. Every user that accesses the console must be assigned an access policy with a user role defined. The policy determines what actions the user can perform within the console. Other users can be assigned with **Manager**, **Writer**, or **Reader** roles when a console manager adds them to the console. The definition of each role is provided in the [Role to permissions mapping table](/docs/blockchain?topic=blockchain-console-icp-manage#console-icp-manage-role-mapping). For the steps required to add new users, see [Managing users from the console](/docs/blockchain?topic=blockchain-console-icp-manage#console-icp-manage-users).

Note that for {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud, users can also be managed with [APIs](/docs/blockchain?topic=blockchain-console-icp-manage#console-icp-manage-users-apis).




### Ports
{: #ibp-security-ibp-ports}

If you are using a client application to send requests to the console, either via the blockchain APIs or the Fabric SDKs, the associated `HTTPS` console port needs to be exposed in your firewall.  

- In {{site.data.keyword.cloud_notm}} use the default port `443`.
- In {{site.data.keyword.cloud_notm}} Private, use the [Console port](/docs/blockchain?topic=blockchain-console-deploy-icp#icp-peer-deploy-quickstart-parms) that was specified when the helm chart was deployed. This external port will be in the range of `31210 - 31220`.




### Key management
{: #ibp-security-ibp-keys}

The {{site.data.keyword.blockchainfull_notm}} Platform network is based on trusted identities. Customers use the Certificate Authorities (CAs) in the console to generate the identities and associated certificates that are required by all members to transact on the network. The generated public and private keys are `ECDSA` with Curve `P256`. These keys are stored in the browser when they are added to the member's blockchain wallet so that the console can use them to manage blockchain components. However, it is recommended that customers export these keys and import them into their own key management system in case they clear their browser cache or switch browsers. Customers are responsible for the storage, backup and disaster recovery of all keys that they export.

Because these public and private key pairs are essential to how the {{site.data.keyword.blockchainfull_notm}} Platform functions, **key management** is a critical aspect of security. If a private key is compromised or lost, hostile actors might be able to access your data and functionality. Although you can use the {{site.data.keyword.blockchainfull_notm}} Platform console to generate private keys, those keys are not permanently stored by the console or the cloud (public keys, on the other hand, are stored in the browser and added to the member's wallet so that the console can use them to manage blockchain components), making customers ultimately responsible for the storage, backup, and disaster recovery of their keys.

If a private key is lost and cannot be recovered, you will need to generate a new private key by registering and enrolling a new identity with your Certificate Authority. You should also then remove and replace your signCert in any components or organizations where you had used the lost or corrupted identity. See [Updating an organization MSP definition](/docs/blockchain?topic=blockchain-ibp-console-organizations#ibp-console-govern-update-msp) for detailed steps.



You also have the option to bring your own certificates from your own non-{{site.data.keyword.blockchainfull_notm}} Platform CA when you create a peer node or ordering service. If you use your own certificates, you will need to manually build the peer or ordering service MSP definition file that includes those certificates and import the file into the console **Organizations** tab. See  [Using certificates from an external CA with your peer or ordering node](/docs/blockchain?topic=blockchain-ibp-console-govern-components#ibp-console-govern-third-party-ca) for the steps required.

### Membership Service Providers (MSPs)
{: #ibp-security-ibp-msp}

Whereas Certificate Authorities generate the certificates that represent identities, turning these identities into roles in the {{site.data.keyword.blockchainfull_notm}} Platform is done through the creation of Membership Service Providers (MSPs) in the console. These MSPs, which structurally are comprised of folders containing certificates, are used to represent organizations on the network. Every organization will have one and only one MSP and will always contain at least one **admincert** that identifies an administrator of the organization. When an MSP is associated with a peer, for example, it denotes that the peer belongs to that organization. Later on in the flow for creating a peer (or any node), this same administrator identity can be used to serve as the administrator of the peer as well. In order to perform some actions on a node, an administrator role is required. For example, to be able to install a smart contract on a peer, your public key must exist in the `admincerts` folder of the peer's organization MSP, which therefore makes you an administrator of the peer organization.

MSPs also identify the root CA that generated the certificates for the organization and any other roles beyond administrator that are associated with the organization (for example, members of a sub-organizational group), as well as setting the basis for defining access privileges in the context of a network and channel (e.g., channel admins, readers, writers).

MSP folders for organization members are based on a Fabric defined structure and are used by Fabric components. For more information about Fabric MSPs and their structure, see the  [Membership](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html){: external} and [Membership Service Provider Structure](https://hyperledger-fabric.readthedocs.io/en/release-1.4/membership/membership.html#msp-structure){: external} topics in the Hyperledger Fabric documentation. The Fabric CA establishes this structure by creating the following folders inside the MSP definition:

| MSP folder name | Description |
|-------------------------|-------------|
| `cacerts` | Contains the certificate of the root CA of your network.|
| `intermediatecerts` | These are the certificates of any intermediate CAs in your chain of trust (leading back to a root CA or CAs). Because intermediate CAs are currently not supported by the {{site.data.keyword.blockchainfull_notm}} Platform, this field will be blank.|
| `keystore` | Contains the private key that was generated alongside your public key. This key is used to generate signatures by creating a cryptographic hash that can be verified using the public key known to other users. This key is never shared, and you are responsible for securing and managing it. If this key becomes compromised, it can be used to impersonate your identity, making it crucial to keep this key safe. |
| `signCerts`| Contains the public key that was generated alongside your private key. It is also known as a "signing certificate" because it is used to verify signatures generated by other users.|
| Many Fabric components contain additional information inside their MSP folder. For example, a peer, includes the following folders: ||
| `admincerts` | Contains the admin certificates for this organization or component. |
| `tls` | Contains the TLS certs that you store for communicating with other network components. |
{: caption="Table 1. MSP folders" caption-side="bottom"}

Note that organization MSPs are stored in browser storage and must be exported to a local file system and secured by the customer.

### Access control lists (ACLs)
{: #ibp-security-ibp-acls}

Hyperledger Fabric allows for finer grained control over user access to specified resources through the use of access control lists (ACLs). ACLs allow access to a channel resource to be restricted to an organization and a role within that organization. The available set of ACLs are from the underlying Fabric architecture and are selected during channel creation or update. Note that access control lists are restrictive, rather than additive. If access to a resource is specified to an organization, it means that **only that organization** will have access to the resource. For example, if the default access to a particular resource is the Readers of all organizations, and that access is changed to the Admin of Org1, then only the Admin of Org1 will have access to the resource. Because access to certain resources is fundamental to the smooth operation of a channel, it is highly recommended to make access control decisions carefully. If you decide to limit access to a resource, make sure that the access to that resource is added, as needed, for each organization.

You can use the blockchain console to select which ACLs to apply to resources on a channel. See this information under [Creating a channel](/docs/blockchain?topic=blockchain-ibp-console-build-network#ibp-console-build-network-channels-create) for instructions on how to configure access control for a channel.

### API authentication
{: #ibp-security-ibp-apis}

In order to use the blockchain [APIs](https://cloud.ibm.com/apidocs/blockchain){: external} to create and manage network components, your application needs to be able to authenticate and connect to your network. The process to authenticate depends on the cloud where your network is provisioned:

- [API Authentication on IBM Cloud {{site.data.keyword.cloud_notm}}](/docs/blockchain?topic=blockchain-ibp-v2-apis#ibp-v2-apis-authentication)
- [API Authentication on {{site.data.keyword.cloud_notm}} Private](/docs/blockchain?topic=blockchain-console-icp-manage#console-icp-manage-create-api-key)



## Best practices for security on the customer Kubernetes cluster
{: #ibp-security-Kubernetes}

**Audience:** Tasks in this section are typically performed by **Kubernetes infrastructure managers**.

The {{site.data.keyword.blockchainfull_notm}} Platform console allows you to deploy and manage nodes on a Kubernetes cluster that you operate. The previous section addressed the security of the console. The following sections detail the best practices you can use to secure your Kubernetes cluster and the nodes of your network:

- [Kubernetes cluster security](#ibp-security-Kubernetes-security)
- [Network security](#ibp-security-Kubernetes-network)
- [Internet Ports](#ibp-security-Kubernetes-ports)
- [Cluster and Operating System (OS)](#ibp-security-Kubernetes-container-os)
- [Keys and cluster access information](#ibp-security-Kubernetes-keys)
- [Membership Service Providers (MSPs)](#ibp-security-kubernetes-msp)
- [Storage](#ibp-security-kubernetes-storage)
- [Data privacy](#ibp-security-kubernetes-privacy)
- [GDPR](#ibp-security-kubernetes-gdpr)

### Kubernetes cluster security
{: #ibp-security-Kubernetes-security}

The best place to start is to learn about the security features of the underlying Kubernetes infrastructure. The open source documentation provides a review of recommended practices for [securing a Kubernetes cluster](https://Kubernetes.io/docs/tasks/administer-cluster/securing-a-cluster/){: external}.


If you are using {{site.data.keyword.cloud_notm}}, you can review the topic on [Security for {{site.data.keyword.cloud_notm}} Kubernetes Service](/docs/containers?topic=containers-security){: external}.


With {{site.data.keyword.cloud_notm}} Private, **Pod Security Policies** provide a way to control the security level of the pods and containers in your cluster. The Pod Security Policy that is applied to the namespace on a cluster is the default security setting for any new pod that is created in that namespace. If you are using {{site.data.keyword.cloud_notm}} Private, review the [Security guide](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.2.0/user_management/admin.html){: external} for best practices.



### Network security
{: #ibp-security-Kubernetes-network}


{{site.data.keyword.cloud_notm}} and {{site.data.keyword.cloud_notm}} Private provide the underlying network, including the networks and routers, over which customers’ VLAN resides. The customer configures their servers and uses gateways and firewalls to route traffic between servers to protect workloads from network threats. Protecting your cloud network by using firewalls and intrusion prevention system devices is imperative for protecting your cloud-based workloads.





#### Firewall configuration
{: #ibp-security-Kubernetes-network-firewall}

If your configuration includes a network firewall, there are ports that need to be exposed to allow network traffic through. The following section describes how to find the ports that can be exposed and what they are for. If you are using {{site.data.keyword.cloud_notm}} Private, you need to ensure that the ports in the 'External port' column are exposed at your firewall all the way through to your cluster.

### Internet Ports
{: #ibp-security-Kubernetes-ports}

{{site.data.keyword.blockchainfull_notm}} Platform exposes certain ports associated with each node type that are used by client applications, for example, when sending transactions to peers or the ordering service. If you have configured a firewall, you will need to expose these ports in your network for the transactions to reach their destination and for the nodes to be able to respond to the requests. {{site.data.keyword.blockchainfull_notm}} Platform supports the `HTTPS` IP Security protocol.

#### {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}} ports
{: #ibp-security-Kubernetes-ports-ic}

| Port | Number | Description |
|----------|---------|-------|
| **CA ports**|||
| Fabric CA | 7054 |Port 7054 is exposed from the CA container. This is exposed all the way to the customer application via the service.|
| **Peer ports**|||
| Peer gRPC API | 7051 | This is the port used by the clients to communicate with the peer. |
| Smart contract containers | 7052 | This is the port used by the smart contract containers to communicate with the peer.|
| Peer Operations | 9443 | This port is used to get to the health check endpoint and to the metrics endpoint of the peer.|
| gRPC Web proxy | 7443 | This port is used by the UI to communicate with the peer via gRPC web APIs. |
| **Ordering service ports** ||||
| Orderer gRPC API | 7050 | This is the port used by the clients to communicate with the orderer. |
| Orderer Operations | 8443 | This port is used to get to the health check endpoint and to the metrics endpoint of the Orderer.|
| gRPC Web proxy | 7443 | This port is used by the UI to communicate with the orderer via gRPC web APIs.|
{: caption="Table 2. Ports on {{site.data.keyword.cloud_notm}}" caption-side="top"}

#### {{site.data.keyword.blockchainfull_notm}} Platform for Multicloud ports
{: #ibp-security-Kubernetes-ports-icp}

This section describes the external ports you can expose in your firewall to allow network traffic.  

You can use a `kubectl` command to identify the external ports on your cluster that need to be exposed. Log in from a terminal window by using the `cloudctl cli` so that you can issue a `kubectl` command and then grep for the NodePort as in the following example. Your output will differ based on the number of {{site.data.keyword.blockchainfull_notm}} Platform Certificate Authority (CA), peer, and ordering nodes you have, as well as the ephemeral ports that have been assigned in your environment. The internal ports are static, but the mapping to the external port changes based on your environment.  Note that in the example below, you need to replace `<namespace>` with the value of the namespace you used when you deployed your blockchain network.

```
kubectl get services --namespace=<namespace> | grep NodePort
```
{: codeblock}

And the results:

```
optools               NodePort    10.0.65.197    3000:31210/TCP
ibpca-service         NodePort    172.21.68.26   7054:30421/TCP
ibporderer-service    NodePort    172.21.32.19   7050:32434/TCP,8443:32216/TCP,8080:32220/TCP,7443:32284/TCP
ibppeer-service       NodePort    172.21.78.16   7051:32013/TCP,9443:30296/TCP,8080:30817/TCP,7443:31724/TCP
```

Using the results that are returned in this example, the external ports that can be exposed are:

| Internal Port |External port| Description |
|----------|---------|-------|
| **Console ports** |||
| 3000 | 31210| {{site.data.keyword.blockchainfull_notm}} Platform operational console, part of the console URL.|
| **CA ports**|||
|  7054 | 30421| CA NodePort |
| **Ordering service ports** |||
| 7050 | 32434 | Ordering node |
| 8443 | 32216 |Ordering node Healthcheck (https://xxx.xxx.xxx.xxx:32216/healthz)
| 8080 | 32220 | gRPC web proxy debug, required by the operational tools console |
| 7443 | 32284 | gRPC web, needed for the operational tools console |
| **Peer ports**|||
| 7051 | 32013| Peer |
| 9443 | 30296| Peer Operational Healthcheck (https://xxx.xxx.xxx.xxx:30787/healthz) |
| 8080 | 30817 | gRPC web proxy debug, required by the operational tools console |
| 7443 | 31724 | gRPC web, needed for the operational tools console |
{: caption="Table 3. Ports on {{site.data.keyword.cloud_notm}} Private" caption-side="top"}


### Cluster and Operating System security
{: #ibp-security-Kubernetes-container-os}

- **Sensitive data:** Cluster configuration data is stored in the `etcd` component of your Kubernetes master. Data in etcd is stored on the local disk of the Kubernetes master and is backed up to {{site.data.keyword.cos_full_notm}}. Data is encrypted during transit to {{site.data.keyword.cos_full_notm}}. 


- **Alpine Linux:** The Fabric Docker images use [Alpine Linux](https://alpinelinux.org/){: external}, which is a smaller, lighter, and therefore more secure version of Linux.




### Keys and cluster access information
{: #ibp-security-Kubernetes-keys}


| Type | Description |Storage | Access |
|------|-------------|--------|--------|
| **Internal IBM Service Keys** | Generated upon deployment of a component and used to access **nodes**, such as a CA, peer, or ordering service. | These keys are temporarily stored in a Kubernetes secret when code needs to access the component. The secret is deleted once the code has completed its task. | Access is from code via the Kubernetes secret. When the component is deleted, the key is also deleted. Developers of the service and support team members do not have have access to these keys. |
|**Customer Kubernetes cluster access information**| In order for the service components to access and manage the components that are deployed into this cluster, the service components generate a Kubernetes secret for **cluster** access. Each Kubernetes secret is tied to that specific customer cluster. | After generation, these secrets never leave the IBM Kubernetes cluster where the service components are running and are deleted whenever a customer deletes the associated cluster. | Accessed only by the service component code and only in response to customer requests to deploy or manage components via the blockchain console. Developers of the service do not have access to the information in the secrets. |
{: caption="Table 4. Types of keys and information used to access the customer Kubernetes cluster" caption-side="top"}

### Membership Service Providers (MSPs)
{: #ibp-security-kubernetes-msp}

Organizations in a blockchain network are represented by [MSP](/docs/blockchain?topic=blockchain-glossary#glossary-msp) definitions. You can use the blockchain console to add a new organization to the network by creating a new MSP definition in the **Organizations** tab. If you are the admin of an ordering service, you can use the console to add that organization MSP to a consortium using the **Ordering service** tab. Finally, if you are an administrator of a channel, you can add that organization to an existing channel so the organization can transact on the network using the **Channels** tab (this task might require the signature of other organizations). See the topic on [Join the consortium hosted by the ordering service](/docs/blockchain?topic=blockchain-ibp-console-build-network#ibp-console-build-network-add-org) for detailed steps.

### Storage
{: #ibp-security-kubernetes-storage}

When the blockchain console deploys a node, storage is dynamically provisioned from the default storageclass for that node from persistent storage. You have the option of encrypting the persistent volume but there may be some performance implications with encryption to consider.  

Customers are responsible for encrypting their own storage and the encryption must occur before any blockchain components are deployed to the cluster.
{: important}


The default persistent storage type is File storage, also known as Endurance storage. For more information about encryption on all of the {{site.data.keyword.cloud_notm}} storage options:
- [{{site.data.keyword.cloud_notm}} File storage Provider managed encryption-at-rest](/docs/infrastructure/FileStorage?topic=FileStorage-encryption){: external}
- [Portworx encrypting volumes](https://docs.portworx.com/reference/cli/encrypted-volumes/){: external}
- [{{site.data.keyword.cloud_notm}} data encryption and key management](https://www.ibm.com/cloud/garage/architectures/securityArchitecture/security-for-data#dataencryptionandkeymanagement){: external}


- For more information about encryption on {{site.data.keyword.cloud_notm}} Private:
[Encrypting volumes that are used by IBM Cloud Private](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_3.1.2/installing/fips_encrypt_volumes.html){: external} 

### Data privacy
{: #ibp-security-kubernetes-privacy}

When you have data privacy requirements, [Private Data collections](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#what-is-private-data){: external} provide a way to further isolate specific data from the rest of the channel members. The combination of the use of channels and private data offer various solutions for achieving [Data Residency](/docs/blockchain?topic=blockchain-console-icp-about-data-residency).

### GDPR
{: #ibp-security-kubernetes-gdpr}

In order to be GDPR compliant, it is recommended that you store PII data off chain.

## Hyperledger Fabric Security
{: #ibp-security-kubernetes-fabric}

Because {{site.data.keyword.blockchainfull_notm}} Platform is based on Hyperledger Fabric, you can leverage the secure features included in a Fabric network.  

- **TLS v1.2 communications** [TLS](https://www.ibm.com/support/knowledgecenter/en/SSFKSJ_7.1.0/com.ibm.mq.doc/sy10660_.htm){: external} is embedded in the trust model of Hyperledger Fabric. By default, server-side TLS is enabled for all communications using TLS certificates. TLS is used to encrypt the communication between your nodes and between your nodes and your applications. TLS prevents man-in-the-middle and session hijacking attacks. All {{site.data.keyword.blockchainfull_notm}} Platform components use TLS to communicate with each other.

- **Transaction integrity:** Fabric uses the cryptographic ECDSA standard to guarantee transaction integrity. With ECDSA, the transaction originator, such as a client application, signs their message using their private key, and the recipient, such as a peer, uses the originator’s public key to verify the authenticity of the message. If a transaction is tampered with on its way to the recipient, the signature verification fails.

- **Channels:** While Fabric channels are a powerful mechanism for partitioning and isolating data, they also provide the primary foundation for data privacy. Only members of the same channel can access the data of this channel. To ensure channel security, the channel configuration update policy is configured to define the number of channel operators who need to agree on the channel update request before a channel can be updated. Therefore, a clear process must exist for defining the organizations that are allowed to join and update channel.

- **Ledger data:** Implicit in the blockchain permissioned network is the notion that an agreed upon policy of multiple endorsers is required to sign (approve) a transaction before it can be committed to the ledger. Before any information can be added to the ledger, a clear and well established process for defining the ledger information must exist. Data on the ledger is immutable.

- **Smart contracts:** All smart contracts should be reviewed by channel members before they are installed and executed on peers in their organization. Likewise, all updates to smart contract should be reviewed before the updates are applied to a peer. See this topic on [Upgrading a smart contract](/docs/blockchain?topic=blockchain-ibp-console-smart-contracts#ibp-console-smart-contracts-upgrade) for the steps that are required.



