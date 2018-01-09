Permission Webhooks
-------------------
  **Configuring Webhooks**

  You need to configure the following options in filestore.yaml in cluster/<clusterName> directory:

  .. code-block:: yaml

        hookUrl: "String"

The following default urls are available on filestore microservice for configuring webhooks:

- Private:
    Only logged in users can read and upload
      URL: http://localhost:8080/v2/hooks/user-read-write
- Public:
    Anybody can read, but only logged in users can upload
      URL: http://localhost:8080/v2/hooks/public-read-user-write
- Read Only:
    Anybody can read, but no one can upload
      URL: http://localhost:8080/v2/hooks/public-read
- Custom Permission URL:
    For any other custom permissions, you need to define your own microservice. Refer to :ref:`Authorization webhooks <filestore-authz-webhooks>` to see how this can be done.
