stackhpc.os-keystone-pam
========================

[![Build Status](https://www.travis-ci.org/stackhpc/ansible-role-os-keystone-pam.svg?branch=master)](https://www.travis-ci.org/stackhpc/ansible-role-os-keystone-pam)

Add OpenStack Keystone pam integration.

Requirements
------------

This role only requires Ansible.

Role Variables
--------------

* ``os_keystone_pam_os_config_name`` which cloud in
  ``/etc/openstack/clouds.yaml`` should be used to validate user's password
  (only needs the auth url).

Dependencies
------------

This role depends on openstack configuration being placed at
`/etc/openstack/clouds.yaml`
One way to do that is using the role ``stackhpc.os-config``.

This role also depends on the users already being created. While ansible makes
that easy, but this should help: ``stackhpc.os-keypair-login``

Example Playbook
----------------

This example pulls a few things together so you push the OpenStack config,
give users ssh key logins, fallback to keystone password:

    ---
    - hosts: all
      vars:
        my_cloud_config: |
          ---
          clouds:
            mycloud:
              auth:
                auth_url: http://openstack.example.com:5000
                project_name: p3
                username: user
                password: secretpassword
              region: RegionOne
        allowed_users:
          - name: "John Smith"
            user: johnsmith
            uid: 2001
            groups: wheel
          - name: "James Bond"
            user: jbond7
            uid: 2007
      roles:
        - { role: stackhpc.os-config,
            os_config_content: "{{ my_cloud_config }}" }
        - { role: stackhpc.os-keypair-login,
            os_keypair_login_cloud: "mycloud",
            os_keypair_login_project_name: "p3",
            os_keypair_login_users: "{{ allowed_users }}" }
        - { role: stackhpc.os-keystone-pam,
            os_keystone_pam_os_config_name: "mycloud" }

An easy way to this example is:

    sudo yum install python-virtualenv libselinux-python

    virtualenv .venv --system-site-packages
    . .venv/bin/activate
    pip install -U pip
    pip install -U ansible

    ansible-galaxy install stackhpc.os-keystone-pam \
                           stackhpc.os-keypair-login \
                           stackhpc.os-config

    ansible-playbook -i "localhost," -c local test.yml

License
-------

Apache 2

Author Information
------------------

http://www.stackhpc.com
