.. .. meta::
   :description: Introduction to Hasura's paid clusters/plans
   :keywords: hasura, cluster, paid plans, paid

Paid Clusters
=============
..
   - What is a paid cluster?
   - Outline - creating and modifying a cluster
   - 
   - Provisioning

A paid cluster is a Hasura cluster running on dedicated infrastructure managed
by Hasura.

Currently only Digital Ocean is supported as a cloud provider. Other
cloud providers are coming soon!

The configuration of the cluster is declarative, and hence can be
version controlled in a Hasura project.

.. note::

  Details of offered paid plans can be found here - `Hasura pricing <https://hasura.io/pricing>`_.


.. toctree::
  :maxdepth: 1
  :titlesonly:

  create-paid-cluster
  cluster-config
  cluster-maintenance
  sample-cluster-configs
  manage-billing
  reference-clusters-yaml
