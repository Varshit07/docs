.. .. meta::
   :description: Creating a Hasura cluster
   :keywords: cluster, create

Creating a cluster
==================

To create a new cluster, use the ``hasura`` CLI.

.. code-block:: bash

  $ hasura cluster create --type free

  INFO Creating a Hasura cluster...
  INFO Hasura cluster created                        cluster=alarming52
  INFO Initializing the cluster...
  INFO Cluster initialized
  INFO Kubernetes context has been added to this system  context=alarming52

.. note::
    You can only create 2 free Hasura clusters.
