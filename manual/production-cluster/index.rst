.. .. meta::
   :description: Introduction to Hasura's production clusters/plans
   :keywords: hasura, cluster, production plans, prod, prod plans

Production Cluster
==================
..
   - What is a prod cluster?
   - Outline - creating and modifying a cluster
   - 
   - Provisioning

A production cluster is a Hasura cluster running on the production tier i.e. dedicated infrastructure managed
by Hasura.

Currently only Digital Ocean is supported as a cloud provider. Other
cloud providers are coming soon!

The configuration of the cluster is declarative, and hence can be
version controlled in a Hasura project.

.. note::

  Details of offered production plans can be found here - `Hasura pricing <https://hasura.io/pricing>`_.


.. toctree::
  :maxdepth: 1
  :titlesonly:

  create-production-cluster
  cluster-config
  cluster-maintenance
  sample-cluster-configs
  manage-billing
  reference-clusters-yaml
