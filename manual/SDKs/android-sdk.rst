.. Hasura Platform documentation master file, created by
   sphinx-quickstart on Thu Jun 30 19:38:30 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. .. meta::
   :description: Reference documentation for the Android SDK used for integrating frontend code with backend APIs (both Hasura micro-services and custom microservices).
   :keywords: hasura, docs, Android SDK, integration, Android, SDK, Mobile SDK, Mobile app, Android app

###########
Android SDK
###########

************
Installation
************

**Step 1**: Download the Hasura Android SDK
===========================================

Using Gradle
------------

Add this to your project level build.gradle file.

.. code-block:: groovy

   allprojects {
      repositories {
         ...
         maven { url 'https://jitpack.io' }
      }
   }

Next, add the following to your app level build.gradle

.. code-block:: groovy

   {
      dependencies {
         compile 'com.github.hasura.android-sdk:sdk:v0.0.9'
      }
   }

Using Maven
-----------

Add the Jitpack repository to your build file.

.. code-block:: xml

   <repositories>
     <repository>
       <id>jitpack.io</id>
       <url>https://jitpack.io</url>
     </repository>
   </repositories>


Add the dependency

.. code-block:: xml

   <dependency>
     <groupId>com.github.hasura.android-sdk</groupId>
     <artifactId>sdk</artifactId>
     <version>v0.0.9/version>
   </dependency>

**Step 2**: Setup Hasura
========================

After you have downloaded the Hasura SDK into your app, you have to initialise the SDK.

Project Config
--------------

The first step to initialising the SDK is to setup your project config. You set the project name and other hasura-project related things in Project Config object.

.. code-block:: java

   ProjectConfig config = new ProjectConfig.Builder()
                    .setProjectName("projectName")
                    .build();

Other methods available are :

* ``.setCustomBaseDomain("myCustomDomain.com")``: To set a custom base domain instead of a project name.
* ``.enableOverHttp()``: If included, then every network call is made over http (https is default).
* ``.setDefaultRole("customDefaultRole")``: If not included then "user" role is used by default.

Initialise Hasura
-----------------

Using the above project config, initialise Hasura.

.. code-block:: java

  Hasura.setProjectConfig(config)
    .enableLogs() // not included by default
    .initialise(this);


.. tip:: Initialisation **MUST** be done before you use the SDK. The best place to initialise Hasura would be in your `application` class or in your Launcher Activity.

*************
Hasura Client
*************

The ``HasuraClient`` object is the most functional feature of the SDK. It is built using the project config specified on initialisation. You can get an instance of the client only from Hasura, like so:

.. code-block:: java

  HasuraClient client = Hasura.getClient();

.. tip:: All network calls are called on a non ui thread and all the callbacks are pushed into the ui thread.

Authentication
==============

``HasuraClient`` provides a ``HasuraUser`` object for all of your authentication needs like login and signup. This ensures that certain data can only be accessed by authorized users. You can get an instance of the ``HasuraUser`` from the ``HasuraClient`` like so:

.. code-block:: java

  HasuraUser user = client.getUser();

Hasura provides different ways to authenticate a user. Take a look at the  :doc:`docs <../users/index>` to get a better understanding of the various ways you can authenticate a user.

Set the username and password.

.. code-block:: java

  user.setUsername("username");
  user.setPassword("password");

.. tip:: Username is a mandatory field for the user, unless you are using social login(see below).

Optional parameters are ``email`` and  ``mobile``. You can also setup
*verification* of user's Email and Mobile. Once you enable email or mobile
verification, those parameters also become mandatory.

.. code-block:: java

  user.setEmail("xyz@abc.com");
  user.setMobile("88888888")


SignUp
------

.. code-block:: java

  user.signUp(new SignUpResponseListener() {
              @Override
              public void onSuccessAwaitingVerification(HasuraUser user) {
                //The user is registered on Hasura.
                //But either his mobile or email needs to be verified.
              }

              @Override
              public void onSuccess(HasuraUser user) {
                //Now Hasura.getClient().getCurrentUser() will have this user
              }

              @Override
              public void onFailure(HasuraException e) {
                  //Handle Error
              }
          });

Login
-----

.. code-block:: java

  user.login(new AuthResponseListener() {

              @Override
              public void onSuccess(HasuraUser user) {
                //Now Hasura.getClient().getCurrentUser() will have this user
              }

              @Override
              public void onFailure(HasuraException e) {
                  //Handle Error
              }
          });

Email-Verification Pending
--------------------------

In case you have enabled email verification and want to resend the verification email.

.. code-block:: java

  user.resendVerificationEmail(new EmailVerificationSenderListener() {
              @Override
              public void onSuccess(String message) {
                  //email verification sent successfully
              }

              @Override
              public void onFailure(HasuraException e) {
                  //handle error
              }
          });

Mobile-Verification Pending
---------------------------

If you have enabled mobile verification, performing a signup on a user will send an otp to the provided mobile number.
To verify the mobile number, use the following:

.. code-block:: java

  user.confirmMobile(otp, new MobileConfirmationResponseListener() {
              @Override
              public void onSuccess(String message) {
                  //The user's mobile number has been confirmed.
                  //Perform a user.login() to login the user.
              }

              @Override
              public void onFailure(HasuraException e) {
                  //Handle error
              }
          });

``user.confirmMobile`` only confirms the user's mobile number but does not log him in. To confirm and login the user:

.. code-block:: java

  user.confirmMobileAndLogin(otp, new AuthResponseListener() {
              @Override
              public void onSuccess(String message) {
                //Now Hasura.getClient().getCurrentUser() will have this user
              }

              @Override
              public void onFailure(HasuraException e) {
                //Handle Error
              }
          });

To ``re-send OTP`` to the mobile

.. code-block:: java

  user.resendOTP(new OtpStatusListener() {
        @Override
        public void onSuccess(String message) {
            //OTP re-sent successfully
        }

        @Override
        public void onFailure(HasuraException e) {
            //Handle Error
        }
  });

Mobile - OTP
------------

Set the username and mobile number on the user object.

.. code-block:: java

  user.setUsername("username");
  user.setMobile("8888888888")

SignUp
^^^^^^

.. code-block:: java

  user.otpSignUp(new SignUpResponseListener() {
              @Override
              public void onSuccessAwaitingVerification(HasuraUser user) {}

              @Override
              public void onSuccess(HasuraUser user) {
                //Now Hasura.getClient().getCurrentUser() will have this user
              }

              @Override
              public void onFailure(HasuraException e) {
                  //Handle Error
              }
          });

.. tip:: Calling this method will send an ``otp`` to the provided mobile number. Once you receive the OTP, call the ``user.otpLogin()`` method to login.

Login
^^^^^

.. code-block:: java

  user.otpLogin(otp, new AuthResponseListener() {

              @Override
              public void onSuccess(HasuraUser user) {
                //Now Hasura.getClient().getCurrentUser() will have this user
              }

              @Override
              public void onFailure(HasuraException e) {
                  //Handle Error
              }
          });


Social Login
------------

Hasura also providers authentication using various oauth login providers.

Facebook
^^^^^^^^

* **Step1**: Integrate facebook login with your Hasura Project, check out the :doc:`docs <../users/facebook>`.

* **Step2**: Intergate facebook login in your Android app. Check out the facebook `docs <https://developers.facebook.com/docs/facebook-login/android/>`_ to do this.

* **Step3**: Perform facebook login in the app and receive the ``access token``.

* **Step4**: Finally, pass this ``access token`` to the user object like so:

.. code-block:: java

  user.socialLogin(HasuraSocialLoginType.FACEBOOK, accessToken, new AuthResponseListener() {
              @Override
              public void onSuccess(String message) {

              }

              @Override
              public void onFailure(HasuraException e) {

              }
          });

Google
^^^^^^

* **Step1**: Integrate google login with your Hasura Project, check out the :doc:`docs <../users/google>`.

* **Step2**: Integrate google login in your Android app. Check out the `docs <https://developers.google.com/identity/sign-in/android/start-integrating>`_ to do this.

* **Step3**: Perform google login in the app and receive the ``access token``.

* **Step4**: Finally, pass this ``access token`` to the user object like so:

.. code-block:: java

  user.socialLogin(HasuraSocialLoginType.GOOGLE, accessToken, new AuthResponseListener() {
              @Override
              public void onSuccess(String message) {

              }

              @Override
              public void onFailure(HasuraException e) {

              }
          });


LoggedIn User
-------------

Each time a ``HasuraUser`` is signed up or logged in, the session is cached by the ``HasuraClient``. Hence, you do not need to log the user in each time your app starts.

.. code-block:: java

  HasuraUser user = client.getUser();
  if (user.isLoggedIn()) {
    //This user is logged in
  } else {
    //This user is not logged in
  }


Logout
------

.. code-block:: java

  user.logout(new LogoutResponseListener() {
              @Override
              public void onSuccess(String message) {

              }

              @Override
              public void onFailure(HasuraException e) {

              }
          });


Data Service
============

Hasura provides out of the box data apis on the tables and views you make in your project. To learn more about how they work, check out the :doc:`docs <../users>`.

.. code-block:: java

  client.useDataService()
    .setRequestBody(JsonObject)
    .expectResponseType(MyResponse.class)
    .enqueue(new Callback<MyResponse>, HasuraException>() {
                      @Override
                      public void onSuccess(MyResponse response) {
                        //Handle response
                      }

                      @Override
                      public void onFailure(HasuraException e) {
                          //Handle error
                      }
                  });

.. tip:: In case you are expecting an array response, use ``.expectResponseTypeArrayOf(MyResponse.class)``. *All SELECT queries to the data microservice will return an array response.*

In the above method, there are a few things to be noted :

* ``.setRequestBody()``: This is an overloaded method which accepts either an object of type ``JsonObject`` or a POJO (ensure that the JSON representation of this object is correct). The sdk uses `gson <https://github.com/google/gson>`_ internally to map Java Objects to JSON.

  * For eg, the POJO representation of the following JSON

.. code-block:: JSON

  {
    "type": "select",
    "args": {
      "table": "tableName",
      "columns": ["column1", "column2"]
    }
  }

would look like so:

.. code-block:: java

  import com.google.gson.annotations.SerializedName;
  public class SelectTodoRequest {

    @SerializedName("type")
    String type = "select";

    @SerializedName("args")
    Args args;

    public SelectTodoRequest() { }

    class Args {

      @SerializedName("table")
      String table = "tableName";

      @SerializedName("columns")
      String[] columns = {
              "column1","column2"
      };
    }
  }

* ``.expectResponseType()``: Specify the POJO representation of the expected response.

    If the HasuraUser in the HasuraClient is loggedin/signedup then every call made by the HasuraClient will be authenticated by default with "user" as the default role (This default role can be changed when building the project config).


In case you want to make the above call for an ``anonymous`` role.

.. code-block:: java

  client.asAnonymousRole()
    .useDataService()
    .setRequestBody(JsonObject)
    .expectResponseType(MyResponse.class)
    .enqueue(new Callback<MyResponse>, HasuraException>() {
                      @Override
                      public void onSuccess(MyResponse response) {
                        //Handle response
                      }

                      @Override
                      public void onFailure(HasuraException e) {
                          //Handle error
                      }
                  });


In case you want to make the above call for a ``custom`` role.

.. code-block:: java

  client.asRole("customRole")
    .useDataService()
    .setRequestBody(JsonObject)
    .expectResponseType(MyResponse.class)
    .enqueue(new Callback<MyResponse>, HasuraException>() {
                      @Override
                      public void onSuccess(MyResponse response) {
                        //Handle response
                      }

                      @Override
                      public void onFailure(HasuraException e) {
                          //Handle error
                      }
                  });


.. tip:: This role will be sent JUST for this query and ***will not*** become the default role.

Query Template Service
======================

The syntax for the query template microservice remains the same as ``Data Service`` except for setting the name of the query template being used.

.. code-block:: java

  client.useQueryTemplateService("templateName")
    .setRequestBody(JsonObject)
    .expectResponseType(MyResponse.class)
    .enqueue(new Callback<MyResponse>, HasuraException>() {
                      @Override
                      public void onSuccess(MyResponse response) {
                        //Handle response
                      }

                      @Override
                      public void onFailure(HasuraException e) {
                          //Handle error
                      }
                  });

Filestore Service
=================

Hasura provides a filestore microservice, which can be used to upload and download files. To use the Filestore microservice properly, kindly take a look at the `docs <https://docs.hasura.io/0.13/ref/hasura-microservices/filestore/index.html>`_ .

Upload File
-----------

The upload file method accepts the following:

* either a ``File`` object or a ``byte`` array (byte[]) which is to be uploaded.
* a ``mimetype`` of the file.
* ``FileUploadResponseListener`` which is an interface that handles the response.
* ``FileId`` (optional): Every uploaded file has an unique Id associated with it. You can optionally specify this fileId on the ``uploadFile`` method. In case it is not, the SDK automatically assigns a unique Id for the file.

.. code-block:: java

  client.useFileStoreService()
                  .uploadFile(/*File or byte[]*/, /*mimeType*/, new FileUploadResponseListener() {
                      @Override
                      public void onUploadComplete(FileUploadResponse response) {
                        //Success
                      }

                      @Override
                      public void onUploadFailed(HasuraException e) {
                        //handle error
                      }
                  });


``FileUploadResponse`` object in the above response contains the following:

* ``file_id``: The uniqiue Id of the file that was uploaded.
* ``user_id``: The id of the user who uploaded the file.
* ``created_at`` : The time string for when this file was uploaded/created.

Download File
-------------

.. code-block:: java

  client.useFileStoreService()
           .downloadFile("fileId", new FileDownloadResponseListener() {
                      @Override
                      public void onDownloadComplete(byte[] data) {
                        //successful
                      }

                      @Override
                      public void onDownloadFailed(HasuraException e) {
                        //handle error
                      }

                      @Override
                      public void onDownloading(float completedPercentage) {
                        //download percentage
                      }
                  });


Custom Service
==============

In addition to the ``data``, ``auth`` and ``fileStore`` microservices, you can also deploy your own custom microservice on Hasura. For such cases, you can still utilize the session management of the SDK to make your APIs. Currently, we have support for `Retrofit <http://square.github.io/retrofit/>`_.

Using a custom microservice - Retrofit Support
-----------------------------------------

This is a wrapper over Retrofit for custom microservices, assuming that your interface with the api definitions is called ``MyCustomInterface.java``.

* Let's say you have a custom microservice set up on Hasura called ``api``.
* Your external endpoint for this custom microservice would be -> ``api.<cluster-name>.hasura-app.io``.

Step1: Including the retrofit support
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Using Gradle
.............

Add the following to your app level build.gradle

.. code-block:: groovy

  dependencies {
     compile 'com.github.hasura:android-sdk:custom-service-retrofit:v0.0.9'
  }

Using Maven
...........

Add the dependency

.. code-block:: xml

  <dependency>
    <groupId>com.github.hasura.android-sdk</groupId>
    <artifactId>custom-service-retrofit</artifactId>
    <version>v0.0.9/version>
  </dependency>


Step2: Build your custom microservice
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java

  RetrofitCustomService<MyCustomInterface> cs = new RetrofitCustomService.Builder()
                  .serviceName("api")
                  .build(MyCustomInterface.class);


.. tip:: This needs to be done before Hasura Init.

Step3: Add this custom microservice during init
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java

  Hasura.setProjectConfig(projectConfig)
    .enableLogs()
    .addCustomService(cs)
    .initialise(this);


Step4: Accessing Custom Service
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java

  MyCustomService cs = client.useCustomService(MyCustomInterface.class);


Bonus: Handle the Response
^^^^^^^^^^^^^^^^^^^^^^^^^^

``RetrofitCallbackHandler`` is a helper class which you can use to handle the responses from your custom APIs and parse errors.

********
Examples
********

To take a look at some sample apps built using the Hasura SDK, take a look at our `github repository <https://github.com/hasura/Modules-Android>`_.

******
Issues
******

In case of bugs, please raise an issue `here <https://github.com/hasura/support>`_.
