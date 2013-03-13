# puppet-rethinkdb
A [Puppet](https://puppetlabs.com/) module for
[RethinkDB](http://www.rethinkdb.com/).

## Dependencies
* [stdlib](https://github.com/puppetlabs/puppetlabs-stdlib)
* [apt](https://github.com/puppetlabs/puppetlabs-apt)

## Limitations
* Only works under Ubuntu. Sorry.
* Only supports a single instance per machine
* You must specify a user/group for the service (leaving it
  blank is not an option)

## Installation
* Via puppet forge: http://forge.puppetlabs.com/tmont/rethinkdb
* Via git submodule: `git submodule add git@github.com:tmont/puppet-rethinkdb path/to/modules/rethinkdb`

## Usage
With all the defaults:

```puppet
include rethinkdb
```

With custom parameters:

```puppet
# these are the default values for each parameter
class { 'rethinkdb':
	rethinkdb_user => 'user'
	rethinkdb_group => 'user',
	instance_name => 'default',
	driver_port => 28015,
	http_port => 8080,
	cluster_port => 29015,
	rethinkdb_bind => 'all', #slightly dangerous, see below
	rethinkdb_join => undef #e.g. "mymachine:29015"
}
```
The above will generate the following conf file
(in `/etc/rethinkdb/instances.d/default.conf`):

```
# Automatically generated by puppet
runuser=rethinkdb
rungroup=rethinkdb
directory=/var/lib/rethinkdb/instances.d/default
pid-file=/var/run/rethinkdb/instances.d/default.pid
driver-port=28015
cluster-port=29015
http-port=8081

bind=all
```

The default for the `bind` parameter is probably not ideal, but it'll
make everything work out of the box. You can probably change it to the
machine's IP address on the LAN (e.g. `10.0.*.*` or `192.168.*.*`) for
something less open. Your mileage may vary.

See the [RethinkDB docs](http://www.rethinkdb.com/docs/guides/startup/)
for more detail on configuration options.


## What actually happens
* Installs rethinkdb via its [ppa](http://www.rethinkdb.com/docs/install/)
* Creates a user/group for the rethinkdb service
* Generates a conf file and puts it into `/etc/rethinkdb/instances.d`
* Runs `rethinkdb create -d /var/lib/rethinkdb/instances.d`, which is
  the default directory
* Ensures the `init.d` service will run on startup

