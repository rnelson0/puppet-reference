# puppet-reference
A list of puppet modules, tools, testing, and architecture that are good references for current Puppet best practices.

### Modules

### Tools
* [garethr/puppet-module-skeleton](https://github.com/garethr/puppet-module-skeleton) - General purpose skeleton for a new module. Includes Gemfile, Rakefile, Travis CI, Rubocop, and other settings you should use.
* [Modulesync](https://github.com/voxpupuli/modulesync) helps synchronize consistent settings across modules in a user or organization namespace.
  * [modulesync_config reference](https://github.com/rnelson0/puppet-modulesync_config_reference) is an example of a starting configuration.

### Testing
These modules highlight good rspec-puppet tests.

#### Types & Providers
* [puppetlabs/azure](https://github.com/puppetlabs/puppetlabs-azure)
* [maestrodev/maven](https://github.com/maestrodev/puppet-maven)

#### Custom Facts
* [puppetlabs/java's java_version](https://github.com/puppetlabs/puppetlabs-java/blob/master/spec/unit/facter/java_version_spec.rb)

### Architecture
A Control Repository is used to control the code deployed in Puppet environments.
* [puppetlabs/control-repo](https://github.com/puppetlabs/control-repo) - Official reference architecture from Puppet Labs, based on [Even Besterer Practices](http://garylarizza.com/blog/2015/11/16/workflows-evolved-even-besterer-practices/).
* [puppetlabs-education/classroom-control-vf](https://github.com/puppetlabs-education/classroom-control-vf) - A good reference implementation of the controlrepo, maintained by Puppet Labs's Education group.


### Other
* [Puppet Cookbook](http://www.puppetcookbook.com/), a collection of task oriented solutions in Puppet
* [YAML for Puppet users?](http://ask.puppetlabs.com/question/19711/yaml-for-puppet-users/) - A combination YAML primer and Guide to Puppet/YAML idiosyncracies.


