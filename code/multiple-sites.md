# Gather analtyics for multiple sites at one time

Sometimes, it's helpful to gather analytics for multiple sites at once. This code was developed to help the Ohio Valley ReSource, a regional journalism collaborative, collect analytics for the same story across multiple partner website.

NOTE: The account that creates the spreadsheet will need to have access to each of the GA accounts it is trying to pull from.

This script allows you to pull page-specific analytics based on a variable start date. 




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
