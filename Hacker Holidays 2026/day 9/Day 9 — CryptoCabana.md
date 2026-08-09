In this room, the objective was to investigate a cloud-based application hosted on Azure and identify how it interacted with Azure Storage and Azure Key Vault. By following the application’s trust chain, it was possible to escalate from a publicly exposed storage token to privileged access over cloud resources, eventually recovering the hidden flag.

Since the target was hosted as an Azure Static Website, I started by inspecting the client-side files. Looking through **app.js**, I discovered a hardcoded **Shared Access Signature (SAS) token** used by the application to communicate with Azure Storage.

![](https://cdn-images-1.medium.com/max/1000/1*CdbYwtqTkrDk4YnTm9qgug.png)

Using the leaked SAS token, I enumerated the available storage containers instead of limiting myself to the one referenced by the application. During enumeration, I discovered an additional container named **vault**, which was not exposed anywhere in the web interface.

![](https://cdn-images-1.medium.com/max/1000/1*G4o5aqhygg8w2ST8Z1OIWA.png)

Inside the **vault** container, I found a file named **backup-service-account.json**. After downloading it, I discovered that it contained Azure **Service Principal** credentials, including the Client ID, Client Secret, Tenant ID, and the name of the Azure Key Vault.

![](https://cdn-images-1.medium.com/max/1000/1*jj9YjFA3netB9BUW7VeU_A.png)

![](https://cdn-images-1.medium.com/max/1000/1*iIgX2zvWHZUW-hO0dwlZuQ.png)

Using the discovered Service Principal credentials, I authenticated to Azure with the Azure CLI. After logging in successfully, I enumerated the secrets stored inside the Azure Key Vault.

The vault contained several secrets, including three flag fragments stored as separate secret shards.

![](https://cdn-images-1.medium.com/max/1000/1*NZ5Em5uvPEiaD-qEGN9jZQ.png)

One of the secret shards had already been rotated and only displayed a message instead of the real value. Following the room’s hint, I listed the previous versions of that secret and retrieved the older version, which still contained the original flag fragment.

![](https://cdn-images-1.medium.com/max/1000/1*cst1u0_W04utoyNEZBQzSQ.png)

![](https://cdn-images-1.medium.com/max/1000/1*oM37HZJYDN2b-KZlw3OKmw.png)

After collecting all three secret fragments, I combined them to reconstruct the complete flag.

**Flag:** THM{n0t_ur_k3ys_n0t_ur_c01ns!}