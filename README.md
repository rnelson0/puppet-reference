# puppet-reference
A list of puppet modules, tools, testing, and architecture that are good references for current Puppet best practices.

### Modules

### Tools
* [garethr/puppet-module-skeleton](https://github.com/garethr/puppet-module-skeleton) - General purpose skeleton for a new module. Includes Gemfile, Rakefile, Travis CI, Rubocop, and other settings you should use.
* [Modulesync](https://github.com/voxpupuli/modulesync) helps synchronize consistent settings across modules in a user or organization namespace.
  * [modulesync_config reference](https://github.com/rnelson0/puppet-modulesync_config_reference) is an example of a starting configuration.
* [puppet-retrospec](https://github.com/nwops/puppet-retrospec) will help you get started with some quick and dirty rspec tests.
* [vim-puppet](https://github.com/voxpupuli/vim-puppet) provides syntax highlighting and other plugins for editing puppet files.
* [puppet-ghostbuster](https://github.com/camptocamp/puppet-ghostbuster) is a dead code detector. Requires puppetdb 3+.

### Testing

* Introduction to Testing Puppet Modules ([slides](https://www.netways.de/fileadmin/images/Events_Trainings/Events/OSDC/2016/Slides_2016/David_Schmitt_-_Introduction_to_Testing_Puppet_Modules.pdf) and [video](https://www.youtube.com/watch?v=GgNrxLfoDF8)) by [David Schmitt](https://twitter.com/dev_el_ops)

The modules below each highlight one or more aspects of rspec-puppet testing.

#### Beaker
* [puppetlabs/httpd](https://github.com/puppetlabs/puppetlabs-apache/blob/master/.travis.yml)

#### Types & Providers
* [puppetlabs/azure](https://github.com/puppetlabs/puppetlabs-azure)
* [maestrodev/maven](https://github.com/maestrodev/puppet-maven)

#### Custom Facts
* [puppetlabs/java's java_version](https://github.com/puppetlabs/puppetlabs-java/blob/master/spec/unit/facter/java_version_spec.rb)

#### Mocking an Object like `File.exists?`
* [calling the original implementation](https://relishapp.com/rspec/rspec-mocks/docs/configuring-responses/calling-the-original-implementation)

### Architecture
#### Control Repositories
A Control Repository is used to control the code deployed in Puppet environments. Puppet has two official reference repositories, and there are some public repositories that are a mix of reference architecture and practical usage. 
* [puppetlabs/control-repo](https://github.com/puppetlabs/control-repo) - Official reference architecture from Puppet, based on [Even Besterer Practices](http://garylarizza.com/blog/2015/11/16/workflows-evolved-even-besterer-practices/).
* [puppetlabs-education/classroom-control-vf](https://github.com/puppetlabs-education/classroom-control-vf) - A good reference implementation of the control repository, maintained by Puppet's Education group.
* [example42/control-repo](https://github.com/example42/control-repo) - Example 42's [reference control repository](http://www.example42.com/2016/05/11/a-modern-puppet4-control-repo/).
* [puppetinabox/controlrepo](https://github.com/puppetinabox/controlrepo) - Rob Nelson's control repository for his [PuppetInABox project](https://rnelson0.com/2015/01/08/introducing-puppetinabox-bootstrap-a-lab-setup-with-puppet/).

### Other
* Whirlwind Tour of Puppet 4 - [Slides](http://www.slideshare.net/ripienaar/whirlwind-tour-of-puppet-4) and [video](https://www.youtube.com/watch?v=5JDhAliu8SM)
* [Puppet Cookbook](http://www.puppetcookbook.com/), a collection of task oriented solutions in Puppet
* [YAML for Puppet users?](http://ask.puppetlabs.com/question/19711/yaml-for-puppet-users/) - A combination YAML primer and Guide to Puppet/YAML idiosyncracies.
