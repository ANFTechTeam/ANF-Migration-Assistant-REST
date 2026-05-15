# ANF-Migration-Assistant-REST

Example code and Postman collection for using the Azure NetApp Files (ANF) Migration Assistant feature via the REST API.

## Overview

This repository contains a Postman collection that provides a complete, step-by-step workflow for migrating volumes to **Azure NetApp Files (ANF)** using external replication. It demonstrates the full migration lifecycle—from initial setup through cutover—using the Azure NetApp Files REST API.

The collection is designed to be executed sequentially and handles authentication, volume provisioning, cluster/SVM peering, replication authorization, resync operations, and finalization.

## Prerequisites

- An active Azure subscription with ANF configured (NetApp account, capacity pool, and resource group).
- A service principal (App Registration) in Azure AD with sufficient permissions to manage ANF resources.
- Postman (recommended) or any REST client.
- Environment variables configured for authentication and resource targeting (see the included environment file).
- Source ONTAP cluster capable of external replication (typically ONTAP 9.15.1 or later).
- Network connectivity and appropriate peering/firewall rules between the source cluster and Azure.

## Getting Started

1. Clone the repository or download the files.
2. Open Postman and import the collection: `ANF Migrate.postman_collection.json`.
3. Import the environment: `Azure NetApp Files.postman_environment.json`.
4. Populate the environment variables with your Azure details:
   - `tenant`, `subscriptionId`, `resourceGroupName`, `accountName`, `poolName`
   - `appId` and `clientSecret` for the service principal
   - `location`, `serviceLevel`, `vol.subnetId`, `vol.usageThreshold`, `vol.protocolTypes`, etc.
   - `api-version` is set to `2025-07-01` (update if a newer stable version is available).
5. Start with the **Azure Authentication** folder (Step 0) to obtain a bearer token. The token is automatically stored in a global variable for subsequent requests.

## Collection Structure

### Setup
Establishes the replication relationship between the source (external ONTAP cluster) and the ANF target volume:

1. **Step 0. Authorization** — Obtains an OAuth2 bearer token.
2. **Step 1. Create Target Volume** — Provisions the destination ANF volume for external replication.
3. **Step 2. Check Volume Creation Progress** — Polls the async operation.
4. **Step 3. Issue Cluster Peer Request** — Initiates cluster peering.
5. **Step 4. Get Cluster Peering Command & Passphrase** — Retrieves the command and passphrase for the source side.
6. **Step 5. Authorize External Replication** — Authorizes the replication relationship.
7. **Step 6. Get Vserver Peering Command** — Retrieves the SVM peering command.

### Pre-Cutover and Cutover
Manages the final replication sync and cutover:

1. **Change Replication Schedule** — Adjusts schedule for cutover preparation.
2. **Issue Re-Sync Request** — Triggers a manual replication transfer.
3. **Issue Break Relationship Request** — Breaks the replication relationship.
4. **Get Async ID – Break** — Polls the async break operation.
5. **Issue Finalize External Replication** — Finalizes the setup post-cutover.
6. **Get Async ID – Finalize** — Polls the async finalize operation.

## Notes

- All requests use the Azure Resource Manager REST API (`https://management.azure.com`).
- The collection automatically extracts and stores tokens, async operation IDs, and other values via Postman scripts.
- For production use, review and customize volume parameters, replication schedules, and error handling.
- Refer to the official Azure documentation for the latest API details and supported features.

## Resources

- [Azure NetApp Files Documentation](https://learn.microsoft.com/azure/azure-netapp-files/)
- [Migrate volumes to Azure NetApp Files](https://learn.microsoft.com/azure/azure-netapp-files/azure-netapp-files-migrate-volumes)
- [Azure NetApp Files REST API reference](https://learn.microsoft.com/rest/api/netapp/)

## Contributing

Contributions are welcome! Please open an issue or submit a pull request with improvements to the collection or documentation.
