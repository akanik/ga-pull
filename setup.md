# Setup

In order to get the code working, we need to run through a few setup steps.

I estimate this setup process taking about 20 minutes or less. As long as you don't run into issues.

> _IMPORTANT: The Google account that owns the spreadsheet must have Google Analytics access to each account the script is trying to pull analytics for_

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

> NOTE: If you have already created a Google Cloud Project for a similar script, in other words, if you've already used the code in this repository to pull analytics into a spreadsheet and you're simply trying to create another spreadsheet that also pulls analytics from the same accounts, you can reuse the project that you used before.
> If you choose to use an existing project, please remember that you will still need to activate Google Analytics API from `Resources > Advanced Google Services` on your new script.  

Navigate to the [Google Cloud Platform API here](https://console.cloud.google.com/cloud-resource-manager) and click `NEW PROJECT`.

Add a name for your project.

It is best to make this name descriptive to its purpose, but not so specific that you feel you cannot reuse it for similar scripts.

> NOTE: If you are adding this project under an organization account, the fields for **Organization** and **Location** may be filled out for you. Take note of these because they will be helpful in finding your project later.

Click `CREATE`.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-21-cloud-name-project.png "cloud name project image")

Once your project is finished processing, you should see the project dashboard screen.

Make sure you have the correct project selected at the top:

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-19-google-cloud-project.png "google cloud project image")

Now we need to authorize access to this project by creating new access credentials.

Click the menu button in the top left hand corner, then navigate to `APIs & Services > Credentials`.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-23-credentials-nav.png "credentials nav image")

Click O`Auth Consent Screen` and enter an **Application Name**. Make sure the email address is correct, and then click `SAVE`.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-24-oauth-consent.png "oauth consent page image")

Once you've done this, you should be directed to the **Credentials** page. Click `Create credentials > OAuth client ID`.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-25-create-credentials.png "create credentials init image")

Select `Web application`, fill in a **Name** and click `SAVE`.

> NOTE: This Name is not the name of your project, but rather associated with the account or system that will be accessing the API we're going to add in the next few steps. You can name it whatever you like, but try to use something short and descriptive. For example: [your company here] GS script.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-26-name-credentials.png "name credentials image")

Now we're going to enable the Google Analytics API.

Navigate to `APIs and Services > Library`. 

In the search bar, search for _Analytics_.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-7-cloud-search.png "google api cloud search image")

Click on the `Analytics API`. (NOTE: Not Google Analytics Reporting API)

Click `ENABLE` (_If the API is already enabled, it will say `MANAGE` instead of `ENABLE`_)

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-8-enable-api.png "google api enable image")

Now that we've set up the appropriate credentials and added the correct API, we're going to sync the Google Cloud Project that we just create with the Google Apps Script we created earlier.

Click the menu button in the top left hand corner, then navigate to `IAM & admin > Settings`.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-27-project-nav.png "project nav image")

Copy the **Project number**

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-22-project-no.png "google cloud project number image")

Go back to your Google Apps Script (if the script window closed, simply go back to your Google spreadsheet and click `Tools > Script editor`). Navigate to `Resources > Cloud Platform project`.

![alt text](https://github.com/akanik/ga-pull/raw/master/img/ga-pull-28-cloud-project.png "script cloud project image")

Under **Change project**, enter your copied project number and click `Set Project`.

Confirm your project change when prompted.

You should now be ready to start adding [code to your Google Apps Script](code/)!!
