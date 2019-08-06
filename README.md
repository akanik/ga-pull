# ga-pull
Steps and code for pulling Google Analytics into Google Sheets

There are several configuration steps that you will need to go through in order to enable Google Sheets (GS) to pull Google Analytics (GA) code.

NOTE: The Google account that creates the spreadsheet must be the same Google account that has GA access.


## Create a new Google spreadsheet. 
Name it something creative like **analytics-dashboard**


## Enable Scripts
Click `Tools > Script Editor`

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-1-sync-script.png "add google sheets script image")

A new tab should open up that looks like this:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-2-new-script.png "new sheets script image")

Name your project something awesome like **metrix-pullin**.


## Activate APIs
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

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-9-fetch-script.png "google api enable image")

Now you can click **OK**.






