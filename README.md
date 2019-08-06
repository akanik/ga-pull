# ga-pull
Steps and code for pulling Google Analytics into Google Sheets

There are several configuration steps that you will need to go through in order to enable Google Sheets (GS) to pull Google Analytics (GA) code.

NOTE: The Google account that creates the spreadsheet must be the same Google account that has GA access.

Steps Overview:

1. Create a new Google spreadsheet
2. Enable Scripts
3. Activate APIs
4. Add the code
5. Add an update button


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

Find the line that says Google Analytics API and make sure that it’s toggled to the  **ON**  position.

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

Click on the **Analytics API**. (NOTE: Not Google Analytics Reporting API)

Click **ENABLE**.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-8-enable-api.png "google api enable image")

> _NOTE: if the API is already enabled, it will say **MANAGE** instead of **ENABLE**._


Head back to the code tab. You should still have this popup window visible:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-4-analytics-api.png "google analytics api image")

Now you can click **OK**.


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

Now, in order to make this code work, you need to replace certain variables with values specific to your GA account.

All of the variables you'll have to replace are located conveniently at the top of the code block:

```
  // Here are all the variables you'll need to set
  var sheetName = 'by-date';
  var viewId = 'ga:xxxxxxx';
  var metric = 'ga:users';
```

