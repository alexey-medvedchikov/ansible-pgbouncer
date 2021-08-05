pgbouncer
=========

This role configures PgBouncer PostgreSQL connection mux utility.

Postgres server performance degrades when handling a high number of connection due to a 1:1 mapping of connection to Postgres backend processes. PgBouncer is a threaded pooler which can reduce the number of backend processes and the handshaking involved in setting up a new connection.

Example group vars
------------------

Ansible handles the templating of userlist.txt, including the md5 hashing.

    pgbouncer_users:
      - name: username
        pass: unencrypted_password
      - name: postgres
        host: unencrypted_password

    pgbouncer_databases:
      - name: "wibble"
        host: wibble.example.com
        port: 5432

    pgbouncer_max_client_conn: 300
    pgbouncer_max_db_connections: 300
    pgbouncer_max_user_connections: 300

    # govern how many backend connections
    pgbouncer_default_pool_size: 80

Requirements
------------

Module is tested with pgbouncer 1.7.2 from the pgdg repository so that is the recommended source for the binary.

However we don't setup the repo here.

Example playbook
----------------

    - hosts:  "{{ group }}"
      serial: "{{ serial }}"
      become: yes
      vars:
        pgbouncer_users:
          - name: username
            pass: unencrypted_password
          - name: postgres
            host: unencrypted_password

        pgbouncer_databases:
          - name: "wibble"
            host: wibble.example.com
            port: 5432

        pgbouncer_max_client_conn: 300
        pgbouncer_max_db_connections: 300
        pgbouncer_max_user_connections: 300

        # govern how many backend connections
        pgbouncer_default_pool_size: 80

      roles:
        # This deploys psql and sets up the pgdg repo this ensures that a new pgbouncer is installed and psql can be used to administer it
        - ansible-postgresql-client
        - ansible-pgbouncer


Debugging
---------

If pgbouncer fails to start

    sudo -u postgres pgbouncer -d /etc/pgbouncer/pgbouncer.ini -vvvv

Testing a connection to a remote database

    psql -h localhost -p 6432 -U username databasename

Stats
-----

The default 1.7.2 configuration provides peer authentication of the pgbouncer database to show stats.
NOTE: this still requires a userlist.txt entry for the postgres user.

    sudo -u postgres psql -p 6432 pgbouncer -c 'show pool;'

    sudo -u postgres psql -p 6432 pgbouncer -c 'show stats;'


Reloading / restarting
----------------------

Key config changes such as pool sizing do not require a full restart

sudo /etc/init.d/pgbouncer reload


License
-------

LGPL

Author Information
------------------

- Alexey Medvedchikov, 2GIS, LLC
