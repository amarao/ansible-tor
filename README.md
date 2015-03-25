Role Name
=========

Playbook to install and configure tor server.

Requirements
------------

None

Role Variables
--------------

There is no mandatory variables. 
Most variables names are composed of tor_Varname, where Varname is original tor configuration option (f.e. tor_SocksPort -> SocksPort in torrc file). Few exceptions are exist:
f.e.tor_HiddenService is dict with Dir and list of Port (maps to HiddenServiceDir and HiddenServicePort), and many 'list-like' variables are stored as lists (f.e. tor_ExitPolicy, tor_SocksPolicy, etc).

See all variables in Exmaple Playbook chapter.

Some variables are 'trigger' for configuration:
if tor_ControlPort is not defined, tor_HashedControlPassword and tor_CookieAuthentication are ignored.
if tor_ORPort is not defined, all options for relay are igonored (Address, OutboundBindAddress, RelayBandwidthRate, etc).

Dependencies
------------

None. This playbook relies on distro-provided version of tor.

Example Playbook
----------------

There is five example playbooks: just anonymous proxy, relay node (no exit), hidden site, exit node, and all-options version.

## Just anonymous proxy on localhost
    ---
    - hosts: tor
      vars:
        sudo: yes
        tor_SocksPort: 127.0.0.1:9050
        tor_SocksPolicy:
          - accept 127.0.0.0/8
          - reject *
      roles:
        - tor

or just (will use all-default options):

    ---
    - hosts: tor
      vars:
        sudo: yes
      roles:
        - tor


## relay node

    ---
    - hosts: tor
      vars:
        sudo: yes
        tor_ORPort: 9001
        tor_Address: fill.me.example
        tor_RelayBandwidthRate: '100 KB'
        tor_RelayBandwidthBurst: '320 KB'
        tor_ContactInfo: noreply@test.example
        tor_Nickname: CopyPasteFromDocs
       roles:
        - tor

## hidden site 
(you need to configure 'site' part with different playbook)

    ---
    - hosts: tor 
      vars:
        sudo: yes

        tor_HiddenService:
          - Dir: /var/www/hidden_site/
            Port:
              - tor_port: 80
                local_address: 127.0.0.1
                local_port: 80
       roles:
        - tor


## exit node 
(be sure to really understand consequences: https://www.torproject.org/docs/faq-abuse.html.en)

    ---
    - hosts: tor 
      vars:
        sudo: yes
        tor_ExitPolicy:
          - reject *:25
          - accept *:*
    

License
-------

BSD

Author Information
------------------

(c) George Shuklin
