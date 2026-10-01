# Angular Admin Dashboard Build Skill

## Technology Stack
### Angular v22
### Angular material
### Bootstrap 5 for general layout

## Implementation Practice requirement
### The font color and size will be covered by angular material typography
### scss stylesheet is centralized, each page no stylesheet file
### All web implementation using angular material ex: tab, button, table

## Page layout
### There are header (can show/hide), side bar (can show/hide), footer and show/hide right bar for notification
### side bar contains following menu and groups:
- Agents Group (accordion) with menu WIP, Sub-WIP, Loan Search
- Admin Group (accordion) with Request, Assignment Approvals, Profiles List, Group List, System Parameters
### action and page(if no details provided just say under construction) mapping for the side bar:
- The code layout has agent and admin folder, generated pages for each menu under the right group
- WIP page: page is a table. column name: CW_ID, Sub-Status, Customer, SSN, Entity, Loan Id, Loan Status, Return Code, Acct Bal. Age, Last Notes Timestamp, Last Note Agent, Add to Sub-WIP(This is a Muti choice check)
- 
- For the Profile List page, create a tab based page, first tab is profile List page(with a paginable table with 10 records per page as default and user can change number records per page, with column as Agent ID, Nama, BlockWork, Case Factor, Min.Bal. Max.Bal., Languages, Groups). Also each column can be sorted, second tab is details page (when use click Agent ID, goes to details tab show agent details in a pre-filled form which allow user to edit)
- For the Group List page do the similar thing for Profile List Page
- For System Parameters page, show a paginated table with column as category, code, name, value, description, value Description, Value Changed By, Value Changed Date Time, Order, each column can be sorted and default per page is 10 rows, user can change the row count per page.

### Definition of elements
#### table
When say page is a table:
- row with switch colors
- all column need to be able to sort
- all table should be paginated, default is 20, user can change also pagination part tells user how many records in total when possible
- If we say export button on top right, it means click the button all records can be export to csv
- all table page has a filter bar on top left, when click we can filter on certain column
- The table layout need to be flexible with browser shrink or enlarge
#### Multi Tab Page
When say multi tab page:
- Default Tab is the page
- Only default tab need to be loaded initially
- Later tab will not populated immediately but once any web click in the default tab trigger certain behaviour, it will create latter tab. Latter tab and default tab should be different component.
#### action Button
When say action button in page with table:
- Put it on top of table.
- Add button with mentioned name, when click show a notification with message "SUCCESS" for now.
#### Search Form
When say Search form in page with table:
- Put the search form on the top as accordion, can be opened and closed, for now an empty form with search/reset button, search button trigger a new search, reset button rest all condition to default
