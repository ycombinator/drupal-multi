Description
===========

This is a template for deploying a [Drupal](http://wordpress.org/)-powered web
site across multiple webservers. By default, this template will create a Cloud
Load Balancer, a Cloud Database instance, and 2 Cloud Servers to act as webservers.
The webservers will connect to the database instance and the load balancer will
distribute incoming traffic in round-robin fashion amongst the web servers.

This template leverages [chef-solo](http://docs.opscode.com/chef_solo.html)
to install and configure Drupal on the webservers.

Requirements
============
* A Rackspace Cloud account. Signup at https://cart.rackspace.com/cloud/.
* [python-heatclient](https://github.com/openstack/python-heatclient)
`>= v0.2.8`:

```bash
pip install python-heatclient
```

We recommend installing the client within a [Python virtual
environment](http://www.virtualenv.org/).

Example Usage
=============
Here is an example of how to deploy this template using the
[python-heatclient](https://github.com/openstack/python-heatclient):

1. First, export some values required to connect to an Rackspace Cloud. This
   is a one-time setup step.

    ```
    export OS_USERNAME=<Your Rackspace cloud account username>
    export OS_PASSWORD=<Your Rackspace cloud account password>
    export OS_TENANT_ID=<Your Rackspace cloud tenant ID>
    export OS_AUTH_URL=<Your Rackspace cloud's authentication endpoint URL>
    ```    
    * For US, set `OS_AUTH_URL` to https://identity.api.rackspacecloud.com/v2.0/
    * For UK, set `OS_AUTH_URL` to https://lon.identity.api.rackspacecloud.com/v2.0/
    
2. Ensure that you have created an SSH keypair and uploaded the public key
   to your Rackspace cloud account, in the same region as you'll be creating
   your stack. This is also a one-time setup step.

3. Then, create the stack.

    ```
    heat \
      --os-region-name DFW \
      stack-create my_drupal_site \
      -u https://raw.githubusercontent.com/ycombinator/drupal-multi/master/drupal-multi.yaml \
      -P ssh_keypair_name=mine \
      -P ssh_private_key="$(cat ~/.ssh/id_rsa)"
    ```
    
The example above assumes the following:
* You want to create the stack in the `DFW` region of the Rackspace Cloud,
* You want to name your stack `my_drupal_site`,
* An SSH keypair named `mine` exists in the same Rackspace cloud region in which you want to create the stack, and
* The corresponding private key for this kepair is stored in `~/.ssh/id_rsa`.

Parameters
==========
Parameters can be replaced with your own values when standing up a stack. Use
the `-P` flag to specify a custom parameter.

* `webserver_count`: Number of web servers to create (Default: 2)
* `webserver_flavor`: The Cloud Server flavor to use when creating the web servers. Optional; default: 2 GB Performance.
* `webserver_image`: The Cloud Server image to use when create the web servers. Optional; default: Ubuntu 12.04 LTS (Precise Pangolin)
* `ssh_keypair_name`: The name of the SSH keypair uploaded to the same Rackspace Cloud region as the one in which you want to create the stack. Required.
* `ssh_private_key`: Contents of the SSH private key corresponding to the SSH keypair. Required.
* `db_instance_flavor`: The Cloud Server flavor to use when creating the database instance. Optional; default: 1GB Instance
* `db_instance_size`: The size of the database disk volume, in GB. Optional; default: 10
* `db_username`: Username used by the Drupal web application to connect to the database. Optional; default: drupal_db_user

Outputs
=======
Once a stack comes online, use `heat --os-region-name DFW output-list` to see all available outputs.
Use `heat --os-region-name DFW output-show <OUTPUT NAME>` to get the value fo a specific output.

* `site_ip`: Public IP address of the web site (i.e. load balancer)

Contributing
============
There are substantial changes still happening within the [OpenStack Heat](https://wiki.openstack.org/wiki/Heat) project. Template contribution
guidelines will be drafted in the near future.

License
=======
```
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
