# Sterling OMS IV Integration

## Documentations

IV Documentation  
https://www.ibm.com/support/knowledgecenter/SSSMTK/landing/welcome.html

OMS 9.5 IV Integration  
https://www.ibm.com/support/knowledgecenter/en/SS6PEW_9.5.0/com.ibm.help.omsiv.integ.doc/c_OMIV_Integration.html

IV Api Documentation  
https://developer.ibm.com/api/view/inventoryvis-prod:inventory-visibility:title-IBM_Inventory_Visibility_APIs#doc

## Shipnode setup

Currently IV Shipnodes must be setup with the steps below. The first step only needs to setup once. 

***Currently, we **must** assign a new shipnode to a distribution group **before** adding the first inventory to the shipnode. Failure to do so will result in IV not able to provide availability for that shipnode.***


1. Setup Settings and Thresholds
   1. Setup Setting
        - Use [Postman Requests](#h2-postman) to check if "Setting" is set. If not, you can PATCH the "setting". You only need to do it once per tenant. 
   ![alt text](/README_images/setting_patch.PNG "Patch setting if needed")
   1. Setup Thresholds
         - Use [Postman requests](#h2-postman) to check if Threshold is set. If not, you can PUT threshold. You only need to do it once per tenant.
   ![alt text](/README_images/threshold_put.PNG "Put threshold if needed")
2. Create Shipnode. You can use the IV UI to create shipnode or you can create the shipnode in Postman
![alt text](/README_images/ui_shipnode_list.PNG "Put threshold if needed")
3. Assign the new shipnode to at least one Distribution Group. Easiest way would be doing this in UI.


## IV Quick Start with Postman <a name="h2-postman"></a>

1. Install postman.
2. Import the "IV Quick Start Collection"
3. "Edit" the newly imported collection to set the variables for the IV including the hostname, tenatID, client_id, client_secret. 
![alt text](/README_images/collection_checkVariables.PNG "set variables for the IV")
4. Create an environment for testing. You can name it anything that you like. It is for viewing and managing the bearer tokens for API calls.
4. Run an API. Any API.
4. Check if tokens are populated properly
   1. the **pre-request script** of the "IV Quick Start" colleciton gets bearer tokens for each API and sets them into environment
   1. Double check if environment now has value for variables such as _bearerToken_supplies, bearToken_configuration/shipNodes_
   ![alt text](/README_images/environment_checkVariables.PNG "check if tokens are set")
   1. if you need to reload tokens, you can unset/delete the environment variable **tokenExpirationTime**
   ![alt text](/README_images/tokenexp_update.PNG "delete token expiration time to reload tokens")
   
  
 
