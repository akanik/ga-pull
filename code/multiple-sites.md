# Gather analtyics for multiple sites at one time

Sometimes, it's helpful to gather analytics for multiple sites at once. This code was developed to help the Ohio Valley ReSource, a regional journalism collaborative, collect analytics for the same story across multiple partner website.

NOTE: The account that creates the spreadsheet will need to have access to each of the GA accounts it is trying to pull from.

This script allows you to pull page-specific analytics based on a variable start date. 




```
//Some variables for you to change
var profiles = [
      {'name':'Dicks Automotive','sheet':'dicks-auto','profileID':'1111111'},
      {'name':'Highway Drive In','sheet':'hwy-drivin','profileID':'1111112'},
      {'name':'Irwin Distributor','sheet':'irwin-dist','profileID':'1111113'},
      {'name':'Fruit Market','sheet':'fruit-mrkt','profileID':'1111114'},
    ];

var ga_end_date = '2018,12,31';

var metrics = 'ga:pageviews, ga:uniquePageviews, ga:avgTimeOnPage';


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
      //rowNumber = i+2;
      //outputToSpreadsheet(profileID, sheet, rowNumber, 6);
      
      profileID = profiles[i]['profileID'];
      sheetName = profiles[i]['sheet'];
      fetchData(profileID, sheetName)
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
  
function fetchData(profileID, sheetName){
  // select the dates sheet
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = ss.getSheetByName(sheetName);
    
  var tableId = 'ga:' + profileID;
  
  // The code below gets the values for the range A2 thru B
  // in the active spreadsheet.  Note that this is a JavaScript array.
  var values = sheet.getRange('A2:B').getValues();
  
  var startDate, endDate, pageURI, rowNumber,results; 
  for (i = 0; i < values.length; i++) { 
    startDate = Utilities.formatDate(values[i][0], 'GMT', 'yyyy-MM-dd');
    endDate = Utilities.formatDate(new Date(ga_end_date), 'GMT', 'yyyy-MM-dd');;
    pageURI = 'ga:pagePath=~' + values[i][1];
    rowNumber = Math.floor(i+2);
        
    var optArgs = {
      //'start-index': '1',
      'filters': pageURI,
      'max-results': '1' // Display the first result.
    };
    
    // Make a request to the API.
    results = Analytics.Data.Ga.get(
      tableId,                    // Table id (format ga:xxxxxx).
      startDate,                  // Start-date (format yyyy-MM-dd).
      endDate,                    // End-date (format yyyy-MM-dd).
      metrics, // Comma seperated list of metrics.
      optArgs);
        
    if(results.getTotalsForAllResults()) {
      var twoDList = []
      var metricsList = [];
      for(result in results.getTotalsForAllResults()){
        metricsList.push(results.getTotalsForAllResults()[result]);
      };
      twoDList.push(metricsList);
      // Print the rows of data.
      sheet.getRange(rowNumber, 7, twoDList.length, 3)
          .setValues(twoDList);
    }else{
      throw new Error('No views (profiles) found');
      //sheet.getRange(rowNumber, 3, 1, 1)
      //    .setValues([[0]]);
    }
  }
}

```
