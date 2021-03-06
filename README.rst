ecsctl
======

kubectl-style command line tool for AWS EC2 Container Service (ECS)

.. image:: https://img.shields.io/pypi/v/ecsctl.svg
    :target: https://pypi.python.org/pypi/ecsctl

.. image:: https://img.shields.io/travis/cxmcc/ecsctl.svg
    :target: https://travis-ci.org/cxmcc/ecsctl
    :alt: travis-ci

Installation
------------

Download latest release ecsctl-<VERSION>.tar.gz from https://github.com/verbit-ai/ecsctl/releases

.. code:: bash

    pip install ecsctl-<VERSION>.tar.gz --user --trusted-host=pypi.python.org --trusted-host=pypi.org --trusted-host=files.pythonhosted.org

Please make sure you have the pip packages directory (usually "$HOME/Library/Python/2.7/bin/") in your "$PATH".

ecscli
------
Gives you some shortcuts for running common ecsctl commands for current project (based on the folder name)!

.. code:: bash

    ecscli qa ps


.. code:: bash

    ecscli qa2 ssh verbit-qa-services/306ba8395ab742bfaa6064502f89e965


.. code:: bash

    ecscli prod stop verbit-qa-services/306ba8395ab742bfaa6064502f89e965


.. code:: bash

    ecscli qa run rake db:migrate


Purpose
-------

A convenient command line tool to view ECS cluster status and do
troubleshooting.

This tool is trying to provide similar functionality of ``kubectl`` for
kubernetes.

Of course, ECS and kubernetes are so different. Many features on
kubernetes are not possible here in ECS.

Some AWS official projects to check out:

-  `ecs-cli <http://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI_installation.html>`__
-  `aws-cli ecs
   command <http://docs.aws.amazon.com/cli/latest/reference/ecs/>`__

Usage
-----
User
^^^^^^^

Set `ECS_USER` var to be the same as your ssh user name.

.. code:: bash
    export ECS_USER=<VPN_USERNAME>


Cluster
^^^^^^^

List nodes:

::

    % ecsctl get clusters
    NAME     STATUS      RUNNING    PENDING    INSTANCE COUNT
    default  ACTIVE            3          0                 2

Get cluster details:

::

    % ecsctl describe cluster mycluster

Create/delete cluster:

::

    % ecsctl create cluster mycluster

Container Instances (nodes)
^^^^^^^^^^^^^^^^^^^^^^^^^^^

List nodes:

::

    % ecsctl get nodes --cluster mycluster
    INSTANCE ID                           EC2 INSTANCE ID      STATUS      RUNNING COUNT
    00000000-1111-2222-3333-444444444444  i-abcdef123456abcde  ACTIVE                  1

Get node detail:

::

    % ecsctl describe node 00000000-1111-2222-3333-444444444444

Drain/undrain node:

::

    % ecsctl drain 00000000-1111-2222-3333-444444444444

Services
^^^^^^^^

List services:

::

    % ecsctl get services

List services in certain order:

::

    % ecsctl get services --sort-by "createdAt"

Delete a service:

::

    % ecsctl delete service badservice

Delete a service (even if it has desiredCount > 0):

::

    % ecsctl delete service badservice --force

Run container quick start
^^^^^^^^^^^^^^^^^^^^^^^^^

::

    % ecsctl run mycontainer --image busybox
    mycontainer

    % ecsctl get services
    NAME             TASK DEFINITION      DESIRED    RUNNING  STATUS    AGE
    mycontainer-svc  mycontainer:1              1          0  ACTIVE    10 seconds ago

Run commands inside tasks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

::

    % ecsctl get tasks --cluster=verbit-qa-services
    TASK ID                                                                                        STATUS    TASK DEFINITION                             AGE
    arn:aws:ecs:eu-west-1:132948732473:task/verbit-qa-services/09beb4b9653a4a00987fede63eafbda1  RUNNING   verbatizer-qa2:25                          2 hours ago


    % ecsctl ssh  arn:aws:ecs:eu-west-1:132948732473:task/verbit-qa-services/09beb4b9653a4a00987fede63eafbda1 --cluster=verbit-qa-services date
    Warning: Permanently added '172.16.2.211' (ECDSA) to the list of known hosts.
    Thu Apr 25 12:39:01 UTC 2019
    Connection to 172.16.2.211 closed.

    % ecsctl ssh  arn:aws:ecs:eu-west-1:132948732473:task/verbit-qa-services/09beb4b9653a4a00987fede63eafbda1 --cluster=verbit-qa-services bash
    Warning: Permanently added '172.16.2.211' (ECDSA) to the list of known hosts.
    root@a3f1743c0113:/app#


Configs
^^^^^^^

Set default cluster name (equivalent to ``--cluster`` option)

::

    % ecsctl config set cluster mycluster

Set default docker daemon port

::

    % ecsctl config set docker_port 2375
