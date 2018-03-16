Creating a paid cluster
=======================

Enable billing on your account
------------------------------

Make sure you have billing enabled on your account.

Log in to `Hasura Dashboard <https://dashboard.hasura.io/>`_, go to the billing
page, and add payment details to enable billing on your account.


Creating a cluster
------------------

Choose cluster configuration
^^^^^^^^^^^^^^^^^^^^^^^^^^^^
You can generate your cluster's configuration using the `Pricing Calculator
<https://hasura.io/pricing>`_.


Create the cluster
^^^^^^^^^^^^^^^^^^
After the above step, follow the ``hasura`` CLI commands to create a cluster of
that configuration.

It is usually of the form:

.. code-block:: bash

   $ hasura cluster create --infra ZXPBVF

Following the above will create a cluster and add its configuration to the
``clusters.yaml`` file, with a default the cluster alias ``hasura``.

To add your own alias, run:

.. code-block:: bash

   $ hasura cluster create --infra ZXPBVF --cluster prod # or -c in short

``prod`` will be set as the alias for the created cluster.


Add the cluster to a Hasura project
-----------------------------------
To add this cluster to a Hasura project, ``cd`` into the Hasura project
directory, and run:

.. code-block:: bash

   $ hasura cluster add <cluster-name> -c <cluster-alias>
   $ hasura setup -c <cluster-alias>
   $ git push <cluster-alias> master

where ``<cluster-name>`` is the newly created name of your cluster, and
``<cluster-alias>`` is the cluster alias.


Example
-------
Let's assume the infra code generated from the Pricing Calculator is ``ZXPBVF``,
and assume we want alias of this cluster to be ``prod``.

Then the flow to create a cluster and adding it to a project is:

.. code-block:: bash

   $ hasura cluster create --infra ZXPBVF -c prod
   # say cluster name is: ambitious93
   # cd into the project directory
   $ hasura cluster add ambitious93
   $ hasura setup -c prod
   # git push the project to this cluster
   $ git push prod master


Advanced
--------
You can also write declarative configuration of your cluster. The file
``clusters.yaml`` (at the top-level of a Hasura project directory) can contain
cluster configuration. :doc:`Learn more <./reference-clusters-yaml>`.

Once you have added your cluster configuration in ``clusters.yaml``, run the
following command to create a cluster.

.. code-block:: bash

   $ hasura cluster create --cluster <cluster-alias>


where ``<cluster-alias>`` is the cluster alias in the ``clusters.yaml``.

After this, you should add the cluster to the project.
