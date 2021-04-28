# MediaStreaming - FileTransfer 

Repository for the FileTransfer of Media Streaming Module (T3.3).


## Short Intro to the funcionality

Nowadays, on internet, users are sharing more and more multimedia contents such as photos, videos and files every day.

However, connectivity networks remain fragile. Platform APIs are also often a mess and every project builds its own file uploader.

This funcionality is mainly based on TUS (Open and Open Source Protocol for Resumable File Uploads: https://tus.io/).

Resumable means people on internet can carry on where they left off without re-uploading whole data again in case of any interruptions. An interruption may happen willingly if the user wants to pause, or by accident in case of a network issue or server outage.

The main goal of it is that it will indeed solve the problem of unreliable file uploads once and for all.

As mentioned before, TUS is a quite recent open protocol (additional information could be found here: https://tus.io/protocols/resumable-upload.html) for resumable uploads built on HTTP.

It offers simple, cheap and reusable stacks for clients and servers. It supports any language, any platform and any network.

### Basic Tus Architecture

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-MediaStreaming-FileTransfer/master/files/TUS.png" alt="Basic Tus Architecture">

### File Transfer:

Thanks to the TUS server, a user can upload content from the mobile phone to the personal storage. The upload can be paused and resumed if needed. The default configuration is:

* TURN server url: `https://builder.helios-social.eu/files/`

All these values can be modified in the `values/strings.xml` file.


### How to use File Transfer:

This module generates a .aar file to be included in your applications as a dependency. See more details at Multiproject dependencies chapter.

To call the extension from your application, include the Intent in your activity

```java
    Intent fileTransferIntent = new Intent(MainActivity.this, FileTransferActivity.class);
    MainActivity.this.startActivity(fileTransferIntent);
```

When the activity finish, return the uploaded file URL to the mainly activity that called to file transfer. An usage example:

```java
    private static final int FILE_TRANSFER_ACTIVITY_REQUEST_CODE = 1;

...

    Intent fileTransferIntent = new Intent(MainActivity.this, FileTransferActivity.class);
    MainActivity.this.startActivityForResult(fileTransferIntent, FILE_TRANSFER_ACTIVITY_REQUEST_CODE);

...

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);

        if (requestCode == FILE_TRANSFER_ACTIVITY_REQUEST_CODE) {
            if(resultCode == Activity.RESULT_OK){
===>            data.getStringExtra("uploadURL");
            } else if (resultCode == Activity.RESULT_CANCELED) {}
        }
    }
```

### How to Develop

- Install Git in your computer: https://github.com/git-guides/install-git

- Choose a directory from your computer and download with Git the code using the link provided in this page:

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-MediaStreaming-FileTransfer/master/files/github.PNG" alt="FileTransfer github">

`git clone https://github.com/helios-h2020/h.extension-MediaStreaming-FileTransfer.git`

- Open Android Studio and open an existing project from the directory of your code downloaded. To install Android Studio follow the next link: https://developer.android.com/studio/install)

- To generate an aar file from the code, select the Build option in the Menu Bar, select ReBuild project or choose Make Project icon as you can see in the picture. Once generated, you can find the file in app/build/options/aar (you can rename the file as you like):

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-MediaStreaming-FileTransfer/master/files/build.PNG" alt="FileTransfer build">

### Request permissions

There are non additional permissions needed.


## Multiproject dependencies

HELIOS software components are organized into different repositories
so that these components can be developed separately avoiding many
conflicts in code integration. However, the modules also depend on
each other.


### How to configure the dependencies

To manage project dependencies developed by the consortium, the approach proposed is to use a private Maven repository with Nexus.

To avoid cloning all dependencies projects in local, compile the "father" project. Otherwise, a developer should have all the projects locally to
be able to compile. Using Nexus, the dependencies are located in a remote repository, available to compile, as described in the next section.
Also to improve the automation for deploy, versioning and distribution of the project.


### How to use the HELIOS Nexus

Similar to other dependencies available in Maven Central, Google or others repositories. In this case we specify the Nexus
repository provided by Atos: `https://builder.helios-social.eu/repository/helios-repository/`

This URL makes the project dependencies available.

To access, we simply need credentials, that we will define locally in the variables `heliosUser` and `heliosPassword`.

The `build.gradle` of the project define the Nexus repository and the credential variables in this way:

```gradle
repositories {
        ...
        maven {
            url "https://builder.helios-social.eu/repository/helios-repository/"
            credentials {
                username = heliosUser
                password = heliosPassword
            }
        }
    }
```

And the variables of Nexus's credentials are stored locally at `~/.gradle/gradle.properties`:

```properties
heliosUser=username
heliosPassword=password
```

To request Nexus username and password, contact with: `jordi.hernandezv@atos.net`


### How to deploy a new version of the dependencies

Let's say that we want to deploy a new version of the videocall project. This project is a dependency of MediaStreaming.
For Continuous Integration we use Jenkins. It deploys the configured projects (e.g., videocall) in different jobs,
and the results are libraries packaged like AAR files (*Android ARchive*). These packaged libraries are upload to Nexus and in this way,
so they are available to build the projects that depend on them (e.g., MediaStreaming).
In the videocall example, Jenkins jobs generate automatically an aar library and store it at the Nexus repository to make it available.

Jenkins is the tool deployed by Atos (WP6 leader) in HELIOS to automate the generation of APKs, joining all the project modules.
Due to the need of managing the dependencies, Atos has selected additional tools, as explained in this document.

After pushing a change to the `master` branch, the maintainer can build the module by means of the job in the Jenkins interface. GitLab repositories are set to protect
the `master` branch push and merge for the partner in charge of its module/project (maintainer).

To request Jenkins username and password, contact with: `jordi.hernandezv@atos.net`


### How to use the dependencies

To use the dependency in `build.gradle` of the "father" project, you should specify the last version available in Nexus, related to the last Jenkins's deploy.
For example, to declare the dependency on the filetransfer module and the respective version:

`implementation 'eu.h2020.helios_social.modules.filetransfer:filetransfer:1.0.10'`

For more info review: `https://scm.atosresearch.eu/ari/helios_group/generic-issues/blob/master/multiprojectDependencies.md`

### How to use the dependencies locally ###

If you want to include the .aar file generated as a dependency in the application whitout use Nexus dependencies:

- Go to your application code and create libs folder inside app folder:

<img src="https://raw.githubusercontent.com/helios-h2020/h.app-MediaStreaming/master/doc/libs.PNG" alt="libs folder">

- Open build.gradle at Project level and add flatDir{dirs 'libs'} :

<img src="https://raw.githubusercontent.com/helios-h2020/h.app-MediaStreaming/master/doc/libs_gradle.PNG" alt="Project build.gradle">

```
allprojects {
   repositories {
      jcenter()
      flatDir {
        dirs 'libs'
      }
   }
}
```

- Open build.gradle at app level and add .aar file:

<img src="https://raw.githubusercontent.com/helios-h2020/h.app-MediaStreaming/master/doc/gradle_app.PNG" alt="app build.gradle">

```
dependencies {
     compile(name:'file_name', ext:'aar')
}
```

### FileTransfer mudule Storage

The module implements a local storage system to consume the TUS server.

See more info at: https://github.com/helios-h2020/h.core-PersonalStorageElements