---
id: installation
title: Installation and Setup
sidebar_label: Installation
---

### Third-Party Resource Dependencies
:::warning
Failure to adhere to these dependencies will result in *NPWD* not working correctly. You MUST use the screenshot basic version linked below.
:::
If you wish for *NPWD* to have full functionality. The following resources are required to be
installed and started before *NPWD*.

1. [screenshot-basic](https://github.com/project-error/screenshot-basic) - **Make sure to use the exact version, as linked.**
2. [pma-voice](https://github.com/AvarianKnight/pma-voice)

:::note
*NPWD* has standardized pma-voice as our default voice system for the phone. We encourage everyone to do such. We will **not** provide support or configuration, for any other VOIP's should issues arise.
:::
### Downloading Release
1. Navigate to the [releases page](https://github.com/project-error/npwd/releases).
    - The latest release is shown at the top of the page and will have an [image](https://i.imgur.com/2SB8pfe.png) to the right stating as such. 
2. [Left click](https://i.imgur.com/LMezKkg.png) on the hyperlink containing `npwd.zip`.
:::tip
The mentioned zip contains a pre-built version of the phone so no building is required.
:::
3. Save and unzip the `npwd` folder into your `resources` folder. An example directory would be: `A:\xd-rp-server\server-data\resources`

## Initial Setup

### Database Integration

To setup the database schema, you must run the `import.sql` file found within the [root](https://github.com/project-error/npwd/blob/master/import.sql) of your `npwd` folder. 

If you already use `mysql-async`, `ghmattimysql`, or `oxmysql`. NPWD will automatically configure itself to use the `mysql_connection_string`
from your `server.cfg` file.

If you do **not** already use one of these libraries, you must add an entry to your `server.cfg` file for `mysql_connection_string`, matching
one of the following connection configuration formats:

```
# You do NOT need both. Only one.
set mysql_connection_string "mysql://root:12345@localhost/es_extended?charset=utf8mb4"
set mysql_connection_string "user=root;database=es_extended;password=12345;charset=utf8mb4"
```

### Basic Configuration
:::caution
Standalone installation requires a users table of some sort that stores player identifiers. If you are a menu based server and don't use popular frameworks, you will need to use [pe-core](https://github.com/project-error/pe-core) so you can use this resource. It's a very basic "framework" and will have no impact on your server.
:::
*NPWD* uses a `config.json` file that contains important information **required** for the resource to work correctly. This file is location within the root of your `npwd` folder. See [here](https://github.com/project-error/npwd/blob/master/config.json).

#### Database Configuration
```json
"database": {
    "useIdentifierPrefix": false,
    "playerTable": "users",
    "identifierColumn": "identifier",
    "identifierType": "license",
    "profileQueries": true,
    "phoneNumberColumn": "phone_number"
  },
```

Above is the default database configuration that comes with *NPWD*. The information needs to be altered to match your database.

- `"useIdentifierPrefix"`
    - The prefix is referring to the text before the colon when obtaining identifiers.
    - If your database uses `license:XXXXXXXXXXXXXXXXXXXXXXXXXXX` set this to `true`. Otherwise, set it to `false`.
    - This is **only** used when `"useResourceIntegration"` is set to `false`.
- `"playerTable"`
    - The table in which your user information is stored. 
    - At a minimum, it needs to contain player's identifier and a phone number column.
- `"identifierColumn"`
    - Found within your playerTable that is specified within the `config.json`. 
- `"identifierType"`
    - Ranges from the following: `steam`, `license`, `xbl`, `ip`, `discord` and `live`.
- `"profileQueries"`
    - Provides useful debug information about the time it took to get data from the database.
- `"phoneNumberColumn"`
    - The column where phone numbers are stored.
    - As of v1.03, you need to create this yourself. See the [sql file](https://github.com/project-error/npwd/blob/13335e98d55ea7a082bf08c7c17f24866658a2d1/import.sql#L3) for an example query.

    :::note
    We are working to improve this so the number column is added when you run the SQL file.
    :::

#### General Configuration
```json
"general": {
    "useResourceIntegration": false,
    "toggleKey": "f1",
    "toggleCommand": "phone",
  },
```

- `"useResourceIntegration"`
    - This is **only** used for framework integration.
    :::info
    Setting this to true is what exposes the `newPlayer` export. The export won't work without this set to true.
    :::
- `"toggleKey"`
    - The key used to open the phone. Please see [here](https://docs.fivem.net/docs/game-references/input-mapper-parameter-ids/keyboard/) for a list of available keys.
- `"toggleCommand"`
    - The command used to open the phone. This is executed on the key press of the configured `toggleKey`. 

### Framework Integration
If you are using a popular framework such as *ESX* or *QBCore*, you can easily integrate NPWD using the corresponding
premade compatibility resource(s), these should automatically configure *NPWD* to work with your framework.

Follow the written documentation for [ESX](../framework/esx) and [QB-Core](../framework/qb-core) for the altered installation.


If you have *basic* development knowledge and wish to integrate NPWD with any arbitrary framework,
see the following [documentation page](../dev/FrameworkIntegration.md) for more information.

:::tip
Have you made a wrapper for a framework not listed above? If so, please reach out to us so we may improve our documentation. You can do so by contacting us on the Project-Error Discord [here](https://discord.com/invite/HYwBjTbAY5).
:::

## Feature Related Setup
Below is information pertaining to functionality to enable specific features of the phone such as image uploading for the camera or report logging for discord.
### Setting up Camera Functionality
*NPWD* allows for players to access a camera app and take in-game photos. The link to the photo is saved within the database of *NPWD* so these photos are **required** to be uploaded somewhere.
We recommend [PE Image](https://image.projecterror.dev), a service we have developed to help with image uploading. This guides you through the process of setting it up. If you have any questions, ask us on Discord. 

**Steps For PE Image**
1. Create an account at [PE Image](https://image.projecterror.dev/register)
2. After you have created an account or logged in, navigate to [Dashboard > Billing](https://image.projecterror.dev/dashboard/billing) and choose a plan.
3. Once you've chosen a plan, you'll have access to the [API Keys](https://image.projecterror.dev/dashboard/keys) tab, where you can generate a token.
4. Copy the API token and add `set SCREENSHOT_BASIC_TOKEN insert_token` to your `server.cfg` file before starting NPWD.

Alternatively, you can use Imgur.

:::caution
Some have reported issues with uploading images. While this is most likely related to using a different version of [screenshot-basic](https://github.com/project-error/screenshot-basic), imgur may also be blocked in your country. We are currently working on alternatives.
:::

**Steps For Imgur**
1. After you have created an account or logged in, create an application [here](https://api.imgur.com/oauth2/addclient).
2. Choose the `Authorization type` called `OAuth 2 authorization without a callback URL`. [Example](https://imgur.com/a/QsYsd4d).
3. Copy the client ID and add `set SCREENSHOT_BASIC_TOKEN your_client_id` to your `server.cfg` file before starting NPWD.
4. Add the following snippet to `npwd/config.json`.

```
"images": {
    "url": "https://api.imgur.com/3/image",
    "type": "imgur",
    "imageEncoding": "jpg",
    "contentType": "multipart/form-data",
    "useContentType": true,
    "authorizationHeader": "Authorization",
    "authorizationPrefix": "Client-ID",
    "useAuthorization": true,
    "returnedDataIndexes": ["data", "link"]
  },
```

If you can't find your application you just created, check your settings pages [here](https://imgur.com/account/settings/apps).

### Setting up Discord Log Integration

NPWD can integrate with Discord to log certain actions. As of release v1.0.3, the only use case is to log reported tweets and marketplace listings. This requires a webhook to be setup and the following convar
set in your `server.cfg` file:

```
# You need to wrap the webhook in quotation marks.
# Do NOT use setr in place of set as this shares this webhook with the client.
set NPWD_DISCORD_TOKEN "my_discord_webhook"
```

## Finishing Up

### Example Final Config

Your `server.cfg` should somewhat resemble the following (in terms of order): 
```cfg
set mysql_connection_string "mysql://root:12345@localhost/es_extended?charset=utf8mb4"
setr SCREENSHOT_BASIC_TOKEN XXXXXXXXXXXXXXX
set NPWD_DISCORD_TOKEN "my_discord_webhook" # Quotations required

ensure screenshot-basic
ensure pma-voice

ensure my_framework_here # Your framework resource. Ignore if you're not using one.
ensure esx-npwd # Any compatibility resources like esx-npwd or qb-npwd. Ignore if you're not using one.

ensure npwd
```

## Important Information
* By installing [npwd](https://github.com/project-error/npwd), you agree to the use of the following diagnostic package:
   * Sentry, (in use within the React portion of NPWD) Automatically
     uploads relevant session details and stack traces whenever an exception is thrown. We use these metrics to further improve the quality of the phone.
   * To explicitly disable this, we urge you **not to** as its incredibly useful metrics for us, please change the `SentryErrorMetrics` setting to `false` in `phone/config/default.json` and rebuild the phone.
* Encountered an issue? Please open an issue [here](https://github.com/project-error/npwd/issues/new/?assignees=&labels=New+Issue&template=bug-report.yml&title=Bug%3A+%3Cshort+description%3E) following the provided template.
* If you seek additional support, feel free to join the [Project-Error Discord](https://discord.gg/HYwBjTbAY5).

