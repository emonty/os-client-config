===============================
os-client-config
===============================

os-client-config is a library for collecting client configuration for
using an OpenStack cloud in a consistent and comprehensive manner. It
will find cloud config for as few as 1 cloud and as many as you want to
put in a config file. It will read environment variables and config files,
and it also contains some vendor specific default values so that you don't
have to know extra info to use OpenStack

Environment Variables
---------------------

os-client-config honors all of the normal `OS_*` variables. It does not
provide backwards compatibility to service-specific variables such as
`NOVA_USERNAME`.

If you have environment variables and no config files, os-client-config
will produce a cloud config object named "openstack" containing your
values from the environment.

Service specific settings, like the nova service type, are set with the
default service type as a prefix. For instance, to set a special service_type
for trove (because you're using Rackspace) set:
::

  export OS_DATABASE_SERVICE_TYPE=rax:database

Config Files
------------

os-client-config will look for a file called clouds.yaml in the following
locations:

* Current Directory
* ~/.config/openstack
* /etc/openstack

The first file found wins.

The keys are all of the keys you'd expect from `OS_*` - except lower case
and without the OS prefix. So, username is set with `username`.

Service specific settings, like the nova service type, are set with the
default service type as a prefix. For instance, to set a special service_type
for trove (because you're using Rackspace) set:
::

  database_service_type: 'rax:database'

An example config file is probably helpful:
::

  clouds:
    mordred:
      cloud: hp
      username: mordred@inaugust.com
      password: XXXXXXXXX
      project_id: mordred@inaugust.com
      region_name: region-b.geo-1
      dns_service_type: hpext:dns
    monty:
      auth_url: https://region-b.geo-1.identity.hpcloudsvc.com:35357/v2.0
      username: monty.taylor@hp.com
      password: XXXXXXXX
      project_id: monty.taylor@hp.com-default-tenant
      region_name: region-b.geo-1
      dns_service_type: hpext:dns
    infra:
      cloud: rackspace
      username: openstackci
      password: XXXXXXXX
      project_id: 610275
      region_name: DFW,ORD,IAD

You may note a few things. First, since auth_url settings are silly
and embarrasingly ugly, known cloud vendors are included and may be referrenced
by name. One of the benefits of that is that auth_url isn't the only thing
the vendor defaults contain. For instance, since Rackspace lists
`rax:database` as the service type for trove, os-client-config knows that
so that you don't have to.

Also, region_name can be a list of regions. When you call get_all_clouds,
you'll get a cloud config object for each cloud/region combo.

As seen with `dns_service_type`, any setting that makes sense to be per-service,
like `service_type` or `endpoint` or `api_version` can be set by prefixing
the setting with the default service type. That might strike you funny when
setting `service_type` and it does me too - but that's just the world we live
in.

Usage
-----

The simplest and least useful thing you can do is:
::

  python -m os_client_config.config

Which will print out whatever if finds for your config. If you want to use
it from python, which is much more likely what you want to do, things like:

Get a named cloud.
::

  import os_client_config

  cloud_config = os_client_config.OpenStackConfig().get_one_cloud(
      'hp', 'region-b.geo-1')
  print(cloud_config.name, cloud_config.region, cloud_config.config)

Or, get all of the clouds.
::
  import os_client_config

  cloud_config = os_client_config.OpenStackConfig().get_all_clouds()
  for cloud in cloud_config:
      print(cloud.name, cloud.region, cloud.config)

* Free software: Apache license
* Documentation: http://docs.openstack.org/developer/os-client-config
* Source: http://git.openstack.org/cgit/openstack/os-client-config
* Bugs: http://bugs.launchpad.net/os-client-config
