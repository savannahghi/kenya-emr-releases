# KenyaEMR Releases

This repository contains releases for OpenMRS-based EMR for the Kenya Ministry of Health(MoH), KenyaEMR. KenyaEMR is developed and maintained by [Palladium Kenya](https://github.com/orgs/palladiumkenya). These releases are similar to those found on the official release page (check [here](https://github.com/palladiumkenya/kenyahmis-releases/releases) for older releases or [here](https://github.com/palladiumkenya/openmrs-config-kenyaemr/releases) for newer ones).

The only difference is that the releases found in this repository are repackaged to conform to the directory structure listed below. The purpose of this repository is to provide a standard structure to KenyaEMR releases making it easier to create deployment automation scripts.

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
    ├── extra_modules - For more details on this, see the section on `Adding extra-modules`
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

This section lists the steps to follow during deployment of KenyaEMR. The steps are split into two sections: **Routine deployment** and **Extra modules deployment.** 
Each section includes the actions for `fresh installation` and `upgrade` of an existing installation.
Note that whatever is listed here are just the general guidelines of the steps to follow and might not be sufficient/accurate for each release. As such, whenever a release has deployment steps that deviate from what is listed here, that will always be indicated on the README file included in the release.

## 1) Routine deployment
### Installation

- Download the release archive from the [releases section](https://github.com/savannahghi/kenya-emr-releases/releases).
- Unarchive the downloaded file into a temporary directory. We will refer to this directory as `kenyaEMR-x.y.z`.
- Stop the target servlet container if running.
- Copy the `openmrs.war` file from the `kenyaEMR-x.y.z/webapps` directory into the webapps (or similar) directory of the servlet container.
- Create an [OpenMRS data directory](https://wiki.openmrs.org/display/docs/Application+Data+Directory). We will refer to this directory as `oMRS_data_dir`.
- Copy `kenyaEMR-x.y.z/configuration`, `kenyaEMR-x.y.z/modules` and `kenyaEMR-x.y.z/frontend` directories to the `oMRS_data_dir` directory.
- Create a database named `openmrs` and import/restore it contents  from `kenyaEMR-x.y.z/databases/openmrs.sql` SQL dump file.
- Apply any database migration files that are in the `kenyaEMR-x.y.z/migrations/install` directory to the `openmrs` (unless specified otherwise) database. 
If non are available, skip this step.
- Create an openmrs [runtime properties file](https://wiki.openmrs.org/display/docs/Overriding+OpenMRS+Default+Runtime+Properties) and populate it with the appropriate settings for your installation.
- Restart the target servlet container.
- After a successful start of KenyaEMR;
- Apply any database migration files that are in the `kenyaEMR-x.y.z/migrations/post_install` directory to the `openmrs` (unless specified otherwise) database. 
If non are available, skip this step.
    > **Note:** _You might be required to stop the target servlet container before running these migrations or perform some additional steps. If this is the case, it will be indicated on the release README._

- That's it :thumbsup:, restart the servlet container (if not already started) and you should be good to go.

### Upgrade
- Download the release archive from the [releases section](https://github.com/savannahghi/kenya-emr-releases/releases).
- Unarchive the downloaded file into a temporary directory. We will refer to this directory as `kenyaEMR-x.y.z`.
- Stop the target servlet container if running.
- Replace the `openmrs.war` file in the webapps (or similar) directory of the servlet container with the one in the `kenyaEMR-x.y.z/webapps` directory.
- Locate the [OpenMRS data directory](https://wiki.openmrs.org/display/docs/Application+Data+Directory) of the installation. We will refer to this directory as `oMRS_data_dir`.
- Delete the following sub-directories from the `oMRS_data_dir` directory: `configuration`, `modules` and `frontend`.
- Copy `kenyaEMR-x.y.z/configuration`, `kenyaEMR-x.y.z/modules` and `kenyaEMR-x.y.z/frontend` directories to the `oMRS_data_dir` directory.
- Apply any database migration files that are in the `kenyaEMR-x.y.z/migrations/upgrade` directory to the `openmrs` (unless specified otherwise) database. 
If non are available, skip this step.
- Restart the target servlet container.
- After a successful start of KenyaEMR;
- Apply any database migration files that are in the `kenyaEMR-x.y.z/migrations/post_upgrade` directory to the `openmrs` (unless specified otherwise) database. 
If non are available, skip this step.
    > **Note:** _You might be required to stop the target servlet container before running these migrations or perform some additional steps. If this is the case, it will be indicated on the release README._

- That's it :thumbsup:, restart the servlet container (if not already started) and you should be good to go.


## 2) Extra modules deployment

These are modules that are not part of the standard kenyaEMR module deployment i.e they're not found packaged in the `modules` directory.  
These modules are not required to run KenyaEMR, but we use them internally in our KenyaEMR deployments. You can safely ignore them.

```

KenyaEMR-v{x.y.z}.tar.xz
└── kenya-emr-release-content - Release content root folder.
    .
    .
    └── extra_modules - OpenMRS module files (omod) that are not part of official KenyaEMR release
        ├── module_x - Directory containing extra module, typically named same as <module_name>
        |    └── migrations - Database migration files. Follows the same hierarchy as shown in parent folder
        |           ├── install - SQL migration files to run during installation.
        |           |   └── *.sql (optional)
        |           ├── post_install - SQL migration files to run after installation.
        |           |   └── *.sql (optional)
        |           ├── upgrade - SQL migration files to run during an upgrade.
        |           |   └── *.sql (optional)
        |           ├── post_upgrade - SQL migration files to run after an upgrade.
        |           |   └── *.sql (optional)
        |           └── module_x.omod - the actual omod file. Must be named as <module_name>.omod
        └── module^n - Other extra modules will follow the same hierarchy as above.
                └── migrations ...
                     ├── install
                     .      .......
                     .         ......
                     .            .....
                     └── module^n.omod 

```

### Installation

- Copy `kenyaEMR-x.y.z/extra_modules` directories to the `oMRS_data_dir` directory.
- Copy omods in `kenyaEMR-x.y.z/extra_modules/<module_name>/<module_name>.omod` to `kenyaEMR-x.y.z/modules` - Do this for every exra module.
- Apply any database migration files that are in the `kenyaEMR-x.y.z/extra_modules/<module_name>/migrations/install`  directory to the `openmrs` (unless specified otherwise) database - Repeat this for every extra module.
If non are available, skip this step.
- Restart the target servlet container.
- After a successful start of KenyaEMR;
- Apply any database migration files that are in the `kenyaEMR-x.y.z/<extra_modules>/<module_name>/migrations/post_install` directory to the `openmrs` (unless specified otherwise) database - Repeat this for every module. 
If non are available, skip this step.
    > **Note:** _You might be required to stop the target servlet container before running these migrations or perform some additional steps. If this is the case, it will be indicated on the release README._

- That's it :thumbsup:, restart the servlet container (if not already started) and you should be good to go.

### Upgrade

- Copy `kenyaEMR-x.y.z/extra_modules` directories to the `oMRS_data_dir` directory.
- Copy omods in `kenyaEMR-x.y.z/extra_modules/<module_name>/<module_name>.omod` to `kenyaEMR-x.y.z/modules` - Do this for every exra module.
- Apply any database migration files that are in the `kenyaEMR-x.y.z/<extra_modules>/<module_name>/migrations/upgrade` directory to the `openmrs` (unless specified otherwise) database - Repeat this for every extra module. 
If non are available, skip this step.
- Restart the target servlet container.
- After a successful start of KenyaEMR;
- Apply any database migration files that are in the `kenyaEMR-x.y.z/<extra_modules>/<module_name>/migrations/post_upgrade` directory to the `openmrs` (unless specified otherwise) database - Repeat this for every extra module.
If non are available, skip this step.
    > **Note:** _You might be required to stop the target servlet container before running these migrations or perform some additional steps. If this is the case, it will be indicated on the release README._


- That's it :thumbsup:, restart the servlet container (if not already started) and you should be good to go.


## License

[MIT License](https://github.com/savannahghi/kenya-emr-releases/blob/main/License)

Copyright (c) 2023, Savannah Informatics Global Health Institute