# <a name="title"></a> chef-homesick [![Build Status](https://secure.travis-ci.org/fnichol/chef-homesick.png?branch=master)](http://travis-ci.org/fnichol/chef-homesick)

## <a name="description"></a> Description

LWRP to manage [homesick][homesick] castles.

## <a name="usage"></a> Usage

Simply include `recipe[homesick]` in your run\_list and the `homesick_repo`
resource will be available.

To use `recipe[homesick::data_bag]`, include it in your run\_list and have a
data bag called `"users"` with an item for your user like the following:

    {
      "id"  : "wigglebottom",
      "homesick_castles"  : [
        { "name"    : "dotvim",
          "source"  : "git://github.com/fnichol/dotvim.git"
        },
        { "name"    : "dotfiles",
          "source"  : "git://github.com/fnichol/dotfiles.git",
          "action"  : "update"
        }
      ]
    }

The data bag recipe will iterate through a list of usernames defined in
`node['users']` and attempt to pull in the homesick information from the data
bag item. In other words, having:

    node['users'] = ['hsolo']

will set up the `hsolo` user's castles and not use the `wigglebottom` user.

## <a name="requirements"></a> Requirements

### <a name="requirements-chef"></a> Chef

Tested on 10.12.0 but newer and older version should work just fine.
File an [issue][issues] if this isn't the case.

### <a name="requirements-platform"></a> Platform

The following platforms have been tested with this cookbook, meaning that the
recipes run on these platforms without error:

* ubuntu
* debian
* mac\_os\_x
* suse
* openbsd

Please [report][issues] any additional platforms so they can be added.

### <a name="requirements-cookbooks"></a> Cookbooks

There are **no** external cookbook dependencies. If you are using [RVM][rvm],
then you should consider using the [rvm cookbook][rvm_cb].

## <a name="installation"></a> Installation

Depending on the situation and use case there are several ways to install
this cookbook. All the methods listed below assume a tagged version release
is the target, but omit the tags to get the head of development. A valid
Chef repository structure like the [Opscode repo][chef_repo] is also assumed.

## From the Opscode Community Platform

To install this cookbook from the Opscode platform, use the *knife* command:

    knife cookbook site install homesick

### <a name="installation-librarian"></a> Using Librarian-Chef

[Librarian-Chef][librarian] is a bundler for your Chef cookbooks.
Include a reference to the cookbook in a [Cheffile][cheffile] and run
`librarian-chef install`. To install Librarian-Chef:

    gem install librarian
    cd chef-repo
    librarian-chef init

To use the Opscode platform version:

    echo "cookbook 'homesick'" >> Cheffile
    librarian-chef install

Or to reference the Git version:

    cat >> Cheffile <<END_OF_CHEFFILE
    cookbook 'homesick',
      :git => 'git://github.com/fnichol/chef-homesick.git', :ref => 'v0.3.2'
    END_OF_CHEFFILE
    librarian-chef install

### <a name="installation-kgc"></a> Using knife-github-cookbooks

The [knife-github-cookbooks][kgc] gem is a plugin for *knife* that supports
installing cookbooks directly from a GitHub repository. To install with the
plugin:

    gem install knife-github-cookbooks
    cd chef-repo
    knife cookbook github install fnichol/chef-homesick/v0.3.2

### <a name="installation-tarball"></a> As a Tarball

If the cookbook needs to downloaded temporarily just to be uploaded to a Chef
Server or Opscode Hosted Chef, then a tarball installation might fit the bill:

    cd chef-repo/cookbooks
    curl -Ls https://github.com/fnichol/chef-homesick/tarball/v0.3.2 | tar xfz - && \
      mv fnichol-chef-homesick-* homesick

### <a name="installation-gitsubmodule"></a> As a Git Submodule

A dated practice (which is discouraged) is to add cookbooks as Git
submodules. This is accomplishes like so:

    cd chef-repo
    git submodule add git://github.com/fnichol/chef-homesick.git cookbooks/homesick
    git submodule init && git submodule update

**Note:** the head of development will be linked here, not a tagged release.

## <a name="recipes"></a> Recipes

### <a name="recipes-default"></a> default

This recipe is a no-op and does nothing.

Use this recipe when you only want access to the `homesick_castle` LWRP.

### <a name="recipes-data-bag"></a> data_bag

Fetches an list of homesick castles with data drawn from a data bag. The
default data bag is `"users"` and the list of user accounts to iterate through
is set on `node['users']`.

Use this recipe when you want data bag driven data in your workflow.

## <a name="attributes"></a> Attributes

### <a name="attributes-data-bag-name"></a> data_bag_name

The data bag name containing a group of user account information. This is used
by the `data_bag` recipe to use as a database of user accounts. The default is
`"users"`.

### <a name="attributes-user-array-node-attr"></a> user_array_node_attr

The node attributes containing an array of users to be managed. If a nested
hash in the node's attributes is required, then use a `/` between subhashes.
For example, if the users' array is stored in `node['system']['accounts']`),
then set `node['homesick']['user_array_node_attr']` to `"system/accounts"`.

The default is `"users"`.

## <a name="lwrps"></a> Resources and Providers

### <a name="lwrps-castle"></a> homesick_castle

### <a name="lwrps-castle-actions"></a> Actions

<table>
  <thead>
    <tr>
      <th>Action</th>
      <th>Description</th>
      <th>Default</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>install</td>
      <td>Clones the castle and symlinks it.</td>
      <td>Yes</td>
    </tr>
    <tr>
      <td>update</td>
      <td>Pulls updates for the castle and re-symlinks it.</td>
      <td>&nbsp;</td>
    </tr>
  </tbody>
</table>

### <a name="lwrps-castle-attributes"></a> Attributes

<table>
  <thead>
    <tr>
      <th>Attribute</th>
      <th>Description</th>
      <th>Default Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>name</td>
      <td><b>Name attribute:</b> The name of the homesick castle.</td>
      <td><code>nil</code></td>
    </tr>
    <tr>
      <td>user</td>
      <td>The user using the castle.</td>
      <td><code>nil</code></td>
    </tr>
    <tr>
      <td>source</td>
      <td>The clone URL (http, https, git, etc.) of the castle.</td>
      <td><code>nil</code></td>
    </tr>
  </tbody>
</table>

#### <a name="lwrps-castle-examples"></a> Examples

##### Install a Castle

    homesick_castle 'dotvim' do
      user    'wigglebottom
      source  'git://github.com/fnichol/dotvim.git'
    end

**Note:** the install action is the default.

##### Pull Updates for a Castle

    homesick_castle 'dotfiles' do
      user    'wigglebottom
      source  'git://github.com/fnichol/dotfiles.git'
      action  :update
    end

## <a name="development"></a> Development

* Source hosted at [GitHub][repo]
* Report issues/Questions/Feature requests on [GitHub Issues][issues]

Pull requests are very welcome! Make sure your patches are well tested.
Ideally create a topic branch for every separate change you make.

## <a name="license"></a> License and Author

Author:: [Fletcher Nichol][fnichol] (<fnichol@nichol.ca>) [![endorse](http://api.coderwall.com/fnichol/endorsecount.png)](http://coderwall.com/fnichol)

Copyright 2011, Fletcher Nichol

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

[chef_repo]:    https://github.com/opscode/chef-repo
[cheffile]:     https://github.com/applicationsonline/librarian/blob/master/lib/librarian/chef/templates/Cheffile
[homesick]:     https://github.com/technicalpickles/homesick
[kgc]:          https://github.com/websterclay/knife-github-cookbooks#readme
[librarian]:    https://github.com/applicationsonline/librarian#readme
[rvm]:          http://rvm.beginrescueend.com
[rvm_cb]:       https://github.com/fnichol/chef-rvm

[fnichol]:      https://github.com/fnichol
[repo]:         https://github.com/fnichol/chef-homesick
[issues]:       https://github.com/fnichol/chef-homesick/issues
