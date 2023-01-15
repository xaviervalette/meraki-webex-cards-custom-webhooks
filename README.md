# Sending alerts to Webex directly from the Meraki Dashboard ?

 

Meraki Dashboard comes with built-in payloads for webhooks (Webex, Slack, Teams, …).

 

Those payloads are a ready to use solution for interacting with existing applications. But if you are using the given Webex payload (_<span style="text-decoration:underline;">Network-wide > Alerts > Webhooks > Add or edit webhook templates</span>_)...

 




<img width="612" alt="image" src="https://user-images.githubusercontent.com/28600326/212558777-4f633b54-884b-4e51-bd8a-ea8a4a6fc50f.png">

 


```json
{
 "markdown": "**{{alertType}}**\nAlert ID: {{alertId}}\nAlert level: {{alertLevel}}\nOccurred at: {{occurredAt}}\nOrganization name: [{{organizationName}}]({{organizationUrl}})\nOrganization ID: {{organizationId}}\nNetwork name: [{{networkName}}]({{networkUrl}})\nNetwork ID: {{networkId}}\nNetwork Tags: {{networkTags | join: ', '}}\nDevice name: {{deviceName}}\nDevice serial: [{{deviceSerial}}]({{deviceUrl}})\nDevice MAC: {{deviceMac}}\nDevice tags: {{deviceTags | join: ', '}}\nDevice model: {{deviceModel}}\n**Alert Data** {{ alertData | markdown_with_additional_formatting_for_webex }}\n"
}
```


 

 

...you will need to host a script listening for Meraki webhooks, and then posting a message to Webex API (via your [Webex Bot](https://developer.webex.com/docs/bots)).

 



<img width="777" alt="image" src="https://user-images.githubusercontent.com/28600326/212558795-b3045da9-05d5-4422-a26c-c520bc994f73.png">

 



<img width="612" alt="image" src="https://user-images.githubusercontent.com/28600326/212558833-d1e30a76-3325-48e0-a228-8183a07c3bff.png">



This workflow is correct, however for the Custom app, you will need to deal with:

 



* **Scripting** (Python, JavaScript, …)
* **Networking/Security** (NAT rules if you are hosting the application behind your ISP router, firewall rules if you are using a cloud VM with public IP, …)
* **Getting computing resources** (a Raspberry Pi at your place, a Cloud VM, Zapier, ...)
* **Billing** (hosting, cloud services, …)

 

**❗Setting up a self-hosted / cloud-hosted application can thus be time/money consuming.**

 


# Workaround

 

In this article, I am presenting a workaround, allowing you to post Webex messages directly from the Meraki Dashboard using a custom webhook payload:

 


<img width="455" alt="image" src="https://user-images.githubusercontent.com/28600326/212558807-6e5e9867-1d46-477f-824c-1b070a2c37cc.png">

 
<img width="612" alt="image" src="https://user-images.githubusercontent.com/28600326/212558840-0f049a80-b554-446e-86ae-e70fbfb7f437.png">

 

👍 Same result, but one less hop!

 

 


# What are custom webhook payload templates ?

 

Custom payload templates allow you to define the structure of webhooks sent from Meraki Dashboard. By using it, you will be able to decide which data you want to send, and how those data are formatted.

 

The structure will be written in [Liquid](https://shopify.github.io/liquid/basics/introduction/), both for the header and the body of the HTTP POST request.

 

 


# Create a new webhook payload template

 

A new webhook payload template can be created from the Meraki Dashboard (_<span style="text-decoration:underline;">Network-wide > Alerts > Webhooks > Add or edit webhook templates</span>_).

 




<img width="613" alt="image" src="https://user-images.githubusercontent.com/28600326/212558880-b9edc998-f81f-4fa4-8c76-3d9c9418d5ee.png">

 

 

 

Or via Meraki APIs: 

[https://developer.cisco.com/meraki/api-latest/#!create-network-webhooks-payload-template](https://developer.cisco.com/meraki/api-latest/#!create-network-webhooks-payload-template) 

 

 


# Custom webhook payload template requirements

 

The custom webhook payload template needs to:

 



1. Be sent to Webex API ([https://webexapis.com/v1/messages](https://webexapis.com/v1/messages))
2. Authenticate to the Webex API with the bot token
3. Include expected formated data
4. (Optional) Provide an Adaptive Card for better display 

 

For the given requirements, I am going to use the Meraki Dashboard.

 

 


## … Be sent to Webex API

_<span style="text-decoration:underline;">Network-wide > Alerts > Webhooks</span>_





<img width="613" alt="image" src="https://user-images.githubusercontent.com/28600326/212558895-e9795ed7-8d14-4ca3-a17c-accb90107c23.png">
![alt_text](images/image7.png "image_tooltip")

 

 

 

 


## … Authenticate to the Webex API with the bot token

_<span style="text-decoration:underline;">Network-wide > Alerts > Webhooks > Add or edit webhook templates</span>_

 


<img width="612" alt="image" src="https://user-images.githubusercontent.com/28600326/212558914-93332d23-03b7-41cb-8a1a-07b792c9adbf.png">

 

 


## … Include expected formated data

_<span style="text-decoration:underline;">Network-wide > Alerts > Webhooks > Add or edit webhook templates</span>_

 



<img width="612" alt="image" src="https://user-images.githubusercontent.com/28600326/212558926-430bb396-6b4c-45d3-a324-6205d4628c41.png">

 

Then, do not forget to apply the custom webhook template to your alerts (_<span style="text-decoration:underline;">Network-wide > Alerts</span>)_:

 



<img width="612" alt="image" src="https://user-images.githubusercontent.com/28600326/212558945-2436101e-9206-44e0-b9d4-304c12bc3a54.png">

 

 


## … (Optional) Provide an Adaptive Card for better display

_<span style="text-decoration:underline;">Network-wide > Alerts > Webhooks > Add or edit webhook templates</span>_

 

In the Liquid Body, you can keep the markdown format of the native Webex payload template, or you can specify an Adaptive Card for better display.

An example of card is available on the following GitHub repo : [https://github.com/xaviervalette/meraki-webex-custom-webhooks/blob/main/liquidBody.json](https://github.com/xaviervalette/meraki-webex-custom-webhooks/blob/main/liquidBody.json)

To create your own custom Adaptive Card, you can use the [Button and Card Designer Tool](https://developer.webex.com/buttons-and-cards-designer) of Webex.

 


<img width="612" alt="image" src="https://user-images.githubusercontent.com/28600326/212558994-4c9a1b6c-2a8a-45ed-840f-0a4fa8860467.png">

 

Here is the result! I hope it helps!
