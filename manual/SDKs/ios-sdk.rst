.. Hasura Platform documentation master file, created by
   sphinx-quickstart on Thu Jun 30 19:38:30 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.


.. .. meta::
   :description: Reference documentation for the IOS SDK used for integrating frontend code with backend APIs (both Hasura micro-services and custom microservices).
   :keywords: hasura, docs, IOS SDK, integration


iOS SDK
=======

This SDK helps you integrate your iOS application with Hasura. It makes data queries and user management exceptionally easy.

Installation
------------

Step 1 : Download the Hasura iOS SDK
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

`CocoaPods <http://cocoapods.org>`__ is a dependency manager for iOS projects. You can install it with the following command:

.. code:: bash

    $ gem install cocoapods


.. tip:: CocoaPods 1.1.0+ is required to build the Hasura sdk.

To integrate the Hasura sdk into your Xcode project using CocoaPods, specify it in your ``Podfile``:

.. code :: ruby

    source 'https://github.com/CocoaPods/Specs.git'
    platform :ios, '10.0'
    use_frameworks!

    target '<Your Target Name>' do
        pod 'Hasura', '~> 0.0.2'
    end

Then, run the following command:

.. code :: bash

    $ pod install

Step 2 : Setup Hasura
~~~~~~~~~~~~~~~~~~~~~

``import Hasura`` wherever you are using the SDK.

Project Config
^^^^^^^^^^^^^^

You set the project name and other hasura-project related things in Project Config object.

.. code-block:: swift

    //Minimum Config
    let config = ProjectConfig(projectName: "projectName")

Other init params are :

-  ``customBaseDomain: String`` - If you have a base domain other than
   .hasura-app.io
-  ``isEnabledOverHttp: Bool`` - Set this to true if you want to use Http
   instead of Https
-  ``defaultRole: String`` - "user" role is used by default

Use the above project config to initialise Hasura.

.. code:: swift

    Hasura.initialise(config: config, enableLogs: true)

**Note**: The above method can throw a ``HasuraInitError`` incase the project config provided is incorrect.

.. tip:: Initialisation **must** be done before you use the SDK. The best place to initialise Hasura would be in your ``AppDelegate`` class.

Hasura Client
-------------

The ``HasuraClient`` is the most functional feature of the SDK. It is built using the project config specified on initialisation. You can get an instance of the client from Hasura, like so :

.. code:: swift

    var client = Hasura.getClient();

**Note**: The above method can throw a ``HasuraInitError``.

Authentication
~~~~~~~~~~~~~~

``HasuraClient`` provides a ``HasuraUser`` for all of your
authentication needs like login and signup. This ensures that certain data
can only be accessed by authorized users.

You can get an instance of the ``HasuraUser`` from the ``HasuraClient``
like so :

.. code:: swift

    var user = client.currentUser;

Hasura provides different ways to authenticate a user. Take a look at the  :doc:`docs <../users/index>` to get a better understanding of the various ways you can authenticate a user.

Set the username and password.

.. code-block:: swift

   user.username = "username"
   user.password = "password"

.. tip:: Username is a mandatory field for the user, unless you are using social login(see below).

Optional parameters are ``email`` and  ``mobile``. You can also setup
*verification* of user's Email and Mobile. Once you enable email or mobile
verification, those parameters also become mandatory.

.. code-block:: swift

   user.email = "xyz@abc.com"
   user.mobile = "8888888888"

SignUp
^^^^^^

.. code:: swift

    user.username = "username"
    user.password = "password"
    user.signUp { (isSuccessful: Bool, isPendingVerification: Bool, error: HasuraError?) in
        if isSuccessful {
            if isPendingVerification {
              //The user is registered on Hasura
              //But either his mobile or email needs to be verified.
            } else {
              //Now Hasura.getClient().currentUser will have this user
            }
        } else {
            //Handle Error
        }
    }

Login
^^^^^

.. code:: swift

    user.username = "username"
    user.password = "password"

    user.login { (successful: Bool, error: HasuraError?) in
        if successful {
          //Now Hasura.getClient().currentUser will have this user
        } else {
            //handle error
        }
    }

Email-Verification Pending
^^^^^^^^^^^^^^^^^^^^^^^^^^

In case you have enabled email verification and want to resend the verification email.

.. code-block:: swift

   user.resendVerificationEmail { (successful, error) in
         if successful {
             //Email re-sent successfully
         } else {
             //Handle Error
         }
   }

Mobile-Verification Pending
---------------------------

If you have enabled mobile verification, performing a signup on a user will send an otp to the provided mobile number.
To verify the mobile number, use the following:

.. code-block:: swift

   user.confirmMobile(otp: "") { (confirmationSuccessful, error) in
         if confirmationSuccessful {
             //User's mobile has been verified/confirmed
             //perform a user.login here
         } else {
             //Handle error
         }
   }

``user.confirmMobile`` only confirms the user's mobile number but does not log him in. To confirm and login the user:

.. code-block:: swift

   user.confirmMobileAndLogin(otp: "") { (isLoginSuccessful, error) in
        if isLoginSuccessful {
            //Now Hasura.getClient().currentUser will have this user
        } else {
            //Handle Error
        }
   }

To ``re-send OTP`` to mobile

.. code-block:: swift

   user.resendOTPForLogin { (successful, error) in
      if (successful) {
          //OTP re-sent to mobile
      } else {
          //Handle error
      }
   }

Mobile - OTP
------------

Set the username and mobile number on the user object.

.. code-block:: swift

  user.username = "username"
  user.mobile = "8888888888"

SignUp
^^^^^^

.. code-block:: swift

   user.otpSignUp { (isSuccessful: Bool, isPendingVerification: Bool, error: HasuraError?) in
       if isSuccessful {
         //Now Hasura.getClient().currentUser will have this user
       } else {
           //Handle Error
       }
   }

.. tip:: Calling this method will send an ``otp`` to the provided mobile number. Once you receive the OTP, call the ``user.otpLogin`` method to login.

Login
^^^^^

.. code-block:: swift

   user.otpLogin(otp: otp) { (successful: Bool, error: HasuraError?) in
      if successful {
          //Now Hasura.getClient().currentUser will have this user
      } else {
          //handle error
      }
   }


Social Login
------------

Hasura also providers authentication using various oauth login providers.

Facebook
^^^^^^^^

* **Step1**: Integrate facebook login with your Hasura Project, check out the :doc:`docs <../users/facebook>`.

* **Step2**: Intergate facebook login in your iOS app. Check out the facebook `docs <https://developers.facebook.com/docs/facebook-login/ios/>`_ to do this.

* **Step3**: Perform facebook login in the app and receive the ``access token``.

* **Step4**: Finally, pass this ``access token`` to the user object like so:

.. code-block:: swift

   user.socialLogin(loginType: .facebook, token: accessToken) { (isSuccessful, error) in
      if isSuccessful {
         //Login Successful
      } else {
         //Handle Error
      }
   }

Google
^^^^^^

* **Step1**: Integrate google login with your Hasura Project, check out the :doc:`docs <../users/google>`.

* **Step2**: Integrate google login in your iOS app. Check out the `docs <https://developers.google.com/identity/sign-in/ios/start-integrating>`_ to do this.

* **Step3**: Perform google login in the app and receive the ``access token``.

* **Step4**: Finally, pass this ``access token`` to the user object like so:

.. code-block:: swift

   user.socialLogin(loginType: .google, token: accessToken) { (isSuccessful, error) in
      if isSuccessful {
         //Login Successful
      } else {
         //Handle Error
      }
   }


LoggedIn User
^^^^^^^^^^^^^

Each time a ``HasuraUser`` is signed up or logged in, the session is
cached by the ``HasuraClient``. Hence, you do not need to log the user
in each time your app starts.

.. code:: swift

    if user.isLoggedIn {
        //User is logged in
    } else {
      //User is not logged in
    }

Log Out
^^^^^^^

To log the user out, simple call ``.logout`` method on the user object.

.. code:: swift

    user.logout { (successful: Bool, error: HasuraError?) in
        if successful {

        } else {

        }
    }

Data Service
~~~~~~~~~~~~

Hasura provides out of the box data APIs on the tables and views you
make in your project. To learn more about how they work, check out the :doc:`docs <../users>`

.. code:: swift

    client.useDataService(params: [String: Any])
        .responseArray { (response: [MyResponse]?, error: HasuraError?) in
            if let response = response {
                //Handle response
            } else {
                //Handle error
            }
    }

``MyResponse`` is just a swift class/struct which is a representation of the response you are
expecting. Hasura uses `ObjectMapper <https://github.com/Hearst-DD/ObjectMapper>`__ internally
to map the json response into your class/struct.

**Note**: In case you are expecting an object response, use
``.responseObject``.

*All SELECT queries to the data microservice will return
an array response.*

    If the HasuraUser in the HasuraClient is logged-in/signed-up, then every call
    made by the HasuraClient will be authenticated by default with "user" as the
    default role (This default role can be changed when building the project
    config)

In case you want to make the above call for an ``anonymous`` role,

.. code:: swift

    client.useDataService(role: "anonymous", params: [String, Any])
        .responseArray { (response: [MyResponse]?, error: HasuraError?) in
            if let response = response {
                //Handle response
            } else {
                //Handle error
            }
    }

In case you want to make the above call for a ``custom`` role,

.. code:: swift

    client.useDataService(role: "customRole", params: [String, Any])
        .responseArray { (response: [MyResponse]?, error: HasuraError?) in
            if let response = response {
                //Handle response
            } else {
                //Handle error
            }
    }

**Note**: This role will be sent **just** for this query and **will
not** become the default role.

Query Template Service
~~~~~~~~~~~~~~~~~~~~~~

The syntax for the query template microservice remains the same as
``Data Service`` except for setting the name of the query template being
used.

.. code:: swift

    client.useQueryTemplateService(templateName: "templateName", params: [String, Any])
        .responseArray { (response: [MyResponse]?, error: HasuraError?) in
            if let response = response {
                //Handle response
            } else {
                //Handle error
            }
    }

Filestore Service
~~~~~~~~~~~~~~~~~

Hasura provides a filestore microservice, which can be used to upload and
download files. To use the Filestore microservice properly, kindly take a
look at the docs
`here <https://docs.hasura.io/0.13/ref/hasura-microservices/filestore/index.html>`__.

Upload File
^^^^^^^^^^^

The upload file method accepts the following:

-  ``file: Data`` which is the data to be uploaded.
-  ``mimetype: String`` which is the ``mimetype`` of the file.

.. code:: swift

    client.useFileservice()
        .uploadFile(file: data, mimeType: "image/*")
        .response(callbackHandler: { (response: FileUploadResponse?, error: HasuraError?) in
            if response != nil {
                print("Successfully uploaded image")
            } else {
                //Handle error
            }
        })

``FileUploadResponse`` in the above response contains the following:

-  ``id: String?`` is the unique Id generated for the file that was uploaded. To download the file you will be using this id.
-  ``userId: Int?`` is the id of the user who uploaded the file.
-  ``createdAt: Date?`` is the time string for when this file was uploaded/created.


Download File
^^^^^^^^^^^^^

.. code:: swift

    client.useFileservice()
        .downloadFile(fileId: "4F2D59B7-7BD0-400A-9C31-F5A43F29560F")
        .response { (downloadedData, progress, error) in
            guard progress == 100 || progress == -1 else {
                print("Download progress: \(progress)")
                return
            }
            if let file = downloadedData {
                self.imageView.image = UIImage(data: file)
            } else {
                self.handleError(error: error)
            }
    }

Issues
------

In case of bugs, please raise an issue
`here <https://github.com/hasura/support>`__
