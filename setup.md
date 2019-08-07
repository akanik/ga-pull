# Setup

In order to get the code working, we need to run through a few setup steps.

_IMPORTANT: The Google account that creates the spreadsheet must be the same Google account that has GA access_

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

Clikc `OK`.

## 4. Create new Google Cloud project

Every Google Apps Script (what you just created above) must have a Google Cloud Project connected to it. By default, when you create an Apps Script, it is assigned a default project. However, our Google Sheet can't access that default project (this is a recent change for which I have no explanation). So we need to head over to the Google Cloud Platform and create a new one that our script _can_ access.

NOTE: If you have already created a Google Cloud Project for a similar script, in other words, if you've already used the code in this repository to pull analytics into a spreadsheet and you're simply trying to create another spreadsheet that pulls analytics for a different set of URLs, you can reuse the project that you used before.

Navigate to the [Google Cloud Platform API here](https://console.cloud.google.com/cloud-resource-manager) and click `NEW PROJECT`.

Add a name for your project.

It is best to make this name descriptive to its purpose, but not so specific that you feel you cannot reuse it for similar scripts.

> NOTE: If you are adding this project under an organization account, the fields for **Organization** and **Location** may be filled out for you. Take note of these because they will be helpful in finding your project later.

Click `CREATE`.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-21-cloud-name-project.png "cloud name project image")

Once your project is finished processing, you should see the project dashboard screen.

Make sure you have the correct project selected at the top:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-19-google-cloud-project.png "google cloud project image")

Click on `Go to project settings`.

Copy the **Project number**

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-22-project-number.png "google cloud project number image")









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
