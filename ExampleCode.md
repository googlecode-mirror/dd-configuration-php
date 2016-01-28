# Introduction #

In the most simple case, dd-configuration can be used to read plaintext properties files. In a more complex case, dd-configuration can be backed by a database or a composite of varying Configuration types.

The following will contain a few simple working examples to give an idea of what is possible.

# Table of Contents #



# Simple Properties - DAO Configuration Example #

This example shows how to instantiate a Properties based Configuration instance and reads various configuration values. This example also highlights the ability to reference other properties. This creates an extremely flexible configuration syntax allowing configurations to reference themselves.

## Example Properties File ##

The following is our `sample.properties` file. Note the `${...}` syntax for the `s3a.dao.dsn` property. When the value for `s3a.dao.dsn` is requested, the `${...}` instances will be replaced by their resolved value. See below for what how this property is rendered.

```
# Basic DAO settings
s3a.dao.driver=mysql
s3a.dao.dbName=s3a
s3a.dao.hostname=127.0.0.1
s3a.dao.username=demodaouser
s3a.dao.password=p@ssw0rd

# Combine some of our values into our DSN.
s3a.dao.dsn=${s3a.dao.driver}:dbname=${s3a.dao.dbName};host=${s3a.dao.hostname}
```

## Example Calling Code ##

Require the appropriate implementation of `dd_configuration_IConfiguration`, in this case `dd_configuration_PropertiesConfiguration`. The name of our properties file is passed to the constructor and several configuration values are printed.

```
<?php
require_once('dd_configuration_PropertiesConfiguration.php');
$config = new dd_configuration_PropertiesConfiguration(
    'sample.properties'
);
print "DAO Driver:   [" . $config->get('s3a.dao.driver') . "]\n";
print "DAO DB Name:  [" . $config->get('s3a.dao.dbName') . "]\n";
print "DAO Hostname: [" . $config->get('s3a.dao.hostname') . "]\n";
print "DAO Username: [" . $config->get('s3a.dao.username') . "]\n";
print "DAO Password: [" . $config->get('s3a.dao.password') . "]\n";
print "DAO DSN:      [" . $config->get('s3a.dao.dsn') . "]\n";
print "DAO DSN(raw): [" . $config->getRaw('s3a.dao.dsn') . "]\n";
?>
```

## Example Output ##

The following is the output from the above script. Of note are the differences between `get('s3a.dao.dsn')` and `getRaw('s3a.dao.dsn')`.

```
DAO Driver:   [mysql]
DAO DB Name:  [s3a]
DAO Hostname: [127.0.0.1]
DAO Username: [demodaouser]
DAO Password: [p@ssw0rd]
DAO DSN:      [mysql:dbname=s3a;host=127.0.0.1]
DAO DSN:      [${s3a.dao.driver}:dbname=${s3a.dao.dbName};host=${s3a.dao.hostname}]
```