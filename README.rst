=======================================
OpenStack recipes for OpenPOWER Servers
=======================================

This project provides recipes for provisioning OpenStack clouds and
application clusters on OpenPOWER servers::

    - Private cloud w/ and w/o Swift Object Storage
    - Database as a Service (Trove)
    - Standalone Swift clusters

Each recipe is composed of the following solution specific files::

    - README-<solution>.rst
    - bom/<solution>.pdf
    - configs/<solution>.yml

The README-<solution>.rst file provides instructions to install and configure
the given solution.  The starting point here is a set of bare metal servers
and switches that have been appropriately racked and cabled according to
the reference bom file.  The operating system (and solution stack) will be
installed by the toolkit.

Each solution is comprised of a unique set of hardware components that are
described in a solution specific Bill of Materials file -- ie.
bom/<solution>.pdf. This file provides information such as model numbers and
feature codes to simplify the ordering process and it provides racking and
cabling rules for the preferred layout of servers, switches, and cables.

The configuration file describes the software and hardware configuration of the
solution. It maps each server to a solution specific node type which is
referred to in the file as a node template and each node template to a managed
set of operating system resources including networks, VLANs, VXLANS, disks, and
users. It also provides mappings between networks, network adapters, and switch
ports. Examples of node templates are controller, compute, and ceph-osd.

The configuration file comes pre-configured with a specific number of servers,
disks, switches, networks, etc. that may need to be adjusted to reflect the
actual configuration. The user is expected to make these adjustments and
specify site specific parameters such as external ip addresses, so that the
cluster is properly integrated into the data center. The configuration file
includes comments to facilitate making these changes. Information is also
provided in README files of related projects.

.. Note:: Private cloud is described in README-dbaas.rst

  There is not necessarily a one to one to one relationship between the
  three files listed above. The README file is the master document and should
  list the specific bom(s) and configuration file(s) that apply.

Getting Started
---------------

The toolkit runs on an Ubuntu 16.04 OpenPOWER server or VM that is connected
to the internet and management switch in the cluster to be configured.

#. Get a local copy of this github project::

   $ git clone git://github.com/open-power-ref-design/openstack-recipes
   $ cd openstack-recipes
   $ git checkout v1.3.0

#. Choose one of the following solutions to deploy:

   - `README-dbaas.rst <https://github.com/open-power-ref-design/openstack-recipes/blob/master/README-dbaas.rst>`_
   - `README-swift.rst <https://github.com/open-power-ref-design/openstack-recipes/blob/master/README-swift.rst>`_

   The private compute cloud solution is described in the DBaaS BOM below.

#. Read the associated Bill of Materials (BOM) file as noted in the README, one of:

   - `bom/dbaas.pdf <https://github.com/open-power-ref-design/openstack-recipes/blob/master/bom/dbaas.pdf>`_
   - `bom/swift.pdf <https://github.com/open-power-ref-design/openstack-recipes/blob/master/bom/swift.pdf>`_

   The private compute cloud BOM is included in the DBaaS BOM.

#. Rack and cable hardware as indicated in the selected BOM

#. Select a solution specific configuration file to use as a starting point for
   your configuration, one of::

   - configs/private-compute-cloud.yml
   - configs/dbaas.yml
   - configs/swift-large.yml
   - configs/swift-medium.yml
   - configs/swift-small.yml
   - configs/swift-small-with-compute-cloud.yml

#. Place the selected configuration file to be activated::

   $ git clone git://github.com/open-power-ref-design/cluster-genesis
   $ pushd cluster-genesis
   $ git checkout 1.0.1
   $ cp ../configs/<selected filename> config.yml
   $ mkdir -p domain/scripts/
   $ cp ../scripts/validate_config.py domain/scripts/
   $ popd

#. Edit the *placed* config.yml file to reflect the actual configuration

   This file tells the toolkit which servers to install and how they should be
   configured. Servers are indexed in the config file based by switch port.
   Switches are then programmed to allow tagged VLAN and VXLAN messages between
   named networked interfaces.

   Instructions for editing the config file are provided in the file
   itself.  Additional information may be found in the
   *TBD-cluster-genesis-user-guide*.
   Depending on the solution, additional settings may be required.  These
   settings if required are located in the solution specific README file
   within this project.

   Within each solution's config file is a bootstrap section that contains the
   GIT_BRANCH and SOLUTION_BRANCH variables.  The GIT_BRANCH variable should be
   set to the branch or tag level of the code to load for os-services.  The
   SOLUTION_BRANCH should be set to the level of openstack-recipes to pull the
   bootstrap-solution.sh script from.

#. Validate the *placed* config.yml file by running the following command::

   $ ./scripts/validate_config.py --file ../cluster-genesis/config.yml

#. Launch the toolkit

   In general, the toolkit is divided into three phases:

   i. Load Linux and the solution specific installer

      In this phase, Linux is loaded on each server. On one of these servers
      as indicated in the config.yml file, solution specific install tools
      are installed. These tools need to be manually configured to achieve the
      desired result in the solution, which is undertaken in the next phase.

      In general, follow the instructions provided on the console. At the end
      of this phase, upon successful completion, the installation process is
      continued on another server. Log into the indicated server. The user
      credentials for this server are provided in *placed* config.yml file
      in the following fields::

      - userid-default
      - password-default

   ii. Configure solution specific installer

       The solution is composed by multiple projects which are identified
       below in the Related Projects section. Each of these projects may
       require customization. Consult the README file in each project to
       identify the settings that should be applied.

       Please note that solution specific user credentials, user id and
       default passwords, are documented in these README files.  For example,
       the user account kibana and password.

   iii. Install the solution

        In this phase, the solution is installed, configured, and activated.


Related Projects
----------------

OpenStack based solutions utilize the following projects:

   - `cluster-genesis <https://github.com/open-power-ref-design/cluster-genesis>`_
   - `os-services <https://github.com/open-power-ref-design/os-services>`_
   - `ceph-services <https://github.com/open-power-ref-design/ceph-services>`_
   - `opsmgr <https://github.com/open-power-ref-design/opsmgr>`_
