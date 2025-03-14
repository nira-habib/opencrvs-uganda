# Changelog

## 1.7.0 (TBD)

### Bug fixes

- Kibana disk space alerts now work regardless of your disk device names. Alerts listen devices mounted both to `/` and `/data` (encrypted data partition)

## 1.6.0 (TBD)

### Breaking changes

- Remove `splitView` option from DOCUMENT_UPLOADER_WITH_OPTION field
- New required sections preview & review added. Signature field definitions are now part of these two sections same as normal form fields.
- Remove `inputFieldWidth` from Number type form field
- Application config file is renamed to `application-config.ts`
- Allow configuring the default search criteria for record search which can be done by adding or modifying a property named `SEARCH_DEFAULT_CRITERIA` in `application-config.ts`
  Value of `SEARCH_DEFAULT_CRITERIA` can be one of the following
  1. 'TRACKING_ID',
  2. 'REGISTRATION_NUMBER',
  3. 'NATIONAL_ID',
  4. 'NAME',
  5. 'PHONE_NUMBER',
  6. 'EMAIL'

## 1.5.0

### Breaking changes

- #### Update the certificate preview mechanism

  In effort of minimizing JavaScript-bundle size, we have streamlined the way how review certificate -page renders certificates. In case the images in your certificates are previewing blurry, you need to update your SVG-certificates to print QR-codes and other images directly with `<image width="36" height="36" xlink:href="{{qrCode}}" x="500" y="770"></image>` instead of the more complicated `<rect fill="url(#pattern)"></rect>` -paradigm. This doesn't affect printed certificates as they are still created as previously.

- #### Application config items `DATE_OF_BIRTH_UNKNOWN` and `INFORMANT_SIGNATURE_REQUIRED` moved under `FEATURES`

  See https://github.com/opencrvs/opencrvs-farajaland/pull/1005 for details

### Infrastructure

- Allow using staging to both period restore of production backup and also for backing up its own data to a different location using `backup_server_remote_target_directory` and `backup_server_remote_source_directory` ansible variables. This use case is mostly meant for OpenCRVS team internal use.

- Automate SSH key exchange between application and backup server. For staging servers, automatically fetch production backup encryption key if periodic restore is enabled

- Improved support for non-22 SSH port

- Treat backup host identically to other hosts. To migrate:

  1. Move all inventory files (qa.yml, production.yml...) from `infrastructure/server-setup` to `infrastructure/server-setup/inventory`
  2. Run environment creator for your backup server `yarn environment:init --environment=backup`

### Other changes

- Upgrade Node.js to 18
- Remove dependency OpenHIM. The OpenHIM database is kept for backwards compatibility reasons and will be removed in v1.6
- Change auth URLs to access them via gateway
- Add hearth URL to search service
- Include an endpoint for serving individual certificates in development mode
- Include compositionId in confirm registration payload
- Remove logrocket refrences
- Enable gzip compression in client & login
- Make SENTRY_DSN variable optional
- Use docker compose v2 in github workflows
- Mass email from national system admin
- Add SMTP environment variables in qa compose file
- Use image tag instead of patterns in certificate SVGs
- Generate default address according to logged-in user's location
- Remove authentication from dashboard queries route
- Added french translation of informant for print certificate flow, issue certificate flow & correction flow
- In the certificate, the 'Place of Certification' now accurately reflects the correct location.
- Added french translation of informant for print certificate flow, issue certificate flow & correction flow
- Groom's and Bride's name, printIssue translation variables updated [#124](https://github.com/opencrvs/opencrvs-countryconfig/pull/124)
- Add query mapper for International Postal Code field
- Add support for image compression configuration
- Provide env variables for metabase admin credentials
- Improved formatting of informant name for inProgress declaration emails
- Rename `farajaland-map.geojson` to `map.geojson` to not tie implementations into example country naming
- Remove `splitView` option from DOCUMENT_UPLOADER_WITH_OPTION field [#114](https://github.com/opencrvs/opencrvs-countryconfig/pull/114)
- Enable authentication for certificates endpoint [#188](https://github.com/opencrvs/opencrvs-countryconfig/pull/188)

## [1.4.1](https://github.com/opencrvs/opencrvs-countryconfig/compare/v1.4.0...v1.4.1)

- Improved logging for emails being sent
- Updated default Metabase init file so that it's compatible with the current Metabase version
- Deployment: Verifies Kibana is ready before setting up alert configuration
- Deployment: Removes `depends_on` configuration from docker compose files
- Deployment: Removes some deprecated deployment code around Elastalert config file formatting
- Provisioning: Creates backup user on backup servers automatically
- Provisioning: Update ansible Github action task version

- Copy: All application copy is now located in src/translations as CSV files. This is so that copy would be easily editable in software like Excel and Google Sheets. After this change, `AVAILABLE_LANGUAGES_SELECT` doesn't need to be defined anymore by country config.

## [1.4.0](https://github.com/opencrvs/opencrvs-countryconfig/compare/v1.3.3...v1.4.0)

- Added examples for configuring HTTP-01, DNS-01, and manual HTTPS certificates. By default, development and QA environments use HTTP-01, while others use DNS-01.
- All secrets & variables defined in Github Secrets are now passed automatically to the deployment script.
- The VPN_HOST_ADDRESS variable is now required for staging and production installations to ensure deployments are not publicly accessible.
- Replica limits have been removed; any number can now be deployed.
- Each environment now has a dedicated docker-compose-<environment>-deploy.yml. Use `environment:init` to create a new environment and generate a corresponding file for customizable configurations.
- 🔒 OpenHIM console is no longer exposed via HTTP.
- Ansible playbooks are refactored into smaller task files.

### New features

- We now recommend creating a new Ubuntu user `provision` with passwordless sudo rights for all automated operations on the server, instead of using the root user. New users for different operations will be created in future releases.
- All human users on all servers now have their own Linux users with mandatory 2-factor authentication.
- OpenCRVS Farajaland now has an interactive script `environment:init` for creating new Github environments and defining secrets. This script should also be run for existing environments to ensure all variables and secrets are defined, especially important when pulling the latest changes from the Farajaland repository to your own country resource package.
- The environment creator script also manages the known hosts file automatically.
- 🚰 New pipeline for automatic provisioning of Ubuntu servers (all environments).
- 🚰 New pipeline for resetting data from an environment (non-production environments).
- 🚰 New pipeline for resetting SSH 2FA for all environments.
- 🚰 Development deploy pipeline now includes a "debug" option for SSHing into the action runner (non-production environments).
- A new "staging" environment has been introduced, acting as a production environment clone that resets its data nightly to match the production environment.
- The deployment script can now verify if there are undefined environment variables referred to in your compose files. All secrets and variables defined in Github Environments are automatically passed down to the deployment script.
- 🔒 Backup archives are now secured with a passphrase.
- HTTPS setup now offers three options: HTTP challenge, DNS challenge, and using a pre-issued certificate file.
- There's now a generic purpose POST /email endpoint only available from the internal network. Elastalert2 is configured to use this endpoint instead of directly using SMTP details or the Sendgrid API key.
- 🔒 QA environment now hosts a Wireguard server and admin panel (wg-easy). After deploying, you can access the admin panel at vpn.<your domain>.
- Allow configuring additional SSH parameters globally using `SSH_ARGS` Github variable.

### Breaking changes

- Known hosts are now defined in the `infrastructure/known-hosts` file. You can clear the file and use `bash infrastructure/environments/update-known-hosts.sh <domain>` to add your own domains.
- Ansible inventory files are now in .yml format. Please convert your old `production.ini` and similar files to this new format.
- The `authorized_keys` file has been removed, and keys should now be defined in the inventory yaml files.
- The `DOCKER_PASSWORD` secret has been replaced with `DOCKER_TOKEN`.

### Note

In the next OpenCRVS release v1.5.0, there will be two significant changes:

- The `infrastructure` directory and related pipelines will be moved to a new repository.
- Both the new infrastructure repository and the OpenCRVS country resource package repositories will start following their own release cycles, mostly independent from the core's release cycle. From this release forward, both packages are released as "OpenCRVS minor compatible" releases, meaning that the OpenCRVS countryconfig 1.3.0-<incrementing release number> is compatible with OpenCRVS 1.3.0, 1.3.1, 1.3.2, etc. This allows for the release of new hotfix versions of the core without having to publish a new version of the infrastructure or countryconfig.

## [1.3.4](https://github.com/opencrvs/opencrvs-countryconfig/compare/v1.3.3...v1.3.4)

### Bug fixes

- Fix typo in certificate handlebar names

## [1.3.3](https://github.com/opencrvs/opencrvs-countryconfig/compare/v1.3.2...v1.3.3)

### New features

- #### Greater customizability of location data in certificates

  The various admin level handlebars e.g. **statePlaceofbirth**,
  **districtPrimaryMother** only contained the name of that location which was
  not able to take advantage of all the information OpenCRVS had available
  about the various admin levels e.g. the name of that location in the
  secondary language. So we are introducing a new set of admin level
  handlebars that would contain the **id** of that location which we can
  resolve into a value of the shape

  ```
  {
    name: string
    alias: string
  }
  ```

  using the new **"location"** handlebar helper. Here name is the primary
  label of the location and alias being the secondary one. Currently only
  these 2 fields are available but we will be adding more fields depending on
  various countries requirements. If previously the certificate svg used to
  contain `{{districtPlaceofbirth}}` then now we can replace it with
  `{{location districtPlaceofbirthId 'name'}}`. To access alias, the `'name'`
  needs to be replaced with `'alias'`.

  Below is a list of all the new handlebars that are meant to be used with the
  "location" handlebar helper.

  - statePrimaryInformantId
  - districtPrimaryInformantId
  - statePlaceofbirthId
  - districtPlaceofbirthId
  - statePrimaryMotherId
  - districtPrimaryMotherId
  - statePrimaryFatherId
  - districtPrimaryFatherId
  - statePrimaryDeceasedId
  - districtPrimaryDeceasedId
  - statePlaceofdeathId
  - districtPlaceofdeathId
  - statePrimaryGroomId
  - districtPrimaryGroomId
  - statePrimaryBrideId
  - districtPrimaryBrideId
  - statePlaceofmarriageId
  - districtPlaceofmarriageId
  - registrar.stateId
  - registrar.districtId
  - registrar.officeId
  - registrationAgent.stateId
  - registrationAgent.districtId
  - registrationAgent.officeId

  ##### We will be deprecating the counterpart of the above mentioned handlebars that contains only the label of the specified location in a future version so we highly recommend that implementers update their certificates to use these new ones.

- #### "Spouse" section in Farajaland death form

  Spouse section is an optional section in death form. Going forward it will be included in Farajaland example configuration.

- #### Type of ID dropdown
  Farajaland forms will now include a dropdown to select the type of ID an individual is providing e.g. National ID, Driving License etc. instead of being restricted to only national ID number.
- #### Number of dependents of deceased field
  As an example of custom field, the deceased section in death form will now include the **numberOfDependants** field.
- #### Reason for late registration field
  The birth & death forms will include another custom field, **reasonForLateRegistration**, which makes use of "LATE_REGISTRATION_TARGET" configuration option in it's visibility conditional.

### Bug fixes

- Updated translations for form introduction page and sending for approval to reflect the default notification method being email.
- Remove hard-coded conditionals from "occupation" field to make it usable in the deceased form
