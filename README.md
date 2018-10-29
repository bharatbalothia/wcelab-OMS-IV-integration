# Sterling OMS IV Integration

## IV Quick Start with Postman

1. Install postman.
2. Import the "IV Quick Start Collection"
3. "Edit" the newly imported collection to set the variables for the IV including the hostname, tenatID, client_id, client_secret. 
![alt text](/README_images/collection_checkVariables.PNG "set variables for the IV")
4. Create an environment for testing. You can name it anything that you like. It is for us to keep the bearer tokens for API calls.
4. Use "Runner" to run the "10 Get Bearer Tokens" folder. 
   1. ***Select the environment that you created or selected in the previous setp*** 
   1. ***Check the "Keep variable values" checkbox to save the tokens into the environment***
   ![alt text](/README_images/runner_setVariables.PNG "Select the environment and save the variables")
   1. Double check if environment now has value for variables such as ___bearerToken_supplies, bearToken_shipNodes___
   ![alt text](/README_images/environment_checkVariables.PNG "check if tokens are set")
   
  
 
