***This project has been merged into and replaced by [awesome-puppet](https://github.com/rnelson0/awesome-puppet). The contents below are data that did not fit into the format of awesome-puppet or is outdated, but is left for archival purposes.***

# puppet-reference
A list of puppet modules, tools, testing, and architecture that are good reference implementations of the [current Puppet best practices](https://github.com/puppetlabs/best-practices).

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

### Other
* Whirlwind Tour of Puppet 4 - [Slides](http://www.slideshare.net/ripienaar/whirlwind-tour-of-puppet-4) and [video](https://www.youtube.com/watch?v=5JDhAliu8SM)
* [Puppet Cookbook](http://www.puppetcookbook.com/), a collection of task oriented solutions in Puppet
* [YAML for Puppet users?](http://ask.puppetlabs.com/question/19711/yaml-for-puppet-users/) - A combination YAML primer and Guide to Puppet/YAML idiosyncracies.
