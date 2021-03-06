---

copyright:
  years: 2019, 2020
lastupdated: "2020-02-11"

keywords: IBM Blockchain Platform, blockchain

subcollection: blockchain

---

{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}
{:external: target="_blank" .external}

# Getting started with the upgrade tool
{: #enterprise-upgrade-tool}

The upgrade tool is not yet available. This draft documentation is provided as a preview for customers who are starting the upgrade process. Any important updates to this draft will be published in the [What's new page](/docs/blockchain/reference?topic=blockchain-whats-new#whats-new) prior to the release of the upgrade tool. Enterprise Plan networks that have been migrated to Fabric 1.4.3 will be able to see the **Upgrade tool** button on the **Overview** page of their Network Monitor when the tool is made available.
{:note}

You can use the upgrade tool to create new components on the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 side by side with your existing network. The nodes that are created by the tool are joined to the same channels and store the same ledger data as your peers and ordering nodes on Enterprise Plan.

The upgrade tool user interface guides you through a series of independent steps. Although you need to follow the steps in order, you do not need to complete the upgrade process in a single session. You can upgrade your network carefully and test your new nodes as they are created and sync with your existing network. Use the following steps to get started and learn more about how the upgrade process works.


## Before you begin

### Prerequisites
{: #enterprise-upgrade-tool-prerequisites}

- {{site.data.keyword.IBM_notm}} needs to upgrade your Enterprise Plan network to Fabric v1.4.3 and migrate your ordering service from Kafka to RAFT consensus before you can start using the upgrade tool. The upgrade tool will appear on your Network Monitor after those upgrades are complete.

### Considerations and limitations
{: #enterprise-upgrade-tool-considerations}

- You can use the upgrade tool to complete every step of the migration process without assistance from {{site.data.keyword.IBM_notm}}. If you encounter any problems while you are upgrading your network, you can open a support ticket. For more information, see [support](/docs/blockchain/reference?topic=blockchain-enterprise-upgrade#enterprise-upgrade-support).
- Migration is not supported for Starter Plan networks.
- Each organization that has a separate Enterprise Plan membership must use the upgrade tool to create nodes on {{site.data.keyword.blockchainfull_notm}} Platform 2.0. The founder of the network cannot upgrade all of the organizations and peers that are joined to your network.
- Review the [considerations for using {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/blockchain/reference?topic=blockchain-ibp-v2-deploy-iks#ibp-v2-deploy-iks-considerations) before you upgrade your network. The next generation of the platform has a different user interface and provides you with more control over the nodes of your network. In particular, you are responsible for managing your certificates and private keys, which are not stored on {{site.data.keyword.cloud_notm}}.
- It is helpful to become familiar with the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console before you start the upgrade process. See [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}}](/docs/blockchain/reference?topic=blockchain-ibp-v2-deploy-iks#ibp-v2-deploy-iks) to learn how to deploy an instance of the {{site.data.keyword.blockchainfull_notm}} Platform 2.0. You can then use the [Build a network tutorial](/docs/blockchain/reference?topic=blockchain-ibp-console-build-network#ibp-console-build-network) to learn how to use the console to operate your new network.
- You need to deploy the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 on a paid cluster of the {{site.data.keyword.IBM_notm}} Kubernetes service. For more information, see the [resource recommendations for paid clusters](/docs/blockchain/reference?topic=blockchain-ibp-v2-deploy-iks#ibp-v2-deploy-iks-resources-required-paid).
- After migration, the founder of the Enterprise Plan network (PeerOrg1) will manage the ordering service on the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 for all organizations on the network. For more information about operating an ordering service on {{site.data.keyword.blockchainfull_notm}} Platform 2.0, see [creating an ordering service](/docs/blockchain/reference?topic=blockchain-ibp-console-build-network#ibp-console-build-network-create-orderer) and [ordering node configurations](/docs/blockchain/reference?topic=blockchain-ibp-console-govern-components#ibp-console-govern-components-suggested-ordering-node-configurations).
- You must create peers on {{site.data.keyword.blockchainfull_notm}} Platform 2.0 that use the same state database (LevelDB or CouchDB) as your peers on Enterprise Plan. You cannot upgrade from peers that are running LevelDB to peers that use CouchDB.
- As part of having more control of your network on the {{site.data.keyword.blockchainfull_notm}} Platform 2.0, you need to take steps to ensure high availability and disaster recovery. For more information, review [High Availability on the {{site.data.keyword.blockchainfull_notm}} Platform 2.0](/docs/blockchain/reference?topic=blockchain-ibp-console-ha#ibp-console-ha).
- The ability to use a hardware security module (HSM) to store the private keys of your blockchain nodes is not yet available on {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}}.
- The ability to use CA replica sets to deploy a highly available Certificate Authority is not yet available on {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}}.
- You need to use the upgrade tool to allocate the resources that will be used by the Certificate Authorities, peers and ordering nodes that you will create on {{site.data.keyword.blockchainfull_notm}} Platform 2.0. The default CPU and memory allocations used by the upgrade tool are provided as guidelines based on the resources used by your nodes on Enterprise Plan. When you allocate resources to your nodes, you need to plan for future growth when your network adds additional blocks. You can use the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console to allocate additional CPU and memory to nodes after they are deployed. However, you cannot allocate additional storage to a deployed node.
- The {{site.data.keyword.blockchainfull_notm}} Platform 2.0 exposes RESTful APIs that allow you to manage your blockchain components. The APIs provided by the new platform are different than the Swagger APIs provided by Enterprise Plan, and you will need to update any applications or scripts that use the APIs to manage your network. For more information about the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 APIs, see [Building a network with APIs](/docs/blockchain/reference?topic=blockchain-ibp-v2-apis#ibp-v2-apis) and the [API reference](https://cloud.ibm.com/apidocs/blockchain).

## Opening the upgrade tool

After {{site.data.keyword.IBM_notm}} has completed upgrading your Enterprise Plan network from Fabric v1.1 to v1.4.3, you can find the upgrade tool on your Enterprise Plan Network Monitor. Go to the **Overview** page and click **Upgrade tool**. The tool opens in a separate URL of your browser. You can return to this URL to open the tool without using the Network Monitor. You can exit the tool after each step is complete without losing your work.

If you are not able to see the **Upgrade tool** button, your Enterprise Plan network needs to be upgraded to Fabric v1.4 or your ordering service needs to be migrated to RAFT.

## Create an instance of the {{site.data.keyword.blockchainfull_notm}} Platform 2.0

You need to deploy an instance of the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 that contains sufficient resources for your Enterprise Plan network. Deploying the platform is a two-step process:

1. You must create a cluster on {{site.data.keyword.cloud_notm}} with the {{site.data.keyword.IBM_notm}} Kubernetes service. It is recommended that you create a multizone cluster for high availability. If you create a multizone cluster, ensure that [`VLAN spanning`](/docs/infrastructure/vlans?topic=vlans-vlan-spanning#manage-vlan-spanning){: external} is enabled in your account.

2. After you create a Kubernetes cluster, you can deploy a service instance of the {{site.data.keyword.blockchainfull_notm}} Platform and link it to that cluster.

The **Deploy an instance of the {{site.data.keyword.blockchainfull_notm}} Platform 2.0** panel displays the CPU and memory that is used by your Enterprise Plan network. This information is provided to help you select the size of the Kubernetes cluster on {{site.data.keyword.cloud_notm}} that you must create. You need to select a cluster that has sufficient resources for your current deployment, with some overhead for high availability and network growth. A 4x16 Kubernetes three worker node cluster is the recommended minimum deployment. For more information about resource considerations, see [Paid clusters](/docs/blockchain/reference?topic=blockchain-ibp-v2-deploy-iks#ibp-v2-deploy-iks-resources-required-paid). You can also visit the [detailed pricing scenarios](docs/blockchain/reference?topic=blockchain-ibp-detailed-pricing#ibp-detailed-pricing) to learn more about the potential costs of various network configurations.

The panel also displays the region where your Enterprise Plan network is deployed. Deploying your instance of the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 in the same region as your Enterprise Plan network provides the best performance during migration.

When you are ready, you can click **Create {{site.data.keyword.blockchainfull_notm}} Platform 2.0 network** to deploy an instance of the new platform. You can also follow [Getting started with {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}}](/docs/blockchain/reference?topic=blockchain-ibp-v2-deploy-iks#ibp-v2-deploy-iks).

When you use the upgrade tool to create your peer and orderer nodes on the new platform, you have the ability to select the resources allocated to each node. The upgrade tool provides default values based on the resources used by your nodes on Enterprise Plan and recommended values. You can review the default resources provided for nodes on {{site.data.keyword.blockchainfull_notm}} Platform 2.0 for more context. While you can re-allocate the CPU and memory allocated to running nodes, you can only allocate storage when a node is first deployed.

| **Component** (all containers) | CPU**  | Memory (GB) | Storage (GB) |
|--------------------------------|---------------|-----------------------|------------------------|
| **Peer**                       | 1.1           | 2.8                   | 200 (includes 100GB for peer and 100GB for state database)|
| **CA**                         | 0.1           | 0.2                   | 20                     |
| **Ordering node**              | 0.35          | 0.7                   | 100                    |

{: caption="Table 1. Default resources for nodes on IBM Blockchain Platform 2.0" caption-side="bottom"}

## Upload connection information
{: #enterprise-upgrade-tool-connection-info}

You need to upload the connection information of your Enterprise Plan network and your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 service instance to the upgrade tool.

- In the **Enterprise Plan service credentials** dropdown, paste a set of service credentials from your Enterprise Plan network. You can create a set of service credentials by using the following steps:

  1. In your [{{site.data.keyword.cloud_notm}} resource list](https://cloud.ibm.com/resources){: external}, open your Enterprise Plan network.
  2. Click **Service credentials** from the left navigator.
  3. Click the **New credential** button on the **Service credentials** page to create a new credential.
    - Give the credential a name, for example, *Upgrade*.
    - You can leave the **Add inline configuration parameter** field blank.
    - Click the **Add** button.
  4. After the new credential is created, click **View credentials** under the **ACTIONS** header of this credential. The contents of the credential looks similar to the following example:

  ```json
  {
    "PeerOrg1": {
      "url": "https://ibp-ash-e2e.4.secure.blockchain.ibm.com:443",
      "network_id": "346980c1af02438ca16a569dadd9cd6f",
      "key": "PeerOrg1",
      "secret": "NsbTcBj6wOcbnHZ2DVNkE6r3R92wEPuS38nnR0c4g1Rb1LWcR1HizGhPUJ5Bp99a"
    }
  }
  ```

- Copy the service credentials of your new instance of the {{site.data.keyword.blockchainfull_notm}} Platform and paste them into the **{{site.data.keyword.blockchainfull_notm}} Platform 2.0 service credentials** window. You can create the service credentials by using the following steps:

  1. In your [{{site.data.keyword.cloud_notm}} resource list](https://cloud.ibm.com/resources){: external}, open the blockchain service instance that you created.
  2. Click **Service credentials** from the left navigator.
  3. Click the **New credential** button on the **Service credentials** page to create a new credential.
    - Give the credential a name, for example, *Upgrade*.
    - Select a role of **Manager**.
    - You can leave the  **Select Service ID** dropdown and the **Add inline configuration parameter** field blank.
    - Click the **Add** button.
  4. After the new credential is created, click **View credentials** under the **ACTIONS** header of this credential. The contents of the credential looks similar to the following example:

    ```json
    {
      "api_endpoint": "https://1088ac8563e44f5a92539d946733ad7e-optools.so01.blockchain.test.cloud.ibm.com",
      "apikey": "nvASKts6B6lMZGZUNTGVP7MLK2BujMnxz0plSPYaqc3R",
      "configtxlator": "https://1088ac8563e44f5a92539d946733ad7e-configtxlator.so01.blockchain.test.cloud.ibm.com",
      "iam_apikey_description": "Auto generated apikey during resource-key operation for Instance - crn:v1:staging:public:blockchain:us-south:a/9d46037caee397faa45c55e087d26baa:1088ac85-63e4-4f5a-9253-9d946733ad7e::",
      "iam_apikey_name": "auto-generated-apikey-c1981f5c-cff0-464f-9a78-ed2f52e24d1a",
      "iam_role_crn": "crn:v1:bluemix:public:iam::::serviceRole:Manager",
      "iam_serviceid_crn": "crn:v1:staging:public:iam-identity::a/9d46037caee397faa45c55e087d26baa::serviceid:ServiceId-774190c5-9c37-4f68-8572-8d6e2aabbc7e",
    }
    ```

## Upgrade Your Certificate Authorities

The first component that you create on the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 is a Certificate Authority for your peer and ordering service organization. You will use CAs on the new platform to create the new peer and ordering nodes. Because the identities that are created by new CA are recognized as part of your organization on Enterprise Plan, your new nodes are able to join your existing channels. The application, node, and admin identities that you created on Enterprise Plan are also registered with the new CA.

### Step One: Use the Certificate Authorities panel in the upgrade tool

In the **Upgrade Your Certificate Authorities** panel, you can find the Certificate Authorities of your peer organization on Enterprise Plan. For high availability, each organization has two CAs. You need to upgrade only one CA per organization.

If you are the founder of the network (PeerOrg1), you need to repeat these steps to upgrade one of the ordering organization CAs, in addition to a CA for your peer organization.

For each CA you want to upgrade, enter a **Display Name** for the new CA on {{site.data.keyword.blockchainfull_notm}} Platform 2.0. You will use this name to identify the CA in future steps. You also need to create the enroll ID and enroll secret of the CA Admin. **Save these values**. You can use these values to generate the CA admin identity and operate your CA. After you provide the required information, click **Start** to create the CA. The CA takes about five to ten minutes to start. If the creation of a CA fails, click on the overflow menu next to the CA and then click **Delete Certificate Authority**.

### Step Two: Operate your Certificate Authority on {{site.data.keyword.blockchainfull_notm}} Platform 2.0

After you upgrade the CA, you need to click **Export identity** in the upgrade tool to download the CA admin identity to a JSON file. You then need to use the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console to associate the admin identity with the new CA. Log in to your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console. Upload the JSON identity to the console wallet by clicking the wallet tab and then **Add identity** > **Upload JSON**. Then, use the nodes tab to view the CA that the tool created. Click the **Associate identity** button to associate the CA admin identity with the CA. For more information about your CA and the identities you will create, see [Creating and managing identities](/docs/blockchain/reference?topic=blockchain-ibp-console-identities#ibp-console-identities).

## Upgrade the ordering service

If you are the founder of the network, you need to use the tool to create ordering nodes on the {{site.data.keyword.blockchainfull_notm}} Platform 2.0. The new ordering nodes are part of your existing ordering service and will communicate with your nodes on Enterprise Plan by using Raft consensus. Raft consensus allows your new nodes to receive the ledger and channel data from Enterprise Plan.

After you upgrade the CA of the orderer organization, you can create ordering nodes on the new platform. The first step is to use the CA to create an orderer administrator identity and orderer organization MSP definition. You can use the orderer administrator to operate your ordering nodes by using the console UI, Fabric CLI tools, or the Fabric SDKs.

### Step One: Create an ordering organization admin and MSP definition

Under **Create an ordering organization admin and MSP definition**, select the ordering service CA that you created on the new platform. Then, provide a new enroll ID and secret for your orderer admin. When you click **Create**, the upgrade tool completes the following steps:

- Register a new orderer administrator with your CA by using the enroll ID and secret that you provide.
- Generate an administrator certificate and private key with the admin identity.
- Create an orderer organization MSP definition that contains the administrator signing certificate. You can see the MSP in the console UI by going to the organizations tab.

After you create the orderer organization administrator and the MSP definition, click **Export identity** to download the administrator private key and certificate. Open your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console and upload this identity to your console wallet. The upgrade tool imports the orderer organization MSP definition into the organizations tab of your console. For more information about your organization MSP and your organization administrator, see [Managing organizations](/docs/blockchain?topic=blockchain-ibp-console-organizations#ibp-console-organizations).

### Step Two: Create new ordering nodes

You can use the **Upgrade orderers** panel to create new ordering nodes. For each new ordering node, enter a **Display Name** that will identify the node on the {{site.data.keyword.blockchainfull_notm}} Platform 2.0. You also need to provide the **Enroll ID** and **Enroll secret** for the ordering node identity. Your node uses this identity to generate the certificate and keys it needs to operate on the network during deployment. Provide a different enroll ID and secret than the values that you used for the orderer admin. After you provide the required information, click **Start** to create the orderer. You can only create one new ordering node at a time, and need to create five ordering nodes on this panel before you can proceed.

Your ordering nodes store the blockchain portion of the ledger for each channel they support. Each node that you create on the new platform needs to download these blocks and sync with your existing ordering service before you can migrate another ordering node. The time that is required depends on the number of channels in your network and the number of blocks on each channel. You can use the [Node Logs](/docs/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-node-logs) from your orderers on {{site.data.keyword.blockchainfull_notm}} Platform 2.0 to see whether they are finished syncing with your ordering nodes on Enterprise Plan. If the creation of an ordering node fails, click on the overflow menu next to each node and then click **Delete Ordering Node**.

If you created a multizone cluster, you can use this panel to deploy an ordering node to a specific zone and ensure that your ordering service is spread across different zones for High Availability. This allows your ordering service to continue to process transactions in the event of a zone failure. While the upgrade tool uses an anti-affinity policy to deploy your ordering nodes to worker nodes with free resources, the anti-affinity policy cannot detect if the ordering nodes are being deployed to different zones. For more information about how to find the zones of your cluster, see [Creating a node within a specific zone](/docs/blockchain?topic=blockchain-ibp-v2-apis#ibp-v2-apis-zone).
{:note}

### Step Three: Operate your ordering service on {{site.data.keyword.blockchainfull_notm}} Platform 2.0

When you finish upgrading your ordering service, your ordering service consists of eight nodes, three on Enterprise Plan and five on the platform. The members of your network will be able to submit their transactions to any ordering node to get their transactions included in blocks and added to the ledger. Log in to your Platform 2.0 console and open your ordering service in the nodes tab. Click the **Associate identity** button to associate the orderer admin identity that you exported from the orderer tool. You can then use the console to operate your ordering service. For more information about operating an ordering service on {{site.data.keyword.blockchainfull_notm}} Platform 2.0, see [creating an ordering service](/docs/blockchain/reference?topic=blockchain-ibp-console-build-network#ibp-console-build-network-create-orderer) and [ordering node configurations](/docs/blockchain/reference?topic=blockchain-ibp-console-govern-components#ibp-console-govern-components-suggested-ordering-node-configurations).

## Upgrade your peers

After you upgrade your peer organization Certificate Authority, and your ordering service if you are the founder of the network, you can start upgrading your peers. The first step is to use the CA to create a peer administrator identity and an organization MSP definition. You can use the administrator identity to operate your peers by using the console UI, Fabric CLI tools, or the Fabric SDKs. The organization MSP definition allows you to deploy nodes and join or create channels on the {{site.data.keyword.blockchainfull_notm}} Platform 2.0. If you are the network founder and the operator of the ordering service, you need to create a new MSP and administrator for peer organization.

### Step One: Create a peer organization admin and MSP definition

Under **Create an organization admin and MSP definition**, select the peer organization CA that you created on the new platform. Then, provide a new **Enroll ID** and **Enroll secret** of your organization admin. When you click **Create**, the upgrade tool completes the following steps:

- Register a new organization administrator with your CA with the enroll ID and secret that you provide.
- Generate an administrator certificate and private key with the admin identity.
- Create an organization MSP definition that contains the administrator signing certificate. You can see the MSP with the console UI by going to the organizations tab.

After you create the administrator identity and the MSP, click **Export identity** to download the administrator private key and certificate. Open your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console and upload this identity to your console wallet. The upgrade tool imports the peer organization MSP definition into the organizations tab of your console. For more information about your organization MSP and your organization administrator, see [Managing organizations](/docs/blockchain?topic=blockchain-ibp-console-organizations#ibp-console-organizations).

### Step Two: Create new peers

You can then use the **Create peers** table to start migrating your peers to the new platform. For each Peer you want to upgrade, enter a **Display Name** for the new peer on {{site.data.keyword.blockchainfull_notm}} Platform 2.0. You also need to provide the **Enroll ID** and **Enroll Secret** for the peer identity. Your peer uses this identity to generate the certificate and keys that are required by the peer during deployment. After you provide the required information, click **Start**. It takes about three to five minutes to create a new peer. If the creation of a peer fails, click on the overflow menu next to each peer and then click **Delete Peer**.

If you created a multizone cluster, you can use this panel to deploy your peer to a specific zone and ensure that your peers are deployed in different zones. This ensures that your peers are available if there is a zone failure. While the upgrade tool uses an anti-affinity policy to deploy your peers to worker nodes with free resources, the anti-affinity policy cannot detect if the peers are being deployed to different zones. For more information about how to find the zones of your cluster, see [Creating a node within a specific zone](/docs/blockchain?topic=blockchain-ibp-v2-apis#ibp-v2-apis-zone).
{:note}

### Step Three: Operate your peers on {{site.data.keyword.blockchainfull_notm}} Platform 2.0

Log in to your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console and open your peer on the nodes tab. You can use the **Associate identity** pane to associate the peer admin identity that you exported from the tool with your peers. You can then use the console to operate your peer by joining it to new channels and installing smart contracts.

Your peers need to download the channel blocks from your ordering service and reconstruct the channel ledger before they can endorse transactions. You might need to wait for your peers to sync with the channel after they start. The time that is needed depends on the number of channels and the number of blocks on the channel. You can use the channel overview panel in the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console to check the number of blocks and the peer [Node Logs](/docs/blockchain?topic=blockchain-ibp-console-manage-console#ibp-console-manage-console-node-logs) to see whether your peers are in sync with your peers on Enterprise Plan.

## Migrating your smart contracts
{: #enterprise-upgrade-tool-smart-contracts}

The Enterprise Plan Network Monitor and the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console use different methods to install chaincode on your peers. As a result, the chaincode that is instantiated on your channels by the Network Monitor or the Swagger APIs cannot be invoked from peers on the new platform. You can use the upgrade tool to install a new version of the chaincode on your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 and Enterprise Plan peers that can be used across platforms. After you upgrade the chaincode running on your channels to the version installed by the upgrade tool, you can endorse transactions from either platform, allowing you to use the two networks side by side.

### Step One: Use the migrate chaincode panel in the Upgrade tool

On the **Migrate chaincode** panel, you can find the name and version of the chaincode that are instantiated on your Enterprise Plan channels. You can use this table to decide which chaincode you want to install on your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 network. **However, you need to have the chaincode on your local file system to use this panel**.

You can use the **Select Chaincode File** button to upload a chaincode from your local file system. Select the peers on the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 that you would like to install the chaincode on and then click **Migrate**. The upgrade tool installs the chaincode with a new version to identify the chaincode that was installed by the upgrade tool. This version is displayed in the upgrade tool UI (the old version with **_V2** appended). If you use an automated build process to package and install your chaincode, the version installed by the upgrade tool may disrupt your normal processes.

All channel members need to complete this step using the upgrade tool before you can move to the next step. You can skip this step if you used the Fabric SDKs to install and instantiate your chaincode.
{: important}

### Step Two: Upgrade to the migrated chaincode using the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console

In order to invoke the chaincode on your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 peers, you need to upgrade the chaincode that is instantiated on the channel to the version installed by the upgrade tool. Make sure that all of the members of the channel have used the upgrade tool to migrate the chaincode to the same chaincode version. This may require additional coordination if you frequently upgrade your chaincode using an automated process. When all of the members of your channel are ready, you can upgrade the chaincode by using the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console.

Open your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 console and navigate to the smart contracts tab. Scroll down to the **Instantiated smart contracts** table and find the migrated chaincode. Click **Upgrade** from the network menu on the right side of the row. The chaincode takes a few minutes to upgrade. For more information, see [Deploy a smart contract on the network tutorial](/docs/blockchain/reference?topic=blockchain-ibp-console-smart-contracts#ibp-console-smart-contracts). Only one network member needs to use the console to upgrade the chaincode. After you migrate the chaincode you can continue to upgrade your chaincode using the upgrade tool or the Fabric SDKs. However, you can no longer use the Network Monitor or the APIs to upgrade chaincode.

### Step Three: Connect to the smart contract with the SDK

After the chaincode has been upgraded on your channels, you can use the console to download a new connection profile for your application. To download a connection profile, navigate to the **Smart Contracts** tab. Click the overflow menu next to each instantiated smart contract. Click the **Connect with your SDK** button to download the connection profile. For more information, see [Connect with SDK](/docs/blockchain/reference?topic=blockchain-ibp-console-smart-contracts#ibp-console-smart-contracts-connect-to-SDK-panel). Your application needs to be updated to use service discovery in order to use the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 connection profile to submit a transaction. To use service discovery, you also need to add an anchor peer to your channels. Use the console to add one of your peers on the new platform as an anchor peer to each channel that you joined. For more information, see [Configuring anchor peers](/docs/blockchain?topic=blockchain-ibp-console-govern#ibp-console-govern-channels-anchor-peers).

## Removing upgraded nodes

If you want to remove upgraded nodes for any reason, such as a deployment failure, you can remove the nodes of your network using the **Delete Certificate Authority**, **Delete Ordering Node**, and **Delete Peer** buttons provided on the panel you used to create each node. These buttons will delete the node from the {{site.data.keyword.blockchainfull_notm}} Platform 2.0, remove any accompanying artifacts, and undo the changes to your organization on Enterprise Plan that were required to deploy the node. Because of the changes made to your Enterprise Plan network, you should use the upgrade tool to remove your network on the {{site.data.keyword.blockchainfull_notm}} Platform 2.0 instead of using the {{site.data.keyword.blockchainfull_notm}} Platform console.

## Complete upgrade

The upgrade tool stores the certificates and private keys that it used to deploy your {{site.data.keyword.blockchainfull_notm}} Platform 2.0 network on your cluster inside Kubernetes secrets. When you finish upgrading your Enterprise Plan network, you can delete all of the data that was created by the upgrade tool, including the certificates and keys, by clicking **Complete Upgrade**. **This action is final**. After you complete the upgrade, you can no longer use the tool to interact with your networks on Enterprise Plan or the {{site.data.keyword.blockchainfull_notm}} Platform 2.0. You can see an overview of the CAs, peers, and chaincode that you migrated to the new platform to help you decide whether you still need to use the tool. If the migration of any node failed, you need to delete that node using the panel that created it before you remove the upgrade tool.

Ensure that you click the **Export all identities** link provided by the upgrade tool to download your administrator identities before you click **Complete Upgrade**. Store the identities in a secure place. After you complete this step, you can operate your network only by using the admin identities that you downloaded.
{: important}

After you delete the tool, you can start to [Delete your Enterprise Plan network](/docs/blockchain/reference?topic=blockchain-enterprise-upgrade#enterprise-upgrade-overview-four).
