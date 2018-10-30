# Sterling OMS IV Integration

## IV Quick Start with Postman

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
   
  
 
