# java_libs

![Build Status](https://travis-ci.org/rakutentech/chef_java_libs.svg?branch=master)

## Description

This cookbook contains LWRPs and HWRPs for handling optional common configuration of the JVM. The LWRPs and/or libraries in this cookbook should be referenced from recipes that run after the installation and configuration of Java via the cookbook at https://github.com/socrata-cookbooks/java.

## Resources/Providers

### `java_certificate` LWRP

This cookbook contains the `java_certificate` LWRP which simplifies adding certificates to a java keystore. It can also populate the keystore with a certificate retrieved from a given SSL end-point. It defaults to the default keystore `<java_home>/jre/lib/security/cacerts` with the default password if a specific keystore is not provided.

## Actions

* `:install`: installs a certificate.
* `:remove`: removes a certificate.

## Attribute Parameters

* `cert_alias`: The alias of the certificate in the keystore. This defaults to the name of the resource.

Optional parameters:

* `java_home`: the java home directory. Defaults to `node['java']['java_home']`.
* `keystore_path`: the keystore path. Defaults to `node['java']['java_home']/jre/lib/security/cacerts`.
* `keystore_passwd`: the keystore password. Defaults to 'changeit' as specified by the Java Documentation.
* `server_name`: server name to use for the SNI header (openssl s_client parameter `-servername`).

Only one of the following

* `cert_data`: the certificate data to install
* `cert_file`: path to a certificate file to install
* `ssl_endpoint`: an SSL end-point from which to download the certificate

### Examples

Simple example:

```ruby
java_certificate 'Install LDAP server certificate to Java CA keystore for Jenkins' do
  cert_alias node['jenkins']['ldap']['url'][/\/\/(.*)/, 1]
  ssl_endpoint node['jenkins']['ldap']['url']
  action :install
  notifies :restart, 'runit_service[jenkins]', :delayed
end
```

SNI (Server Name Indication) support example:

```ruby
java_certificate 'Install Self Signed server certificate with SNI Support' do
  cert_alias 'bar.com'
  ssl_endpoint 'foo.com'
  server_name 'bar.com'
  action :install
end
```
