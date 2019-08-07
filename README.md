# ga-pull
Steps and code for pulling Google Analytics into Google Sheets

There are several configuration steps that you will need to go through in order to enable Google Sheets (GS) to pull Google Analytics (GA) code.

NOTE: The Google account that creates the spreadsheet must be the same Google account that has GA access.

**Steps Overview:**
1. Create a new Google spreadsheet
2. Enable Scripts
3. Activate APIs
4. Add the code
5. Replace the variables
6. Add an update button (and running the damn thing)
7. Troubleshooting


## 1. Create a new Google spreadsheet. 
Name it something creative like **analytics-dashboard**


## 2. Enable Scripts
Click `Tools > Script Editor`

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-1-sync-script.png "add google sheets script image")

A new tab should open up that looks like this:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-2-new-script.png "new sheets script image")

Name your project something awesome like **metrix-pullin**.


## 3. Activate APIs
On your new code tab, select `Resources > Advanced Google Services`

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-3-google-services.png "google api services image")

Find the line that says Google Analytics API and make sure that it’s toggled to the  `ON`  position.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-4-analytics-api.png "google analytics api image")

**DON’T CLICK OK YET**. See that yellow box telling you to enable services in the Google Cloud Platform API Dashboard? Click _that_ link.

> Don’t panic if you get a screen that looks like this:
>
> ![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-5-account-error.png "api account error image")
> 
> It just means that the Developer Console tried to open up using the wrong Google account. Look at the top right of the page at your account icon and make sure it reflects the appropriate account.

If you’re using the correct account, you should see something like this:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-6-cloud-dashboard.png "google api cloud dashboard image")

Click `ENABLE APIS AND SERVICES`.

In the search bar, search for _Analytics_.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-7-cloud-search.png "google api cloud search image")

Click on the `Analytics API`. (NOTE: Not Google Analytics Reporting API)

Click `ENABLE` (_If the API is already enabled, it will say `MANAGE` instead of `ENABLE`_)

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-8-enable-api.png "google api enable image")


Head back to the code tab. You should still have this popup window visible:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-4-analytics-api.png "google analytics api image")

Now you can click `OK`.


## 4. Add the code

Copy and paste the following code into the text area of your code tab: 

```
function gaDateFetch(){
  // Here are all the variables you'll need to set
  var sheetName = 'by-date';
  var viewId = 'ga:xxxxxxx';
  var metric = 'ga:users';

  // select the sheet named by-date
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = ss.getSheetByName(sheetName);
  
  var optArgs = {
    'start-index': '1',
    'max-results': '1'
  };
  
  // Get the values in columns A and B 
  // which should be your start and end dates
  var values = sheet.getRange('A2:B').getValues();
  
  var startDate, endDate, rowNumber,results; 
  for (i = 0; i < values.length; i++) { 
    startDate = Utilities.formatDate(values[i][0], 'GMT', 'yyyy-MM-dd');;
    endDate = Utilities.formatDate(values[i][1], 'GMT', 'yyyy-MM-dd');;
    rowNumber = Math.floor(i+2);
    
    // Make a request to the API.
    results = Analytics.Data.Ga.get(
      viewId,                    // View id (format ga:xxxxxx).
      startDate,                  // Start-date (format yyyy-MM-dd).
      endDate,                    // End-date (format yyyy-MM-dd).
      metric, // Comma seperated list of metrics.
      optArgs);
        
    if(results.getRows()) {
      sheet.getRange(rowNumber, 3, results.getRows().length, 1)
          .setValues(results.getRows());
    }else{
      sheet.getRange(rowNumber, 3, 1, 1)
          .setValues([[0]]);
    }
  }
}
```

_If you see a red asterisk by the filename of your code tab, it means your file isn’t saved. Save your file often._

## 5. Replace the variables

Now, in order to make this code work, you need to replace certain variables with values specific to your GA account.

All of the variables you'll have to replace are located conveniently at the top of the code block:

```
  // Here are all the variables you'll need to set
  var sheetName = 'by-date';
  var viewId = 'ga:xxxxxxx';
  var metric = 'ga:users';
```

### sheetName

`sheetName` refers to the name of the sheet in your Google spreadsheet. Create a new sheet or rename Sheet1 to by-date.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-10-sheet-name.png "rename google spreadsheet sheet image")

### viewID

`viewId` refers to the Google Analytics profile that you’re pulling data from.

1. Go to https://analytics.google.com
2. Click on the Profile and View that you want to pull data from.
3. Then select `Admin > View Settings`
4. Copy the value under View ID.
5. Back in your code tab, replace the `xxxxxxx` in this variable with your View ID: `var viewId = 'ga:xxxxxxx';`

_NOTE: Make sure you keep the ga: and the single quotes._

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-11-ga-admin.png "google analytics admin image")

### metric

`metric` refers to the Google Analytics metric you want to pull in based on the date ranges you’re going to put in columns A and B. 
Here’s a list of some of the common metrics you might want to use:
- ga:users
- ga:newUsers
- ga:sessions
- ga:bounceRate
- ga:avgSessionDuration

[Explore more available metrics here](https://developers.google.com/analytics/devguides/reporting/core/dimsmets)

## 6. Add an update button (and running the damn thing)

We’re going to add a few more lines of code here to make this thing really easy to use. Copy this code and paste it above our `gaDateFetch()` function:

```
//These two functions create a new menu item titled "Update data" that
//when clicked will run the rest of the code we're writing here
function onOpen() {
  var ui = SpreadsheetApp.getUi();
  ui.createMenu('Update data')
      .addItem('Update data', 'updateDataMenu')
      .addToUi();
}

function updateDataMenu() {
  gaDateFetch();
}
```

Altogether, your code should look like this:

```
//These two functions create a new menu item titled "Update data" that
//when clicked will run the rest of the code we're writing here
function onOpen() {
  var ui = SpreadsheetApp.getUi();
  ui.createMenu('Update data')
      .addItem('Update data', 'updateDataMenu')
      .addToUi();
}

function updateDataMenu() {
  gaDateFetch();
}

function gaDateFetch(){
  // Here are all the variables you'll need to set
  var sheetName = 'by-date';
  var viewId = 'ga:xxxxxxx';
  var metric = 'ga:users';

  // select the sheet named by-date
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = ss.getSheetByName(sheetName);
  
  var optArgs = {
    'start-index': '1',
    'max-results': '1'
  };
  
  // Get the values in columns A and B 
  // which should be your start and end dates
  var values = sheet.getRange('A2:B').getValues();
  
  var startDate, endDate, rowNumber,results; 
  for (i = 0; i < values.length; i++) { 
    startDate = Utilities.formatDate(values[i][0], 'GMT', 'yyyy-MM-dd');;
    endDate = Utilities.formatDate(values[i][1], 'GMT', 'yyyy-MM-dd');;
    rowNumber = Math.floor(i+2);
    
    // Make a request to the API.
    results = Analytics.Data.Ga.get(
      viewId,                    // View id (format ga:xxxxxx).
      startDate,                  // Start-date (format yyyy-MM-dd).
      endDate,                    // End-date (format yyyy-MM-dd).
      metric, // Comma seperated list of metrics.
      optArgs);
        
    if(results.getRows()) {
      sheet.getRange(rowNumber, 3, results.getRows().length, 1)
          .setValues(results.getRows());
    }else{
      sheet.getRange(rowNumber, 3, 1, 1)
          .setValues([[0]]);
    }
  }
}
```

Return to your spreadsheet and refresh the page. You should now see an additional menu tab titled **Update data**.

_NOTE: You will have to reopen the code tab by going to `Tools > Script editor`_

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-12-gs-update.png "google sheets update image")

Add a few date ranges in the start_date and end_date columns

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-13-gs-dates.png "add dates to gs image")

Now, click `Update data > Update data`

Don’t worry if you’re prompted to grant permissions to run this code. That’s normal on the first time running it:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-14-auth.png "sheet/script authentication image")

Hopefully what you now see is Google Analytics data in column C of your spreadsheet.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-17-date-fill.png "date analytics fill image")

If not, see the Troubleshoots section below:

## 7. Troubleshooting

### Wrong APIs enabled
If you receive an error message like this it means that you enabled the wrong APIs. Follow the link provided and enable the correct API.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-15-wrong-apis.png "wrong apis image")

### Insufficient Google Analytics permissions
If you receive an error message like this it means that the Google account that you’re running this code from does not have permission on the Google Analytics account you’re trying to access:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-16-wrong-permissions.png "wrong permissions image")

### Date out of range warning
You might see an error message like this pop up, even if your code has run successfully:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-18-date-warning.png "date warning image")
