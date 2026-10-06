# Mattermost on Wodby

What Wodby sets up for Mattermost on this service. Mattermost runs from the official `mattermost/mattermost-team-edition` image. Configuration is passed as `MM_<SECTION>_<SETTING>` environment variables, which take precedence over `config.json`; a setting given by a variable cannot be changed in the System Console.

## Linked services

| Link | Variables |
| --- | --- |
| PostgreSQL (required) | `MM_SQLSETTINGS_DRIVERNAME`, `MM_SQLSETTINGS_DATASOURCE` |
| SMTP server (optional) | `MM_EMAILSETTINGS_SMTPSERVER`, `MM_EMAILSETTINGS_SMTPPORT`, `MM_EMAILSETTINGS_ENABLESMTPAUTH`, `MM_EMAILSETTINGS_CONNECTIONSECURITY` |

Mail goes to the linked mail service without authentication or connection security. The mail variables are present only while the link exists.

## What the manifest sets

- `MM_SERVICESETTINGS_SITEURL` is the environment's primary URL.
- `MM_FILESETTINGS_MAXFILESIZE`; the route allows request bodies up to 128 MiB and has no request timeout.
- Logs go to the console as JSON (`MM_LOGSETTINGS_*`); file logging is off.
- `MM_CONFIG` points to `/mattermost/config/config.json`. Before the first start an init container creates that file with initial plugin states, only when it does not exist.
- The container runs as user and group 2000 and listens on port 8065. The health endpoint is `/api/v4/system/ping`.

## Changing configuration

Settings that the manifest or a link provides as variables are changed on the service (environment variables) and applied by a deployment. Other settings can be changed in the System Console; they are saved to `config.json` on the volume.

## Data

One `data` volume holds four directories:

| Path in the container | Content |
| --- | --- |
| `/mattermost/data` | uploaded files |
| `/mattermost/config` | `config.json` |
| `/mattermost/plugins` | server plugins |
| `/mattermost/client/plugins` | web application plugins |

Everything else is in the linked PostgreSQL database. The manifest declares no backup, import or action for this service, and creates no account: the first system administrator is created in the browser.
