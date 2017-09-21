1. Add the store to constructor in component.ts
2. Import behavior subject to componenet.ts file
3. public questionData$: Observable<CommonQuestion[]>;
4. public filterText$ = new BehaviorSubject<string>(''); 

# Redux

## Model
1. Create model. The model is an interface of the data received from the endpoint.

# EES Sprint 3 (5 June 2017 - 24 Jun 2017)
## Client Sprint
- Display, add and edit a facility
- Installation dashboard
- Upload document for facility and installation
- Finish up create notes
- Facility detail
- Learn how to rebase
- Faciility count
- Notes count
- Action item as tiles


## API Sprint
- Patch & post for facility
- Post & delete for units
- Post for installation notes
- Help Jamie getting data done

# EES
1. inspection complete percentage appears only AFTER the inspection
2. deviations may be in the questionnaire - do you have deviation(s)? if so provide...
    - Mike wants a link to the deviation document through wizard to see exactly what the deviation is
    - start, stop and level
    - purpose, recommendation, expiration
3. ID total of facilities - inspected facilities = facilities not inspected
5. Active inslection list - show metrics/reports
4. Questions, total findings, corrective actions in this order
5. group findings even it applies to multiple unites
6. On the ESI process:
    - ESO puts in worker requests, non dod storage...
    7. Legacy Make -> ESO sees -> Documentation -> Requests that have to be addressed -> Needs to see status of the documentation
Get rid of ezxpiration date on document create.
Jim doesnt like: commets submitted doesnt show
ESO needs the standard 
    - Action Due
    - Caps that are open
    - Training Due
    - 

- Jim wants consolidated view of documents

# CAPM

1. Army provides the unit price, quantity and total price. Maverick tells the Army how much money we can spend. Army will give back to Maverick the quanity and unit price they feel satisfies that amount.
 - Add another toggle/row to show the import

2. Total Price can be changed. Do not change the quanity and unit price until the buttons are clicked. Show an indicator that the values are not sync'd..

# Analysis Lab Room Dimensions

165" x 149"

# Clear SP Cache

1.Close SPD if it is open
  
2.Open My Computer
 
a.Click the address bar
  
3.Paste in:
%USERPROFILE%\AppData\Local\Microsoft\WebsiteCache
  
4.Delete everything within this location
  
5.Click the address bar
  
6.Paste in:
%APPDATA%\Microsoft\Web Server Extensions\Cache
  
7.Delete everything in this location

SharePoint Designer 2010 and 2013
1.Navigate to the "File" menu then select "Options" -> "General" -> "Application Options". 
2.On the “General” tab, under the “General” heading, uncheck “Cache site data across SharePoint Designer sessions”.
