java-libraries
==============

Description
===========

This cookbook contains LWRPs and HWRPs for handling optional 
common configuration of the JVM. The LWRPs and/or libraries in 
this cookbook should be referenced from recipes that run after 
the installation and configuration of Java via the cookbook at 
https://github.com/socrata-cookbooks/java.

Contributing
------------
1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

Resources/Providers
===================

## `java_certificate` LWRP

This cookbook contains the `java_certificate` LWRP which simplifies
adding certificates to a java keystore. It can also populate the keystore
with a certificate retrieved from a given SSL end-point. It defaults
to the default keystore `<java_home>/jre/lib/security/cacerts` with the
default password if a specific keystore is not provided.

### Actions

- `:install`: installs a certificate.
- `:remove`: removes a certificate.

### Attribute Parameters

- `cert_alias`: The alias of the certificate in the keystore. This defaults
  to the name of the resource.

Optional parameters:
- `java_home`: the java home directory. Defaults to `node['java']['java_home']`.
- `keystore_path`: the keystore path. Defaults to `node['java']['java_home']/jre/lib/security/cacerts`.
- `keystore_passwd`: the keystore password. Defaults to 'changeit' as specified by the Java Documentation.

Only one of the following 
- `cert_data`: the certificate data to install
- `cert_file`: path to a certificate file to install
- `ssl_endpoint`: an SSL end-point from which to download the certificate

### Examples

    java_certificate "Install LDAP server certificate to Java CA keystore for Jenkins" do
        cert_alias node['jenkins']['ldap']['url'][/\/\/(.*)/, 1]
        ssl_endpoint node['jenkins']['ldap']['url']
        action :install
        notifies :restart, "runit_service[jenkins]", :delayed
    end

Usage
=====

Simply include the `java` recipe where ever you would like Java installed.

To install Oracle flavored Java override the `node['java']['install_flavor']` attribute with in role:

    name "java"
    description "Install Oracle Java on Ubuntu"
    default_attributes(
      "java" => {
        "install_flavor" => "oracle"
      }
    )
    run_list(
      "recipe[java]"
    )

To install IBM flavored Java, set the required attributes:

    name "java"
    description "Install IBM Java on Ubuntu"
    default_attributes(
      "java" => {
        "install_flavor" => "ibm",
        "ibm" => {
          "accept_ibm_download_terms" => true,
          "url" => "http://fileserver.example.com/ibm-java-x86_64-sdk-7.0-4.1.bin",
          "checksum" => "The SHA256 checksum of the bin"
        }
      }
    )
    run_list(
      "recipe[java]"
    )

