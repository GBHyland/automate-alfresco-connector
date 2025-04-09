# Generating and Saving Documents to Alfresco

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
3. Upload your document template using the three-dots next to **Files**.
   - This can be a private file.
4. 
