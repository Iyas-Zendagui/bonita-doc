
# Platform configuration


In this section you will learn what the Platform setup tool is and how to use it to initialize and configure the Bonita BPM Platform.


<a id="platform_setup_tool" />

## Platform setup tool overview

The *Platform setup tool* handles the creation of the database schema and the configuration of Bonita BPM Platform.  
It is located in the [deploy bundle](deploy-bundle.md) and in both [Tomcat](tomcat-bundle.md) and [JBoss](jboss-bundle.md) bundles.

::: info
In Tomcat and JBoss bundles you can find the tool in the `setup` folder
:::


### Structure

It is composed of the following items:
* `platform_conf/`
    * `initial/`: contains the default configuration of the Bonita BPM Platform, that can be customized and will be pushed when the database is created.
    * `current/`: will contain configuration files after a `pull` from the database is made.
    * `licenses/`: (Subscriptions only) ==> must contain the license file to allow Bonita BPM Platform to start without error.
    * `sql/`: SQL scripts that are used to create the Bonita BPM database tables
* `setup.sh`: Unix / Mac script to run.
* `setup.bat`: Windows script to run.
* `database.properties`: contains properties to connect to the database on which the configuration is managed.

### Functions

This tool has 3 functions:

* Initialize the database:`init`
It creates the database schema and pushes the initial configuration in the database.   
This initial configuration is taken from the `platform_conf/initial` folder.

* Retrieve current configuration: `pull`
It gets the current configuration of Bonita BPM Platform and saves it in the `platform_conf/current` folder.  

* Update current configuration: `push`
It pushes the configuration from the `platform_conf/current` folder into the database of an already initialized Bonita BPM Platform


<a id="configure_tool" />

## Configure the Platform setup tool

Before running it, the tool must be configured to point to the database of the Bonita BPM Platform.

To do so, after you have created the database, modify the tool `database.properties` file:  
Set the right db vendor by commenting and uncommenting properties inside this file and change connection url, user credentials, database name and so on.

If you are using an Oracle or Microsoft SQL Server database, you will need to add the related JDBC driver in the `lib` folder.

::: warning
This database configuration is only for the Platform setup tool. You still need to configure the database for your Tomcat or JBoss bundle.   
To do so, refer to the [Tomcat](tomcat-bundle.md) and [JBoss](jboss-bundle.md) bundles pages.
:::



<a id="init_platform_conf" />

## Initialize Bonita BPM Platform

You can initialize Bonita BPM Platform manually on an empty database using the *Platform setup tool*.

::: info
Remember that in the [Tomcat](tomcat-bundle.md) and [JBoss](jboss-bundle.md) bundles, this is done automatically.
:::

To do so, proceed as follows:

1. Create a new empty database.
2. Configure the *Platform setup tool* as explained above.
3. From the tool, run `setup.sh init` (Unix/Mac) or `setup.bat init` (Windows).

This last step will connect to the database pointed to in `database.properties`, create the tables required for the platform to run, push all configuration from the `platform_conf/initial` folder and push the licenses from the licenses folder.

That's it! You can now run Bonita BPM Platform on this database.


<a id="update_platform_conf" />

## Update Bonita BPM Platform configuration or licenses

If you want to modify the configuration of an already initialized Bonita BPM Platform, you must use the *Platform setup tool* as follows:

1. Stop Bonita BPM Platform.
2. If not already done, update the `database.properties` of the *Platform setup tool* to point to the database used by Bonita BPM Platform.
3. Run `setup.sh pull` or `setup.bat pull` from the tool. It will get the current configuration and put it in the `platform_conf/current` folder.
4. Modify the configuration files inside the `platform_conf/current` folder according to your needs.
5. If you are updating your license file, put it inside `platform_conf/licenses`, along with the existing ones. If some retrieved license files are not valid anymore, you can remove them, to delete them from the database when pushed.
6. Run `setup.sh push` or `setup.bat push` from the tool.
7. Start Bonita BPM Platform.


::: info
Note that the *Platform Setup tool* does not need the Bonita BPM Server to be running for the configuration to be updated. However, the server needs to be restarted for the changes to take effect.
:::


::: warning
Keep in mind that the folder **`platform_conf/initial`** is not used anymore once the platform has been initialized for the first time. To update your configuration, only the **`platform_conf/current`** folder is taken into account.
:::

## Troubleshooting

---

**Problem**: When I run Platform setup tool, I get the exception: `locked by an other process`

**Possible cause**: The Platform setup tool tries to access the same database (H2) as a running Bonita BPM Bundle or Bonita BPM Studio.

**Solution**: Stop Bundle or Studio before running Platform setup tool, as H2 tends to lock the database files when running.

---

**Problem**: When I run Platform setup tool, I get the exception `Cannot determine database vendor (valid values are h2, postgres, sqlserver, oracle, mysql).`

**Possible cause**: property `db.vendor` is not found when reading file `database.properties`

**Solution**: Edit file `database.properties` and ensure there is a valid `db.vendor` value. Also ensure the line is not commented (no # at the beginning of the line)

---