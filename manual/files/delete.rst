Deleting a file
---------------
The unique file_id is used to delete an uploaded file.

.. http:delete:: /v1/file/(file_id)
   :noindex:

   Delete a file with a given `file_id`.

   **Example request**:

   .. sourcecode:: http

      GET https://filestore.project-name.hasura-app.io/v1/file/05c40f1e-cdaf-4e29-8976-38c899 HTTP/1.1
      Authorization: Bearer <token>

   **Example response**:

   .. sourcecode:: http

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
        "file_id": "05c40f1e-cdaf-4e29-8976-38c899",
        "content_type": "image/png",
        "file_status": "uploaded",
        "file_size": 351667,
        "user_id": 42,
        "user_role": "user",
        "created_at": "2017-04-25T08:26:22.834266+00:00"
      }

.. note::
    If the permissions webhook is set to ``Public`` on the console. The files will be available to the public, i.e. anyone on the internet with the link can download the file.

``Authorization`` header is not required from a web browser, since ``Cookie`` will be sent.

For more details, see :ref:`API reference <filestore-api-delete>`.

