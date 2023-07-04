# KenyaEMR Releases

This repository contains releases for OpenMRS-based EMR for the Kenya Ministry of Health(MoH), KenyaEMR. KenyaEMR is developed and maintained by [Palladium Kenya](https://github.com/orgs/palladiumkenya). These releases are similar to those found on the official release page [here](https://github.com/palladiumkenya/kenyahmis-releases/releases). The only difference is that the releases found in this repository are repackaged to conform to the directory structure listed below. The purpose of this repository is to provide a standard structure to KenyaEMR releases making it easier to create deployment automation scripts.

## Archive directory structure

```
.
KenyaEMR-v{x.y.z}.tar.xz
└── kenya-emr-release-content - Release content root folder.
    ├── configuration - Extra configurations needed to run KenyaEMR.
    ├── databases - Database dumps to restore for a fresh installation.
    |   ├── openmrs.sql - The main database dump.
    |   └── *.sql - Other database dumps (optional).
    ├── frontend - Assets and static files required to render the UI.
    ├── migrations - Database migration files.
    |   ├── install - SQL migration files to run during installation.
    |   |   └── *.sql (optional)
    |   ├── post_install - SQL migration files to run after installation.
    |   |   └── *.sql (optional)
    |   ├── upgrade - SQL migration files to run during an upgrade.
    |   |   └── *.sql (optional)
    |   └── post_upgrade - SQL migration files to run after an upgrade.
    |       └── *.sql (optional)
    ├── modules - OpenMRS module files (omod) that constitute this release.
    |   └── *.omod
    ├── webapps - War files to deploy to a servlet container.
    |   └── openmrs.war - The OpenMRS war file.
    └── README.md - Readme file with additional info about this release (optional).
```

Things to note:
 - Any file/folder without the *(optional)* suffix will always be present on the archive. 
 - The SQL scripts in the `migrations` directory will be named lexicographically according to the order in which they should be applied.
 - Unless otherwise specified, the SQL scripts in the `migrations` directory should be applied on the main application database which is typically named `openmrs`.
 - The `openmrs.war` file in the `webapps` directory will always be present.

## Deployment

This section lists the general steps to follow during the deployment of KenyaEMR. These are split into steps for a fresh installation and steps for an upgrade of an existing installation. Note that whatever is listed here are just the general guidelines of the steps to follow and might not be sufficient/accurate for each release. As such, whenever a release has deployment steps that deviate from what is listed here, that will always be indicated on the README file included in the release.

### Installation

- Download the release archive from the [releases section](https://github.com/savannahghi/kenya-emr-releases/releases).
- Unarchive the downloaded file into a temporary directory. We will refer to this directory as `keEMR-x.y.z`.
- Stop the target servlet container if running.
- Copy the `openmrs.war` file from the `keEMR-x.y.z/webapps` directory into the webapps (or similar) directory of the servlet container.
- Create an [OpenMRS data directory](https://wiki.openmrs.org/display/docs/Application+Data+Directory). We will refer to this directory as `oMRS_data_dir`.
- Copy `keEMR-x.y.z/configuration`, `keEMR-x.y.z/modules` and `keEMR-x.y.z/frontend` directories to the `oMRS_data_dir` directory.
- Create a database named `openmrs` and import/restore it contents  from `keEMR-x.y.z/databases/openmrs.sql` SQL dump file.
- Apply any database migration files that are in the `keEMR-x.y.z/migrations/install` directory to the `openmrs` (unless specified otherwise) database. If non are available, skip this step.
- Create an openmrs [runtime properties file](https://wiki.openmrs.org/display/docs/Overriding+OpenMRS+Default+Runtime+Properties) and populate it with the appropriate settings for your installation.
- Restart the target servlet container.
- After a successful start of KenyaEMR, apply any database migration files that are in the `keEMR-x.y.z/migrations/post_install` directory to the `openmrs` (unless specified otherwise) database. If non are available, skip this step.

    > **Note:** _You might be required to stop the target servlet container before running these migrations or perform some additional steps. If this is the case, it will be indicated on the release README._

- That's it :thumbsup:, restart the servlet container (if not already started) and you should be good to go.

### Upgrade
- Download the release archive from the [releases section](https://github.com/savannahghi/kenya-emr-releases/releases).
- Unarchive the downloaded file into a temporary directory. We will refer to this directory as `keEMR-x.y.z`.
- Stop the target servlet container if running.
- Replace the `openmrs.war` file in the webapps (or similar) directory of the servlet container with the one in the `keEMR-x.y.z/webapps` directory.
- Locate the [OpenMRS data directory](https://wiki.openmrs.org/display/docs/Application+Data+Directory) of the installation. We will refer to this directory as `oMRS_data_dir`.
- Delete the following sub-directories from the `oMRS_data_dir` directory: `configuration`, `modules` and `frontend`.
- Copy `keEMR-x.y.z/configuration`, `keEMR-x.y.z/modules` and `keEMR-x.y.z/frontend` directories to the `oMRS_data_dir` directory.
- Apply any database migration files that are in the `keEMR-x.y.z/migrations/upgrade` directory to the `openmrs` (unless specified otherwise) database. If non are available, skip this step.
- Restart the target servlet container.
- After a successful start of KenyaEMR, apply any database migration files that are in the `keEMR-x.y.z/migrations/post_upgrade` directory to the `openmrs` (unless specified otherwise) database. If non are available, skip this step.

    > **Note:** _You might be required to stop the target servlet container before running these migrations or perform some additional steps. If this is the case, it will be indicated on the release README._

- That's it :thumbsup:, restart the servlet container (if not already started) and you should be good to go.


## License

[MIT License](https://github.com/savannahghi/kenya-emr-releases/blob/main/License)

Copyright (c) 2023, Savannah Informatics Global Health Institute