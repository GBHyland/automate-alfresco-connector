# Generating and Saving Documents to Alfresco

### What you will need:
- A running Alfresco environment in AWS.
- The Alfresco environment URL and Admin account username / password.

### Automate Initial Steps
1. Create the following process variables:
   - **NOTE:** v_name & v_mssg are variables used for the form. If you have (or plan to use) a different form then you may replace these variables with your own coinciding with the data you plan to capture from your form. The other variables below are used in the generation and saving of the document.
   
|  Name               |  Variable type      |
|  -------------      |  -------------      |
|  acsFolder          |  Array<json>        |
|  acsFile            |  Array<json>        |
|  v_name             |  string             |
|  v_mssg             |  string             |
|  generatedDocument  |  content            |

2. Create an Authentication titled ```myAuth```.
   - Use the dropdown in the Auth config to set it to **Basic**.
   - Ensure that the **Secured** check box is checked.
3. Upload your document template (to use in the Generate Document task) using the three-dots next to **Files**.
   - This can be a private file.
   - If you don't have a previous template you want to use and plan to follow my process exactly, download the _personnel-file.docx_ from this repository and use it; if so, be sure to use the specific variables I recommend below.
4. Create a new form (or use a form you already have created).
   - If following along this guide, create the form as follows:
     - **Name:** ```Create Message```
     - Add a text field with the following configuration:
       - **Label:** ```Your Name:```
       - **ID:** ```v_name```
     - Add a text field with the following configuration:
       - **Label:** ```Bio:```
       - **ID:** ```v_mssg```
     - Save the form and return to the process.
5. Add a **User Task** to the process and give it the following configuration:
   - **Name:** ```create-mssg```
   - **Form Name:** _use drop-down to select the Create Message form_
   - **Mapping Type:** ```Map Variables```
   - **Map OUTPUT variables to:**
     - ```Your Name``` > ```v_name```
     - ```Bio``` > ```v_mssg```
6. Add a **Generate Document** task to the process connected after the User Task. Give it the following configuration:
   - **Name:** ```generate-doc```
   - **Mapping Type:** ```Map Variables```
   - **Map INPUT variables to:**
     - ```Metadata```: use expression:
   ```
   {
      "v_name": "${v_name}",
      "v_mssg": "${v_mssg}"
   }
   ```
     - ```outputFileName``` > ```bio```
     - ```outputFormat``` > ```PDF```
     - ```targetFolderPath``` > ```/Buzzy```
     - ```template```: _under Value, select the **Project file** radio button, then use the drop-down to select the file template uploaded in earlier step_
   - **Map OUTPUT variables to:**
     - ```file```: ```generatedDocument``` (process variable)
7. Add an Alfresco Connector task to the process, connected after the generate document task. Configure the connector with the following:
   - **Name:** ```create-file```
   - **Authentication:** _use the drop-down to select the Authorization created earlier, **myAuth**_.
   - **Action:** _use the drop-down to select **CREATE_FILE**_.
   - **Mapping Type:** ```Map Variables```
   - **Map INPUT variables to:**
     - ```FileName``` > (expression) ```bio-${v_name}.pdf```
     - ```targetFolderPath``` > (value) ```/app:company_home/app:shared```
   - **Map OUTPUT variables to:**
     - ```file``` > (process variable) ```acsFile```
8. Add an Alfresco Connector task to the process, connected after the generate document task. Configure the connector with the following:
   - **Name:** ```update-file-content```
   - **Authentication:** _use the drop-down to select the Authorization created earlier, **myAuth**_.
   - **Action:** _use the drop-down to select **UPDATE_FILE_CONTENT**_.
   - **Mapping Type:** ```Map Variables```
   - **Map INPUT variables to:**
     - ```sourceFile``` > (process variable) ```generatedDocument```
     - ```targetFile``` > (process variable) ```acsFile```
   - **Map OUTPUT variables to:**
     - ```response``` > (process variable) ```acsFile```
9. Add a **Content Task** to the process, connect at the end of the process after the Alfresco connector. Configure the connector with the following:
   - **Name:** ```delete-local-doc```
   - **Action:** _use the drop-down to select **DELETE_CONTENT**_.
   - **Mapping Type:** ```Map Variables```
   - **Map INPUT variables to:**
     - ```content``` > (process variable) ```generatedDocument```
11. Release and Deploy - See further Deployment steps below.

### Deployment Configuration:
Since the process employs an Authentication and Connectors, two tabs are added to the deploy wizard which you'll need to configure:
- **Authentication Tab:** You should see the name of the Authentication you applied in the process (myAuth) - (unless you named it something else). Enter the username and password for the Alfresco Admin account you want to use. Usually it is demo / demo.
- **Connectors Tab:** There's some values on this tab you don't need. Remove them as specified below using the "X" icon next to the value. If any of the below required values are not there by default you must add them.
  - ```ACS_KEYCLOAK_URL```: remove
  - ```ACS_KEYCLOAK_REALM```: ```alfresco```
  - ```ACS_CONTENT_CLIENT_ID```: remove
  - ```ACS_CONTENT_CLIENT_SECRET```: remove
  - ```ACS_CONTENT_SERVICE_URL```: _this is the url to your Alfresco environment with no slashes at the end_
  - ```ACS_CONTENT_SERVICE_PATH```: ```/alfresco/api/-default-/public/alfresco/versions/1```
  - ```ACS_SEARCH_SERVICE_PATH```: ```/alfresco/api/-default-/public/search/versions/1```


**End. Deploy and test.**
