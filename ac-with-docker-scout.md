---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: false
  pagination:
    visible: true
---

# ☁ AC with Docker Scout

<details>

<summary>Prerequisites:  </summary>

* Active Azure Subscription&#x20;
* Resource Group (Make sure region of resource group should match with ACR region and ACR region integration is not available for all region so refer official docs)
* ACR (Azure Container Registry)
* Event Grid with System Topic Deployed
* Event Hub Namespace
* Inside ACR, enable Token from Repository Permission Blade
* Docker Hub account, if you don’t have create New one
* [Docker socut](https://scout.docker.com/org/sivolko/settings/integrations) logged in with Docker hub account
* Locally Docker Installed, if using Laptop CLI
* In this lab I have taken [OWASP Juice Shop App](https://github.com/juice-shop/juice-shop) as container image to scan with Docker Scout.

</details>

## LAB

* Go to Azure Portal and search for container Registry and create one.  Just for testing I have allowed all public network access to registry from Networking blade, but in the production use private N/W

&#x20;

<figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

After successful ACR creation, you’ll get unique login server

<figure><img src=".gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

* Now create a registry token from Repository permission blade,this token will be required during Docker Scout configuration. If you are using ARM template provided by Docker to deploy ACR then you can skip this step.

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

* Now grab the container image of OWASP Juice APP, using docker pull command or else feel free to use own custom image.

```
  docker pull bkimminich/juice-shop
```

* Now Run this Image locally

```
docker run --rm -p 3000:3000 -d bkimminich/juice-shop 
```

You will see OWASP Juice Shop application can be accessible over port 3000. This is vulnerable application provided by OWASP for pen testing.

<figure><img src=".gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

* Now tag this image and push it to ACR using following command

```
 docker tag bkimminich/juice-shop dockerscoutshubhendu.azurecr.io/owasp:v1
```

{% hint style="info" %}
Replace my loginserver with yours.
{% endhint %}

* Push it to ACR

```
 docker push dockerscoutshubhendu.azurecr.io/owasp:v1
```

<figure><img src=".gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

Confirm from Azure portal Repositories blade

<figure><img src=".gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

## Now let’s integrate ACR with SCOUT for Vulnerability scan

### Docker Scout Integration

* Visit [Docker Scout Dashboard](https://scout.docker.com/org/sivolko/settings/integrations), and Login with docker account and select Azure Container Registry Option

<figure><img src=".gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

* Now Enter Registry Name, which is nothing but your login server from ACR, copy paste same

<figure><img src=".gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

after that, you will get ARM template to deploy, basically this ARM template will deploy a Event Grid system topic from Azure Service Events and Registry token\


<figure><img src=".gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

Make sure to deploy Docker Scout resources to the same resource group as the registry.Then review and create. After successful deployment go to your ACR–> Tokens from Repository Permission blade and copy token, then generate password. You can set password expiration date too. But remember to copy and save password locally, once window is close same password can’t be retrived. You need to regenerate.

<figure><img src=".gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

Copy the same Token/password put into Docker Scout Registry Token blade and click on enable integration.

<figure><img src=".gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

After 5 min, status on Docker Scout will change to connected

<figure><img src=".gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

### Now to start SCAN, select Image and activate Scan Analysis

<figure><img src=".gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

### Jump over image blade, there our ACR image is scanned with list of vulnerabilities.

<figure><img src=".gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

Jump over Vulnerabilities blade for more details

<figure><img src=".gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

### To mitigate vulnerabilities, jump to patch blade and follow the patch released by specific vendor.

<figure><img src=".gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

We can check all centralised details from overview blade too.

<figure><img src=".gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
we can deploy our own custom policies from Ploicies blade to set rules
{% endhint %}
