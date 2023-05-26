# 2023-03-20 Azure dev deployment week long outage

## What

The login - and thus entire service - for a client Azure deploy was broken for a week, due a relying on a secret that must be created by a third-party supplier, with no process for updating.


2023-03 (all times UTC)

* -18 (Saturday) Client secret expires one year after creation
* -20 (Monday) 11:36 Steve reports "screen is freezing" after SSO and asks if others have issue
* -20 11:38 Martin reproduces 500 result from auth callback and starts investigating
* -20 12:14 Martin attempts to discover root cause by examining Azure portal logs, including enabling logging to disk
* -20 13:09 Martin reports the client secret expiry is the cause
* -20 13:19 Martin attempts emailing original ticket handler, and got automated response that they no longer worked for the supplier
* -20 14:16 Martin emails help desk as web interface has no path to request new secret
* -20 14:19 Ticket created for updating app registration by service desk
* -20 14:46 Martin chases help desk for contact details of anyone at supplier
* -20 14:57 Help desk informs Martin they are not allowed to give out supplier contact details
* -20 17:22 G requests expediting of request from O
* -22 (Wednesday) 12:44 O requests supplier looks at ticket
* -22 15:33 JJ reports adding down notice to Sharepoint intro page
* -23 (Thursday) 11:13 Communication with supplier around updating app registration starts
* -23 Investigation reveals the build must be re-run in order for the new secret in the vault to propagate to easyauth configuration
* -23 16:20 Devops pipeline build fails due to service connection having expired secret
* -23 Supplier indicates further follow up will require a new work item
* -24 (Friday) 10:14 Martin emails G to get a new ticket created for an updated build secret
* -27 (Monday) Examining secrets in Azure portal, only one previous secret is visible, despite two having being originally requested in email logs from previous year, Martin wonders if same secret was used for easyauth and for build pipeline
* -27 13:02 Martin changes deployment configuration successfully and re-runs build with new code
* -27 13:08 Martin reports on Teams site is back up and starts validating
* -27 Martin runs database migration to allow app to come back up
* -27 13:15 Martin reports on Teams incident resolved
* -27 13:25 Martin emails G to confirm resolution and indicate a new ticket is not after all required


## Why

Azure deployment was intended as a proof of concept, using a 'dev' resource group which had a planned migration to another environment. Since then goal and timeline for productionising has been revised, and proper domain was given to the site to enable early testing with some groups within the client organisation, which give the appearance of a more mature solution. This means both monitoring and processes around management of the environment are less robust than for the primary deployment in our own setup.

The method for obtaining the secret was not well understood by us or the supplier, we originally walked one of their support agents through the process in the Azure portal on a video call, and were then emailed the result. That secret had lifetime of one year (likely a default) and was included in one of the emails, but not added to a calendar or otherwise recorded.

There is no process in the client organisation for updating secrets. Neither Martin or JJ had ability to file the ticket under the options available, and had to go through helpdesk, and the ticket had to be filed as a "Non Standard Request". There were then multiple layers within the client organisation to navigate to get the supplier to look at this outside their 10 day window for work items not otherwise categorised. With chasing, it took three days for a response.

## Wonderings

There was no monitoring to detect the outage, it was only spotted when trying to access the service during working hours. Again due to having been created as a temporary deployment, and also restrictions from the client around our standard tooling. The web service exposes Prometheus metrics but we don't have a service principal would let us scrape, dashboard, and alert on them. We need to decide on an approach here as part of productionisation that would catch auth specific outages. Whitebox monitoring would not be sufficient.

The component serving the 500 response (the easyauth 'sidecar' component) did not return a response with any useful details as to why it was failing, nor log anywhere that could be seen from within the azure portal or by sshing into an app service. This made discovering why authentication was failing difficult. At some point, we may have to switch to handling oauth within our web server for better observability and flexibility on handling.

Our approach for infrequent non-automatable processes, such as awkward cert updates, is to have a documented playbook and a calendar invite reminder sufficiently in advance. Any deployment secrets required for Azure will need to use this framework.

For something that prevented all use of our service within the client, it was unclear what level of urgency was actually appropriate. Once it was clear that we would be unable to resolve the issue without going through another supplier, we did address the issue in own Azure environment, but that did not catch the build pipeline failure which meant additional delay once the secret was eventually provided.

Communication on what we were doing at each stage was reasonable, but also in the teams thread where the problem had initially been spotted, and did not have much overall visibility for the rest of the company.
