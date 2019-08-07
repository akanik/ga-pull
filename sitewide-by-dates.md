# Pull sitewide data between date ranges


## 1. Add the code

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

## 2. Replace the variables

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

## 3. Add an update button (and running the damn thing)

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

If not, see the [Troubleshoots section](https://github.com/akanik/ga-pull/blob/master/troubleshooting).
