# puppet-reference
A list of puppet modules, tools, testing, and architecture that are good references for current Puppet best practices.

### Puppet 4

* [Puppet Tea](https://github.com/voxpupuli/puppet-tea/tree/master/types) - Custom [Defined Types](https://docs.puppet.com/puppet/latest/lang_defined_types.html) that can be used to shorten the parameter list definitions and/or when complex types are used in multiple places.

### Tools
* [Puppet Development Kit](https://puppet.com/download-puppet-development-kit) - All-in-one CLI for writing and testing puppet modules ([docs](https://docs.puppet.com/pdk/latest/index.html), [announcement](https://puppet.com/blog/develop-modules-faster-new-puppet-development-kit), [github](https://github.com/puppetlabs/pdk)); provides Gemfile, Rakefile, Travis CI, Rubocop, and other settings you should use.

* [Modulesync](https://github.com/voxpupuli/modulesync) helps synchronize consistent settings across modules in a user or organization namespace.
  * [modulesync_config reference](https://github.com/rnelson0/puppet-modulesync_config_reference) is an example of a starting configuration.
* [puppet-retrospec](https://github.com/nwops/puppet-retrospec) The only tool you need to generate puppet code, tests, modules, facts, types, providers, data and everything else..
* [puppet-ghostbuster](https://github.com/camptocamp/puppet-ghostbuster) is a dead code detector. Requires puppetdb 3+.
* [puppet-debugger](https://github.com/nwops/puppet-debugger) A interactive live debugger and REPL for the puppet language
* [puppet-strings](https://github.com/puppetlabs/puppet-strings) generates automatic documentation for properly configured modules. See [the Puppet blog](https://puppet.com/blog/using-puppet-strings-generate-great-documentation-puppet-modules) for details.
  * Puppet's [NTP module](https://github.com/puppetlabs/puppetlabs-ntp) is a recommended reference module.

### Editor support

* [VSCode Plugin](https://marketplace.visualstudio.com/items?itemName=jpogran.puppet-vscode): syntax highlighting, code snippets, linting, IntelliSense (using your deployed modules), validation, import from `puppet resource`, node graph view, PDK integration ([blog](https://puppet.com/blog/announcing-puppet-visual-studio-code), [VSCode](https://code.visualstudio.com/download))
* [JetBrain's RubyMine](https://www.jetbrains.com/help/ruby/puppet.html): style guide, syntax and error highlighting, some refactoring support, code completion, structure view [download](https://www.jetbrains.com/ruby/download/)
* [vim-puppet](https://github.com/voxpupuli/vim-puppet): provides syntax highlighting and other plugins for editing puppet files.
* [Atom](https://atom.io/): [syntax highlighting](https://atom.io/packages/language-puppet), [linting](https://atom.io/packages/linter-puppet-lint)

### Testing

* Introduction to Testing Puppet Modules ([slides](https://www.netways.de/fileadmin/images/Events_Trainings/Events/OSDC/2016/Slides_2016/David_Schmitt_-_Introduction_to_Testing_Puppet_Modules.pdf) and [video](https://www.youtube.com/watch?v=GgNrxLfoDF8)) by [David Schmitt](https://twitter.com/dev_el_ops)

* Main [rspec-puppet docs site](http://rspec-puppet.com)

The modules below each highlight one or more aspects of rspec-puppet testing.

#### Beaker
* [puppetlabs/httpd](https://github.com/puppetlabs/puppetlabs-apache/blob/master/.travis.yml)

#### Types & Providers
* [Resource API](https://github.com/puppetlabs/puppet-resource_api)
* Legacy examples:
  * [puppetlabs/azure](https://github.com/puppetlabs/puppetlabs-azure)
  * [maestrodev/maven](https://github.com/maestrodev/puppet-maven)

#### Custom Facts
* [puppetlabs/java's java_version](https://github.com/puppetlabs/puppetlabs-java/blob/master/spec/unit/facter/java_version_spec.rb)
* [puppetinabox puppet_role fact](https://github.com/puppetinabox/controlrepo/blob/539b2adb474f9028c59565b40fe340a9a59f57e0/dist/profile/lib/facter/puppet_role.rb) and [test](https://github.com/puppetinabox/controlrepo/blob/539b2adb474f9028c59565b40fe340a9a59f57e0/dist/profile/spec/unit/facter/puppet_role_spec.rb) - The *puppet_role* fact is calculated on another fact's value, *hostname*, which requires resetting BOTH custom facts for every test.

#### Mocking an Object like `File.exists?`
* [calling the original implementation](https://relishapp.com/rspec/rspec-mocks/docs/configuring-responses/calling-the-original-implementation)

#### Mocking a puppet 4 function
If you have a puppet 4 function that is called and you don't want it to execute and want to create a mock/fake to be used in the real one's place you can use the puppet-rpsec precondition.  This function definition will override will be added to the loader and the original function will not be attempted to be loaded from disk since there already exists the function, your mock!

```puppet

# Puppet code that calls puppet 4 function
class profile::sqlserver {

  $iso_location = 'C:/SW_DVD9_SQL_Svr_Ent_Core_2012_English_MLF_X17-99682.IMG'
  googlecloudstorage::gcpsignedfile { $iso_location:
    ensure     => 'present',
    signed_url => googlecloudstorage::generate_signed_url('20m', '/etc/puppetlabs/puppetserver/ssh/Terraform-puppet-credentials.json', 'gs://puppet-provisioning-binaries/SQL Server 2012 - Enterprise Edition/SW_DVD9_SQL_Svr_Ent_Core_2012_English_MLF_X17-99682.IMG'),
    # signed_url => 'https://asdasdasd.com/file.iso',
    mounted    => true,
  }
}
```
```ruby
# Puppet 4 function
# @param [String] duration
# Example use case
#   
# with s = seconds, m = minutes, h = hours, d = days
require 'open3'
Puppet::Functions.create_function(:'googlecloudstorage::generate_signed_url') do
  # Signatures
  dispatch :generate_signed_url do
    required_param 'Pattern[/(s)|(m)|(h)|(d)$/]', :duration
    required_param 'String', :private_key_file
    required_param 'String', :google_cloud_storage_url
    return_type 'String'
  end

  # Implementations
  def generate_signed_url(duration, private_key_file, google_cloud_storage_url)
    Puppet.info("duration: #{duration}")
    Puppet.info("private_key_file: #{private_key_file}")
    Puppet.info("google_cloud_storage_url: #{google_cloud_storage_url}")
    stdout, stderr, status = Open3.capture3("gsutil signurl -d #{duration} '#{private_key_file}' '#{google_cloud_storage_url}'")
    if not status.success?
      raise Puppet::ParseError, "gcloud failure: exitcode #{status.exitstatus} stdout #{stdout} stderr #{stderr}"  
    end
    return stdout.split(' ')[-1]
  end
end
```
```ruby
# Test
require 'spec_helper'

describe 'profile::sqlserver' do
  context 'with default values for all parameters' do
    let(:pre_condition) {
      'function googlecloudstorage::generate_signed_url($x, $y, $z) { return \'https://asdasdasd.com/file.iso\' }'
    }

     it { should contain_googlecloudstorage__gcpsignedfile('C:/SW_DVD9_SQL_Svr_Ent_Core_2012_English_MLF_X17-99682.IMG').with({
      :ensure     => 'present',
      :signed_url => 'https://asdasdasd.com/file.iso',
    }) }
  end
end

```

#### Including dependent classes
* [puppetlabs/apache's defined type apache::vhost](https://github.com/puppetlabs/puppetlabs-apache/blob/5d2e65ed3df9d39fb7d99b5948584035f8b662c3/spec/defines/vhost_spec.rb#L4-L6) requires the class `apache` to be included as well, via `let :pre_condition do .. end`

#### Template Results
Templates are often fed values by class parameters. Test for portions of the content based on the values you expect to find with various parameter settings, rather than testing the entire contents.
* [puppetlabs/apache](https://github.com/puppetlabs/puppetlabs-apache) - httpd.conf.erb's [DefaultType setting](https://github.com/puppetlabs/puppetlabs-apache/blob/5d2e65ed3df9d39fb7d99b5948584035f8b662c3/templates/httpd.conf.erb#L50-L52) is tested [here](https://github.com/puppetlabs/puppetlabs-apache/blob/5d2e65ed3df9d39fb7d99b5948584035f8b662c3/spec/classes/apache_spec.rb#L152-L184)

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
