.. .. meta::
   :description: Manual for accessing postgres directly
   :keywords: hasura, docs, postgres, tunnel

Accessing Postgres directly
===========================

The postgres database runs as a microservice on a Hasura cluster.

Getting direct CLI access via port forwarding:
----------------------------------------------

To get direct CLI access to the database, you can port forward from the postgres microservice to your local system.

Run the following hasura CLI command from your project directory:

.. code-block:: bash

   # Open a tunnel to postgres
   hasura microservice port-forward postgres -n hasura --local-port 6432

The above command will allow direct access to postgres at ``localhost:6432``.

Now, you can run ``psql`` (or any other postgres client) from your localhost to access the ``hasuradb`` database:

.. code-block:: bash

   psql -U admin -h localhost -p 6432 -d hasuradb

Getting direct CLI access via exec / SSH:
-----------------------------------------

To get direct CLI access to the database, you can :doc:`exec <../microservices/exec-container>` (equivalent to SSH) into the postgres microservice container.

Run the following hasura CLI command from your project directory:

.. code-block:: bash

   $ hasura ms exec postgres -n hasura -ti -- /bin/bash
   root@postgres-3391217220-6jbq7:/$ # You can now run psql, pg_dump and other commands

Note: This method will only work with Hasura paid plans.

Accessing database directly from a microservice:
------------------------------------------------

See: :doc:`../microservices/connect-postgres`

.. ..todo::
   * Describe postgres, data API, and API gateway architecture
