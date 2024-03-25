Vitex Software debian repository
================================

Configure vitexsoftware repository on Debian or Ubuntu based systems for easy installation of our software.

Requirements
------------

Only Debian/Ubuntu based systems are supported.

Role Variables
--------------

vitex_software_repo_components: [main, games, borrowed]  =  List of components to enable in the repository. Default is all components.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      roles:
         - { role: vitex.vitex_software_repo, vitex_software_repo_components: [main, games] }

License
-------

MIT

Author Information
------------------

Part of Vitex Software solutions https://www.vitexsoftware.cz/

![The Last Student](thelaststudent.png?raw=true)
