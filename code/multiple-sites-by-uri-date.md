# Gather analtyics for multiple sites at one time

Sometimes, it's helpful to gather analytics for multiple sites at once. This code was developed to help the Ohio Valley ReSource, a regional journalism collaborative, collect analytics for the same story across multiple partner websites.

This script allows you to pull page-specific analytics based on a variable start date. 

## Setup

First, you must create a Google spreadsheet, link it to a Google Apps Script and sync it to a Google Cloud project. All of those steps are explained in the [setup documentation](../setup.md)

## Constructing your spreadsheet

Once you've completed the initial setup, you're going to want to configure your spreadsheet with the necessary data. For this code to work, you need to have at least two columns in your spreadsheet: a **start date** and a **URI** for the story.

Please note that the script requires a URI, not a URL. A URI is like a URI but with the domain name stripped. Keep the leading `/`.

Your spreadsheet can have as many columns of data as you need. For example, in addition to a start date and the story URI, you might want to record the author of the piece or the subject matter.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-29-uri-sheet-structure.png "uri spreadsheet image")

Each site that you're trying to pull analytics for needs to have a sheet with the same structure.

## Adding your code

In the Google Apps script associated with your spreadsheet, add the following code. You can delete anything default code that appears in the new Apps script first.

```
//Some variables for you to change
//The variables that begin with 'index' are
//0 indexed. So if your URI is in the first column, 
//the value of indexURI should be 0.
var profiles = [
  {'name':'Dicks Automotive','sheet':'dicks-auto','viewID':'1111111'},
  {'name':'Highway Drive In','sheet':'hwy-drivin','viewID':'1111112'},
  {'name':'Irwin Distributor','sheet':'irwin-dist','viewID':'1111113'},
  {'name':'Fruit Market','sheet':'fruit-mrkt','viewID':'1111114'},
];
var endDateformat = new Date(2018,12,31);
var metrics = ['ga:pageviews', 'ga:uniquePageviews', 'ga:avgTimeOnPage'];
var indexURI = 2;
var indexStartDate = 0;
var indexDataStart = 3;


//These two functions create a new menu item titled "Update data" that
//when clicked will run the rest of the code we're writing here
function onOpen() {
  var ui = SpreadsheetApp.getUi();
  ui.createMenu('Update data')
      .addItem('Update data', 'updateDataMenu')
      .addToUi();
}
function updateDataMenu() {
  runDataPulls();
}


function runDataPulls() {
  try {
    var ss = SpreadsheetApp.getActiveSpreadsheet();
    
    var i;
    for (i = 0; i < profiles.length; i++) {       
      viewID = profiles[i]['viewID'];
      sheetName = profiles[i]['sheet'];
      fetchData(viewID, sheetName)
    }
    
  } catch(error) {
    Logger.log(error.message); 
  }
}

function getLastNdays(nDaysAgo) {
  var today = new Date();
  var before = new Date();
  before.setDate(today.getDate() - nDaysAgo);
  return Utilities.formatDate(before, 'GMT', 'yyyy-MM-dd');
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
  
function fetchData(viewID, sheetName){
  // select the dates sheet
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = ss.getSheetByName(sheetName);
    
  var tableId = 'ga:' + viewID;
  var metricString = metrics.join(',');
  var dataStart = indexDataStart +1;
  
  var headerList = [];
  
  // The code below gets the values for the range A2 thru B
  // in the active spreadsheet.  Note that this is a JavaScript array.
  var range = sheet.getRange(2, 1, sheet.getLastRow(), sheet.getLastColumn());
  var values = range.getValues();
  
  var startDate, endDate, pageURI, rowNumber,results; 
  for (i = 0; i < (range.getLastRow() - 2); i++) {
    var startDateformat = new Date(values[i][indexStartDate]);
    startDate = Utilities.formatDate(startDateformat, 'GMT', 'yyyy-MM-dd');
    endDate = Utilities.formatDate(endDateformat, 'GMT', 'yyyy-MM-dd');
    pageURI = 'ga:pagePath=~' + values[i][indexURI];
    rowNumber = Math.floor(i+2);
        
    var optArgs = {
      'filters': pageURI,
      //'max-results': '1'          // Display the first result.
    };
    
    // Make a request to the API.
    results = Analytics.Data.Ga.get(
      tableId,                    // Table id (format ga:xxxxxx).
      startDate,                  // Start-date (format yyyy-MM-dd).
      endDate,                    // End-date (format yyyy-MM-dd).
      metricString,               // Comma seperated list of metrics.
      optArgs);
        
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
  var headerListUnique = removeDups(headerList)
  addHeaders(sheet, headerListUnique, dataStart);
}

```

## Changing variables

All of the things that you need to change in order to make this script work for your data are located at the top of the script.

```
//Some variables for you to change
//The variables that begin with 'index' are
//0 indexed. So if your URI is in the first column, 
//the value of indexURI should be 0.
var profiles = [
  {'name':'Dicks Automotive','sheet':'dicks-auto','viewID':'1111111'},
  {'name':'Highway Drive In','sheet':'hwy-drivin','viewID':'1111112'},
  {'name':'Irwin Distributor','sheet':'irwin-dist','viewID':'1111113'},
  {'name':'Fruit Market','sheet':'fruit-mrkt','viewID':'1111114'},
];
var endDateformat = new Date(2018,12,31);
var metrics = ['ga:pageviews', 'ga:uniquePageviews', 'ga:avgTimeOnPage'];
var indexURI = 2;
var indexStartDate = 0;
var indexDataStart = 3;
```

### profiles

Profiles are the different sites that you're trying to pull data for. If you are only trying to pull data for one site, you can remove all of the objects (the lines that start and end with {...}) in the list save one.

For each site that you are trying to pull data for, replace the **name**, **sheetName**, and **viewID** properties.

`name` is simply a human-readable name of the site.

`sheet` refers to the name of the sheet in your Google spreadsheet.

`viewId` refers to the Google Analytics profile view of the site that you’re pulling data from.

- Go to https://analytics.google.com
- Click on the Profile and View that you want to pull data from.
- Then select Admin > View Settings
- Copy the value under View ID

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-11-ga-admin.png "google analytics admin image")

### endDateformat

`endDateformat` end date for the analytics date range that you want to use when pulling your data.

There are ways to have the end date be a variable pulled from the spreadsheet, much like the state date is. There are also ways for this date to be a variable based on the start date. For example, for each story, get two months of analytics. 

This script uses a static end date.

### metrics

`metrics` refers to the Google Analytics metrics you want to pull in. You can have one metric or several. Seperate each metric with a comma.

Here’s a list of some of the common metrics you might want to use:
- ga:users
- ga:newUsers
- ga:sessions
- ga:bounceRate
- ga:avgSessionDuration

[Explore more available metrics here](https://developers.google.com/analytics/devguides/reporting/core/dimsmets)

### indexURI, indexStartDate, indexDataStart

The variables that begin with `index` help orient the script to your specific spreadsheet structure. 

`indexURI` is the 0-indexed position of the column that contains the URI. For example, if your story URIs are in column B of your spreadsheet, the value of `indexURI` should be 1. 

`indexStartDate` is the 0-indexed position of the column that contains the start date.

`indexDataStart` is the 0-indexed position of the first column that will hold your Google Analytics data once it is returned by the script. This is usually the first empty column after all of the different story-specific information has been recorded in your spreadsheet.

## Running your code

Once you've changed all of the variables, return to your spreadsheet and refresh the page. You should now see an additional menu tab titled Update data.

Click `Update data > Update data`.

Don’t worry if you’re prompted to grant permissions to run this code. That’s normal on the first time running it:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-14-auth.png "sheet/script authentication image")
