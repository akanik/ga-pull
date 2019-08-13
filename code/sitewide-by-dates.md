# Pull sitewide data between date ranges

This code allows you to enter a start date and an end date and will return selected Google Analtyics metrics for your site as a whole.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-17-date-fill.png "date analytics fill image")

## Setup

First, you must create a Google spreadsheet, link it to a Google Apps Script and sync it to a Google Cloud project. All of those steps are explained in the [setup documentation](../setup.md)

## Adding code

Copy and paste the following code into the text area of your code tab: 

```
//Some variables for you to change
//The variables that begin with 'index' are
//0 indexed. So if your URI is in the first column, 
//the value of indexURI should be 0.
var sheetName = 'by-date';
var viewId = 'xxxxxxx';

var metrics = ['ga:pageviews', 'ga:uniquePageviews', 'ga:avgTimeOnPage'];
var indexStartDate = 0;
var indexEndDate = 1;
var indexDataStart = 2;

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

function addHeaders(sheet, headerList, dataStart){
  sheet.getRange(1, dataStart, 1, headerList.length)
      .setValues([headerList]);
}

function removeDups(names) {
  var unique = {};
  names.forEach(function(i) {
    if(!unique[i]) {
      unique[i] = true;
    }
  });
  return Object.keys(unique);
}

function gaDateFetch(){
  // select the sheet named by-date
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = ss.getSheetByName(sheetName);
  
  var tableId = 'ga:' + viewID;
  var metricString = metrics.join(',');
  var dataStart = indexDataStart +1;
  
  var headerList = [];
  
  //var optArgs = {
  //  'start-index': '1',
  //  'max-results': '1'
  //};
  
  // Get the values in columns A and B 
  // which should be your start and end dates
  var range = sheet.getRange(2, 1, sheet.getLastRow(), sheet.getLastColumn());
  var values = range.getValues();
  
  var startDate, endDate, rowNumber,results; 
  for (i = 0; i < values.length; i++) { 
    startDate = Utilities.formatDate(values[i][indexStartDate], 'GMT', 'yyyy-MM-dd');;
    endDate = Utilities.formatDate(values[i][indexEndDate], 'GMT', 'yyyy-MM-dd');;
    rowNumber = Math.floor(i+2);
    
    // Make a request to the API.
    results = Analytics.Data.Ga.get(
      viewId,                    // View id (format ga:xxxxxx).
      startDate,                  // Start-date (format yyyy-MM-dd).
      endDate,                    // End-date (format yyyy-MM-dd).
      metrics); // Comma seperated list of metrics.
    
    if(results.getTotalsForAllResults()) {
      var twoDList = []
      var metricsList = [];
     
      for(result in results.getTotalsForAllResults()){
        metricsList.push(results.getTotalsForAllResults()[result]);
        headerList.push(result);
      };
      twoDList.push(metricsList);
      // Print the rows of data.
      sheet.getRange(rowNumber, dataStart, twoDList.length, metrics.length)
          .setValues(twoDList);
    }else{
      throw new Error('No views (profiles) found');
    }
  }
  var headerListUnique = removeDups(headerList);
  addHeaders(sheet, headerListUnique, dataStart);
}
```

_If you see a red asterisk by the filename of your code tab, it means your file isn’t saved. Save your file often._

## Changing variables

Now, in order to make this code work, you need to replace certain variables with values specific to your GA account.

All of the variables you'll have to replace are located conveniently at the top of the code block:

```
 //Some variables for you to change
//The variables that begin with 'index' are
//0 indexed. So if your URI is in the first column, 
//the value of indexURI should be 0.
var sheetName = 'by-date';
var viewId = 'xxxxxxx';

var metrics = ['ga:pageviews', 'ga:uniquePageviews', 'ga:avgTimeOnPage'];
var indexStartDate = 0;
var indexEndDate = 1;
var indexDataStart = 2;
```

### sheetName

`sheetName` refers to the name of the sheet in your Google spreadsheet.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-10-sheet-name.png "rename google spreadsheet sheet image")

### viewID

`viewId` refers to the Google Analytics profile that you’re pulling data from.

1. Go to https://analytics.google.com
2. Click on the Profile and View that you want to pull data from.
3. Then select `Admin > View Settings`
4. Copy the value under View ID.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-11-ga-admin.png "google analytics admin image")

### metrics

`metrics` refers to the Google Analytics metrics you want to pull in. You can have one metric or several. Seperate each metric with a comma.

Here’s a list of some of the common metrics you might want to use:
- ga:users
- ga:newUsers
- ga:sessions
- ga:bounceRate
- ga:avgSessionDuration

[Explore more available metrics here](https://developers.google.com/analytics/devguides/reporting/core/dimsmets)

### indexStartDate, indexStartDate, indexDataStart

The variables that begin with `index` help orient the script to your specific spreadsheet structure. 

`indexStartDate` is the 0-indexed position of the column that contains the start date. For example, if your start dates are in column A of your spreadsheet, the value of `indexURI` should be 0. 

`indexEndDate` is the 0-indexed position of the column that contains the end date.

`indexDataStart` is the 0-indexed position of the first column that will hold your Google Analytics data once it is returned by the script. This is usually the first empty column after all of the different story-specific information has been recorded in your spreadsheet.

## Running code

Return to your spreadsheet and refresh the page. You should now see an additional menu tab titled **Update data**.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-12-gs-update.png "google sheets update image")

Add a few date ranges in the start_date and end_date columns

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-13-gs-dates.png "add dates to gs image")

Now, click `Update data > Update data`

Don’t worry if you’re prompted to grant permissions to run this code. That’s normal on the first time running it:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-14-auth.png "sheet/script authentication image")

Hopefully what you now see is Google Analytics data in column C of your spreadsheet.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-17-date-fill.png "date analytics fill image")

If not, see the [Troubleshooting section](troubleshooting.md).
