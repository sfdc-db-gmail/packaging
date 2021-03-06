# FAQ on Unlocked Packages
##### By Dileep Burki (Last Modified on 10/29/2018)

#### Please note that this is NOT an official documentation; it is written in an informal style with many future looking statements - these future-looking features may not be developed at all or they may be developed in ways very different from what's stated here. For the official product documentation, please click [here](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_dev2gp.htm). Also, despite best attempts, this FAQ may not stay in updated state as features evolve. Refer to the official documentation for up-to-date info.

##### If you have any comments or questions, please post them in the [Trailblazer Community](https://success.salesforce.com/_ui/core/chatter/groups/GroupProfilePage?g=0F93A000000Lg5U) for Unlocked Packages

<h1 id="toc">
Table of Contents
</h1>

[Q. What are Unlocked Packages?](#unlocked-pkgs)

[Q. What are the benefits of packaging over Change Sets and ANT Migration Tool?](#pkg-benefits)

[Q. What is a "package" and what is a "package version"?](#pkg-pkgver)

[Q. What is "unpackaged metadata"?](#unpackaged-md)

[Q. What are the different types of packages and which one should I use?](#pkg-types)

[Q. If I am an ISV, what type of packaging should I use?](#isv-packaging)

[Q. How do I build and deploy a new app using unlocked packages? What’s my Hello World App for unlocked packages?](#hello-world)

[Q. How can I organize my unpackaged metadata using unlocked packages?](#organize-md)

[Q. What are some of the common packaging operations?](#common-pkg-ops)

[Q. What are package aliases and how do they work?](#pkg-alias)

[Q. How can I use Salesforce CLI to perform package-related operations?](#pkg-cli-ops)

[Q. How can I specify attributes in the sfdx-project.json file for managing my packages?](#simple-json)

[Q. What can be packaged in Unlocked Packages? Will unlocked packages support ALL metadata in future?](#supported-md)

[Q. How and where can I install my unlocked packages?](#pkg-install)

[Q. How can I find out about the set of packages installed / deployed in my org?](#installed-pkgs)

[Q. How do I iterate on my package? What happens when I add, edit and delete package metadata?](#pkg-upgrades)

[Q. How do I work with Version Numbers?](#ver-num)

[Q. What are the implications of modifying or deleting metadata that is part of an unlocked package *directly* in the installed org?](#modify-md-installed-org)

[Q. During a package upgrade, how are destructive changes handled?](#delete-deprecate)

[Q. How can I remove metadata from my package?](#remove-md)

[Q. How can I refactor my package?](#pkg-refactor)

[Q. Can I downgrade my package?](#downgrade-pkg)

[Q. How can I secure my packages?](#pkg-security)

[Q. What is the difference between beta package versions and released package versions?](#pkg-rel-state)

[Q. Can my packages depend on one another?](#pkg-deps)

[Q. How can I specify dependencies among packages?](#specify-pkg-dep)

[Q. When are package dependencies enforced?](#pkg-dep-enforcement)

[Q. Can you give me an example of an sfdx-project.json file with many interdependent packages?](#complex-pkg-deps)

[Q. How can I automatically install dependent packages?](#dep-pkg-install)

[Q. Who owns the unlocked packages?](#pkg-owner)

[Q. What is a namespace and should I use one with my unlocked package?](#namespace)

[Q. How can I delete my package and package versions?](#delete-pkg)

[Q. What is the relationship between version control system and packages?](#vcs)

[Q. What are Locked Packages? Can I use them?](#locked-pkgs)

[Q. How can I get more info about Salesforce DX and Unlocked Packages?](#pkg-info)


<h2 id="unlocked-pkgs">
What are Unlocked Packages?
</h2>

As part of Salesforce DX, with the Winter '19 release, **Unlocked Packages** is *Generally Available*. Unlocked Packages is a new type of packaging solution that is being offered as part of Salesforce DX. It provides a package-based solution for metadata organization, deployment and distribution for enterprise customers building Apps on Salesforce Platform.

Salesforce enterprise customers have historically used org-based deployment approaches like Changesets and ANT deployment to deploy metadata to their orgs. With Unlocked Packages, there is a package-based deployment unit that offers an alternative to Changesets and metadata deploys.

Unlocked Packages offers certain distinct advantages over Change Sets and the ANT Migration Tool (See [here](#pkg-benefits) for the benefits of unlocked packages over current technologies).

([Back to the Table of Contents](#toc))

<h2 id="pkg-benefits">
What are the benefits of packaging over Change Sets and ANT Migration Tool?
</h2>

The following are some key benefits of packaging:

1. Packages promote a source-driven development approach and use the source format that Salesforce DX uses. The metadata that you specify when you build a package is the same metadata that is contained in your Salesforce DX project and version control system. 
2. With package versions, you have an immutable, versionable artifact that can be used in CI, UAT, etc. The same artifact that passes all your CI tests and UAT can be installed in your production orgs.
3. With a version-based approach, you can develop and install different package versions in target orgs and adopt a build once, deploy anywhere approach.
4. You can express dependencies among packages. With this capability, you can embrace a modular approach where each package contains a set of metadata that represents a distinct unit of functionality, with well-defined dependencies among packages.
5. Packages can be tracked in an installed org as they show up as installed packages with a set of associated metadata. In a production org, for a given metadata, you can view the package it is associated with, and for a given package, you can view the set of all metadata owned by the package.
6. Packages provide a repeatable, scriptable and trackable way for managing change as you develop functionality using Salesforce DX.

([Back to the Table of Contents](#toc))

<h2 id="pkg-pkgver">
What is a package and what is a package version?
</h2>

A **package** is a container for Salesforce metadata. It represents a distinct deployable unit of functionality that is iterated over time. You can add metadata to a package and take a snapshot of it, anytime. This snapshot is called a **package version**. You can create package versions any number of times. Each package version, once created, is immutable and is associated with a single package. You can install a package version in any Salesforce environment - scratch orgs, sandbox orgs, or production orgs. Installing a package version deploys the metadata that was specified when the package version was created. 

([Back to the Table of Contents](#toc))

<h2 id="unpackaged-md">
What is "unpackaged metadata"?
</h2>

Broadly speaking, the metadata in your production org can be categorized into three buckets:

1. The set of metadata that Salesforce provides - such as  Sales Cloud, Service Cloud, etc. The Account object or the Case object falls under this category.
2. Metadata that is part of AppExchange packages installed in the production org.
3. *The rest of the metadata*. This can be a Custom Field or Apex Trigger that you added to the Account object, the customizations that you made to an AppExchange app, or a brand new app that your team built in-house. This is the sum total of all *custom stuff* that you have built over the years in your org. This category of metadata is referred to as **unpackaged metadata** in this FAQ.

([Back to the Table of Contents](#toc))

<h2 id="pkg-types">
What are the different types of packages and which one should I use?
</h2>

**If you are a customer or someone developing something for a specific customer, it is recommended that you use unlocked packages; if you are an ISV partner that wishes to distribute your App on AppExchange for a set of customers, managed packages is the recommended solution. For more details, read on...**

You may have heard about many package-related terms - managed packages, Package 2, Developer-controlled packages, DCPs, unlocked packages, locked packages, unmanaged packages, Salesforce DX packages, second-generation packages... Let’s demystify these now. 


**First-Generation Packaging**

Before Salesforce DX came along, we had two types of packages:

 - Unmanaged packages used for one-time deployment of metadata.
 - Managed packages geared towards Salesforce partners that build and list apps on AppExchange.


**Second-Generation Packaging**

With Salesforce DX, we are launching Unlocked Packages and Managed second-generation packages (aka managed 2GPs). We want packaging to work as well for customers as they do for partners.

Unlocked Packages are designed primarily to address the packaging needs of customers. Unlocked packages offer a super-set of features compared to unmanaged packages.

Managed 2GPs are geared towards ISVs who want to develop and list apps on AppExchange. While Managed 2GPs are beta, there are still certain key parity features (Push Upgrades, LMA, ability to list on AppExchange, patch versions, etc.) that are missing in them. So, if you are a Salesforce Partner, it is recommended that you continue with first-Generation Managed Packages until we build those feature parity items in managed 2GPs.

([Back to the Table of Contents](#toc))

<h2 id="isv-packaging">
Q. If I am an ISV, what type of packaging should I use?
</h2>

If you are an ISV that develops and lists apps on AppExchange, here are our recommendations (See [here](#pkg-types) for info about different types of packages):

1. Continue to use first-generation managed packages (1GPs) when it comes to listing and distributing apps to customers.
2. In upcoming releases, we are working on enabling the listing of second-generation packages (managed 2GPs) on AppExchange and building feature parity with first-generation managed packages - features like LMA and push upgrades. Until we can develop these features and launch managed second-generation packages as GA, we recommend that you continue with first-gen packages.
3. You can definitely consider second-generation managed packages (managed 2GPs) during development cycle. You can build and test your metadata using scratch orgs and 2GPs and use 1GPs for final rounds of testing and building the official release candidate. This will enable you to adopt the benefits of managed 2GPs in development stages.
4. Unlocked packages are designed primarily keeping customer use cases in mind. There could be scenarios where unlocked packages are helpful to ISVs as well. Familiarize yourself with what unlocked package can do and see if they map to some of your use cases.

([Back to the Table of Contents](#toc))

<h2 id="hello-world">
Q. How do I build and deploy a new app using unlocked packages? What’s my Hello World App for unlocked packages?
</h2>

In less than 5-10 minutes, you can build and test your *Hello World App* to get a feel for unlocked packages. The only precondition is that you have a Salesforce DX environment set up with Salesforce CLI and that you have enabled Dev Hub and Unlocked Packages in your Dev Hub Org. 

If you haven’t done it already, refer to this [documentation](https://developer.salesforce.com/docs/atlas.en-us.sfdx_setup.meta/sfdx_setup/sfdx_setup_intro.htm) for setting up the Dev Hub and Packaging.

Follow these simple steps and you have for yourself a package!


1. Set up a directory on your local machine and copy the sample repo. Let’s presume this is the app that your team is building for meeting a business need.
   
    ```git clone https://github.com/dreamhouseapp/dreamhouse-sfdx.git```  

    ```cd dreamhouse-sfdx/```  


2. Authenticate to your Dev Hub org.  
```sfdx force:auth:web:login --setdefaultdevhubusername```


3. Create a new unlocked package.  
```sfdx force:package:create --name Dreamhouse-App --packagetype Unlocked --path force-app --nonamespace```  
Note how the sfdx-project.json is automatically updated with information about this package.


4. Create a package version. When you create a package version, you are associating metadata with your package. Over the course of time, you may create many package versions for a given package. Once created, a package version serves as an immutable artifact containing a specific set of metadata. This is the same metadata that you specify at the package version creation step.   
```sfdx force:package:version:create --package Dreamhouse-App --installationkey mypkgisnowsecure#%^ --wait 20```

    In this example, *force-app* is the directory in which your package metadata is located in Salesforce DX format. This was specified when the the `force:package:create` command was run in step # 3. The other pieces of information needed for package version creation are stored in sfdx-project.json. The installation key offers a security mechanism for your package version - this key has to be speicified while installing the package for the installation to be successful.


5. Create a scratch org where you can install the package.  
```sfdx force:org:create --definitionfile config/project-scratch-def.json --setalias sub1 --setdefaultusername```


6. Install the package in the scratch org.  
```sfdx force:package:install --package Dreamhouse-App@0.1.0-1 --publishwait 20 --installationkey mypkgisnowsecure#%^ --wait 10```  
    If `Dreamhouse-App@0.1.0-1` is not the alias for the package version created in step 4, update the value for the `--package` flag with the correct alias. See [here](#pkg-alias) for more info about package aliases.

1. Open the scratch org in the browser.  
```sfdx force:org:open```


8. In the scratch org, go to Setup -> Installed Packages, where you can see that the package has been installed.


9. Click the Package Name and the View Components button to validate that the components present in the project are now deployed as part of the package.


Congratulations! You have now created and installed your first unlocked package!

([Back to the Table of Contents](#toc))

<h2 id="organize-md">
Q. How can I organize my unpackaged metadata using unlocked packages?
</h2>

See [here](#unpackaged-md) for a quick overview of *unpackaged metadata*.

**Step 1** - Extract a small set of unpackaged, self-contained metadata from your production org and convert it to Salesforce DX format.

How to identify this set of self-contained metadata is a bit of a science and a bit of an art. You can try and select metadata that represents an app, or customization to an AppExchange app, or customizations to Sales Cloud or an Account object, or some set of metadata that represents a functional unit (e.g.: a library of apex classes). We know this is a challenging task. We plan to produce material to help in this. See this [blog](https://developer.salesforce.com/blogs/2018/02/getting-started-salesforce-dx-part-1-5.html) series that talks about DX and unlocked packages.

**Step 2** - Push this metadata to a scratch org using `force:source:push` and validate that this is the metadata that you want to be part of a new package. If you are missing some metadata, go back to Step 1.

**Step 3** - Create an unlocked package using this metadata (see [here](#hello-world) for how to create a package). Make sure this package has no namespace (supply the `--nonamespace` flag to the `force:package:create` command). See [here](#namespace) for more info about namespaces and packages.

**Step 4** - Test the package in CI and UAT environments. If you encounter issues, you may have to go back to Step 1 and validate that you have the right set of metadata. Once the package has passed all CI runs and UAT on sandboxes, install the package in the production org. This installation does **not** deploy new metadata as what’s contained in the package is already present in the production org. But what this does is *migrate* the metadata from the unpackaged set to the package so that it is now part of the package in the production org. From this point forward, you can iterate over this metadata set using the package.

These steps, along with the specification of dependencies among packages, can help in organizing the unpackaged metadata into a set of interdependent packages.

([Back to the Table of Contents](#toc))

<h2 id="common-pkg-ops">
Q. What are some of the common packaging operations?
</h2>

**Package Creation**  
A package is a container of metadata. When you create a package, you specify some characteristic info about the package: name, description, namespace associated with it, package type and so on. 

**Package Version Creation**  
As your development team is working on some functionality, you, as a release manager, may want to take periodic snapshots of the functionality when it is in a functional state. Such snapshots are package versions. When the package version creation operation is executed, a copy of the current state of the metadata in the project folder (the directory specified in `force:package:create` command) is used to create the package version. You can create any number of package versions. Each version, once created, is immutable and can be installed in any Salesforce org.

**Package Install**  
A package install is an operation whereby the metadata of a package is deployed to the target org. A package install is actually a package version install - it is the deployment of metadata contained in a specific package version of a package. A package is typically associated with 100s or even 1,000s of package versions. When you install a package, you specify which one of the many versions you intend to install. *Install* and *deploy* mean the same in this context.

**Package Upgrade**  
Installing a version on top of a previously installed package version is a package upgrade. When you perform a package upgrade, the metadata of the new package version is deployed to the org. The package upgrade process can add new metadata to the org, modify existing metadata, and may even delete and deprecate some metadata depending on what’s there in the new version of the package.

**Package Downgrade**  
See [here](#downgrade-pkg) for more info about package downgrades.

**Package Uninstall**  
This is an operation where you are deleting the metadata and associated data related to an installed package.

See [here](#pkg-cli-ops) for how you can perform these operations using the Salesforce CLI.

([Back to the Table of Contents](#toc))

<h2 id="pkg-alias">
Q. What are package aliases and how do they work?
</h2>

In Summer '18 release, we made a set of improvements to enhance the usability of packaging commands in the Salesforce DX CLI. As part of that, we introduced the concept of *aliases* to packages. Package aliases were developed to reduce the usage of package ids as the latter is non-intuitive.

As most of you already know, aliases are used elsewhere in the CLI; for E.g.: alias for a scratch org. 

With package aliases, the following changes were introduced, all aimed at improving the usability of packaging commands:
1. When the `force:package:create` command runs successfully, a package alias is automatically created in the *packageAliases* section of sfdx-project.json. E.g: `"revenue-optimizer": "0HoB00000004CWDKA2"`. Here, the alias name (revenue-optimizer) is the name of the package supplied in the `--name` attribute of `force:package:create` command and the 0Ho id is the package id that it maps to.
2. When the `force:package:version:create` command runs successfully within the `--wait` time specified, a package version alias is automatically created in the packageAliases section of sfdx-project.json. E.g: `"revenue-optimizer@0.1.0-4": "04tB00000006DewIAE"`. Here, the alias name (revenue-optimizer@0.1.0-4) is the name of the package supplied in the `name` attribute of `force:package:create` command and the version number appended in the *major.minor.patch-build* number format. The id is the package version id that it maps to.
3. It is possible to manually add, modify or remove entries in the packageAliases section.
4. Once aliases are set up like this, in the rest of sfdx-project.json, the aliases can be used instead of cryptic ids. This makes the sfdx-project.json much more readable.
5. In packaging commands, the aliases can be used instead of ids. In flags like `--package` and `--packages`, there is an option to supply either the package alias or the appropriate ids. Using the aliases approach makes the sfdx-project.json file, commands and scripts much more intuitive and readable.

([Back to the Table of Contents](#toc))

<h2 id="pkg-cli-ops">
Q. How can I use Salesforce CLI to perform package-related operations?
</h2>

See [here](#common-pkg-ops) if you want to first understand what these operations are intended for.


**Development-side Commands**

Create a package - `force:package:create`

Add, modify and remove metadata from a package - Update the workspace files

Get a listing of all packages - `force:package:list`

Update info about a package - `force:package:update`

Create a package version - `force:package:version:create`

Obtain info about the status of a package version create request - `force:package:version:create:report`

Obtain info about the status of all package version create requests - `force:package:version:create:list`

Get detailed info about a single package version - `force:package:version:report`

Get a listing of all package versions - `force:package:version:list`

Update info about a package version - `force:package:version:update`


**Install-side Commands**

Install a package - `force:package:install`

Status of an install request - `force:package:install:report`

Info about all installed packages in an org - `force:package:installed:list`

Uninstall a package - `force:package:uninstall`

Status of an uninstall request - `force:package:uninstall:report`

([Back to the Table of Contents](#toc))

<h2 id="simple-json">
Q. How can I specify attributes in the sfdx-project.json file for managing my packages?
</h2>

Some of the sfdx-project.json updates are done automatically for you. See [here](#pkg-alias) for more info. 

See this example below for a section of sfdx-project.json file

(this deliberately avoids package dependencies to keep things simple. See [here](#specify-pkg-dep) for a simple example of package dependencies and [here](#complex-pkg-deps) for a more complex scenario)  

```

{
    "packageDirectories": [

        {
            "package": "revenue-app",
            "path": "common",
            "default": true,
            "versionNumber": "0.1.0.NEXT",
            "versionName": "Summer 2018",
            "versionDescription": "Summer 2018 Release",
            "definitionFile": "force-app"
        },

        {
            "package": "revenue-app-utils",
            …
        }
        
    ],

    "namespace": "",
    "sfdcLoginUrl": "https://login.salesforce.com",
    "sourceApiVersion": "44.0",


    "packageAliases": {
            "revenue-app": "0HoB00000008OoZKAU",
            "revenue-app-utils": "0HoB00000008OoYMZD"
        }
}

```

package (required field) - this is the package and everything in this sub-section contains info about that package. You can use aliasing and specify the package alias here or directly specify the 0Ho id of the package. This info is auto-populated in the sfdx-project.json when the `force:package:create` command executes successfully.

path (required field) - root folder where the source metadata of the package is stored in the local workspace in Salesforce DX format. The metadata in this folder is used to create the package version when the `force:package:version:create` command is run. This info is auto-populated in the sfdx-project.json when the `force:package:create` command executes successfully.

versionName (required field) and versionNumber  (required field) - name and version number. The latter is in major.minor.patch.build format where each one of these is a whole number. See [here](#ver-num) for more info about package version numbers. This info is auto-populated in the sfdx-project.json when the `force:package:create` command executes successfully.

versionDescription (optional field) - description of the package version that you’re creating.

definitionFile (optional field) - this is a json file similar to the scratch org definition file that you can use to specify features and org settings that need to be enabled for the successful deployment of your package metadata. For E.g.: if your package metadata requires multi-currency to be enabled in the target org before the metadata can be successfully deployed, this feature should be specified in the json file. In many scenarios, you may want to use the *same* json file for both package version creation and scratch org creation.

([Back to the Table of Contents](#toc))

<h2 id="supported-md">
Q. What can be packaged in Unlocked Packages? Will unlocked packages support ALL metadata in future?
</h2>

The metadata coverage report (see [here](https://mdcoverage.secure.force.com/docs/metadata-coverage/44) for more info) is the source of truth for metadata supported in unlocked packages. While the vision is to provide metadata API support for all Salesforce Metadata and enable everything to be packaged in an unlocked package, it is going to take a while to get there, and the metadata coverage report will track the progress. Having said this, singletons (things that have only one instance of them in an org) like org settings and preferences, and metadata without top-level ids (E.g.: Standard Value Sets) are NOT packageable in unlocked packages. 

([Back to the Table of Contents](#toc))

<h2 id="pkg-install">
Q. How and where can I install my unlocked packages?
</h2>

An unlocked package can be installed in any Salesforce environment - scratch orgs, sandbox orgs, trial orgs, and production orgs (see [here](#pkg-rel-state) for a note on beta vs released state of a package version).


A package can be installed in two different ways:

**Salesforce CLI command**

`force:package:install --package <package-alias or 04t id> --targetusername <org where the package should be installed>` 

**Salesforce UI**

By appending the following to the browser URL: *packaging/installPackage.apexp?p0=<04t id>*

The 04t is the package version ID. This represents the version of the package to install. The 04t ID is returned when the `force:package:version:create`  CLI command returns successfully. You can use `force:package:version:list` command to get a list of package versions associated with a package.

See [here](#common-pkg-ops) if you want to know more about different packaging operations.

([Back to the Table of Contents](#toc))

<h2 id="installed-pkgs">
Q. How can I find out about the set of packages installed / deployed in my org?
</h2>

In any given org, you can obtain information about installed packages in one of the following ways.

**Salesforce CLI**

`force:package:installed:list` command

**Salesforce UI**

* Go to Setup
* In Setup, search for “Installed Packages”
* Go to Apps -> Installed Packages page
* The installed packages are listed in this page. Click on any package name to get more info about the package like version info, list of metadata present in the package, etc. This list shows info about ALL installed packages, including managed packages installed from AppExchange.

See [here](#pkg-install) for how to install a package in an org.

([Back to the Table of Contents](#toc))

<h2 id="pkg-upgrades">
Q. How do I iterate on my package? What happens when I add, edit and delete package metadata?
</h2>

Unlocked Packages provide a very open framework for making metadata changes. Before we talk about these changes, make sure you understand the difference between a package and a package version. See [here](#pkg-pkgver) for more info. Also take a look at [this](#common-pkg-ops) for packaging operations.


Versioning is one of the core differentiating features of packaging. It is versioning that enables iterative development, where you can continually update an artifact (package) in a controlled and manageable way in response to business needs (feature requests and bug fixes).


When it comes to versioning and changes introduced in versions, it is helpful to look at them from two angles - development stage and deployment stage.


**Development and Build Stages**

When you create a new package version, you can introduce the following changes in relation to a previous package version:

1. Add new metadata to the project workspace, such as  a new Lightning Page or a new Apex Controller
2. Modify existing package metadata, such as updating a business process in response to end-user feedback or updating an Apex method to fix a bug.
3. Delete existing package metadata, such as deleting Visualforce pages as they are being replaced by Lightning Pages, deleting a custom field as it has become obsolete, etc.

The above-described changes can be applied in a variety of ways (Setup UI in scratch orgs, in VS Code, in Developer Console, etc.) and ultimately make it to the version control system for the project (See [here](#vcs) for relationship between version control system and packages). The release manager then pulls in the right set of metadata to a local workspace and creates a new package version so that the package version captures these changes.


**Deployment Stage**

Let’s presume you are trying to install ver 2.0 of an unlocked package in an org where ver 1.0 is currently installed. During this package upgrade process, the following changes are applied:

1. Metadata that is new in v2.0 in comparison to v1.0 are deployed or created in the target org.
2. Metadata that has changed are applied. For example, if a Lightning Page or Apex Controller has been modified in v2.0, those modifications are applied as part of package upgrade.
3. When it comes to metadata delete - this is the scenario where something (custom field or report or apex class) present in v1.0 was deleted in v2.0 - there are two possible outcomes:
   * For the first class of metadata types, the metadata is deleted from the target org.
   * For the second class of metadata types, the metadata is marked as deprecated. When metadata is marked deprecated, it is still part of the package but it shows up as deprecated when you look at the metadata in Setup so that it is easier for admins to track and delete them from the org. We understand that this deletion of metadata in the installed org is a manual step that should be automated. In many scenarios, you may want the package upgrade process to automatically delete metadata that was removed from the package. We plan to make this process more automatable in future releases.

([Back to the Table of Contents](#toc))

<h2 id="ver-num">
Q. How do I work with Version Numbers?
</h2>
A package version is an immutable artifact that is a snapshot of a package with a specific set of metadata. A package version is created with a successful execution of the `force:package:version:create` command. Every package version you create has a unique version number. The version number is in the major.minor.patch.build number format where major, minor, patch and build are integers grater than or equal to zero.

Here are some things to keep in mind with respect to version numbers:

1. You specify the version number in the `versionNumber` field in sfdx-project.json. This is a required field.
2. You increment version numbers so you keep track of changes. You typically increment major number for major releases and minor number for minor releases. You can try and adopt some of the best practices associated with semantic versioning when you manage version numbers.
3. With Winter '19 release, you can use patch numbers for patch releases. In the past, we required patch numbers to be 0; this limitation goes away with Winter '19 release.
4. If your team is working on a specific release (E.g.: ver 3.5), you may find the process of updating build number laborious especially if you are using CI to create a lot of package versions. We recommend that you use the `NEXT` keyword to simplify this. When you specify `NEXT` as in `1.3.0.NEXT`, we auto-increment the build number for you. When you want to do the same with package dependencies, you can use `LATEST` in the dependencies section of sfdx-project.json file. The `NEXT` and `LATEST` keywords currently work only with build numbers.

([Back to the Table of Contents](#toc))

<h2 id="modify-md-installed-org">
Q. What are the implications of modifying or deleting metadata that is part of an unlocked package *directly* in the installed org?
</h2>

You have developed an unlocked package, taken it through all of the testing cycles - integration, user acceptance, etc. - and you are now ready to install it in your production org. One question that arises is, what happens if your admin has to make an emergency change to the metadata *directly* in the production org? For example, the admin might have noticed that some permission sets are granting too much access, a page layout is missing some critical fields, or a report is missing a key column. If this metadata is part of an unlocked package, should the admin contact the development team and take it through the full cycle of development, testing, UAT and then do the deployment? The answer is yes from a best practice of software development process perspective. But what if one or all of these changes need to be made *right now!*?

With unlocked packages, the admin has the flexibility to make these changes *directly* in production. The fact that these components belong to a package won’t restrict the flexibility that the admin has towards making changes. However, the admin has to ensure that the development team is informed of these changes. Once the development team is aware of these changes and incorporate them in the next version of the package, installing future versions of the package won’t undo the changes made by the admin in the production org. However, if the changes made directly in production are not applied to the metadata of the package by the development team, subsequent package upgrades will overwrite the changes made in production.

([Back to the Table of Contents](#toc))

<h2 id="delete-deprecate">
Q. During a package upgrade, how are destructive changes handled?
</h2>

See [here](#pkg-upgrades) for more info.

([Back to the Table of Contents](#toc))

<h2 id="remove-md">
Q. How can I remove metadata from my package?
</h2>

There are some related scenarios here:
1. Delete metadata as it is not needed. For E.g.: as part of moving towards Lightning, you may be replacing Visualforce pages with Lightning. In such a scenario, you intend to delete the now-obsolete Visualforce pages when you ship the new version of the package with Lightning pages.
2. Remove the metadata from the package as the package is being refactored, and the desire is to move the metadata from one package to another package.
3. Remove the metadata *temporarily* from the package *directly* in an installed org as there are some issues. In this scenario, the intention is to *release* the metadata from the package and make it part of unpackaged metadata in the org. Currently, this can only be done via UI in installed org (in Installed Packages detail page) for no-namespace, unlocked packages by clicking on the "Remove" link next to the metadata in Package Components section. 
   
For scenarios 1 and 2, you delete the metadata from the DX project and build a new version of the package. When you install the new version in an org, the changes are applied. See [here](#pkg-upgrades) for more details.
For scenario 3, you do the same if you determine that you want to permanently remove the metadata from the package.

See [here](#pkg-refactor) for more information about scenario 2.

([Back to the Table of Contents](#toc))

<h2 id="pkg-refactor">
Q. How can I refactor my package?
</h2>

Let's suppose you have installed ver 1 of your unlocked package pkg1. As part of refactoring your App, you decide to move some metadata from pkg1 to a new package pkg2. 

You will refactor the package by following these steps:
1. Identify the metadata that you want to move out of pkg1.
2. Remove metadata identified in step 1 from pkg1 repo and create ver 2 of pkg1.
3. Add the metadata identified in step 1 to a different DX folder / project / repo and create ver 1 of pkg2 using that metadata. You may add other metadata to complete pkg2.
4. After proper testing and UAT, install ver 2 of pkg1 in the org where ver1 of pkg1 has been installed.
5. Install ver1 of pkg2 in the same org.
6. At the end of step 5, the metadata identified in step 1 would have migrated from pkg1 to pkg2.

([Back to the Table of Contents](#toc))

<h2 id="downgrade-pkg">
Q. Can I downgrade my package?
</h2>
In the context of packaging, a downgrade is installing a lower version of a package on top of a higher version. For E.g.: this is when you try to install ver 2.0 of your package in an org where ver 3.0 has been installed. When you downgrade a package, the following occurs:

1. The metadata of the package version (ver 2.0 in this example) is deployed to the target org.
2. Any metadata that is present as part of the package in the installed org (ver 3.0 metadata in this example) is modified, deleted or deprecated based on whether they are present in the version being installed. For E.g.: an apex class will be modified to the ver 2.0 copy of it as part of the downgrade. If a custom object foo__c is not present in ver 2.0 but present in ver 3.0, foo__c is marked deprecated (See here for more info about what deprecated means in this context). If a report object is not present in ver 2.0 but present in ver 3.0, the report is deleted from the org.

You should consider any downgrade of a package carefully. Package downgrades can fail due to dependency scenarios. For E.g.: if ver 3.0 has an apex class that is referenced by another apex class in a different package, downgrading to ver 2.0 would fail with a user-friendly error message if that apex class is not present in ver 2.0 of the package.

([Back to the Table of Contents](#toc))

<h2 id="pkg-security">
Q. How can I secure my packages?
</h2>
You can secure your packages by setting an *installation key* to your package versions. During the package installation process, you are challenged to supply the installation key and failing to provide it will abort the installation process.

You can set the installation key in two ways:
1. Provide the key to the `--installationkey` flag of the `force:package:version:create` command. If you choose to skip this security feature, you have to supply the `installationkeybypass` flag to the version create command. Not supplying either of these flags results in an error. This enforces a *security-by-default* mindset.
2. You can set or change the key by providing the key to the `--installationkey` flag of `force:package:version:update` command.

In future (safe harbor), we have plans to provide additional security mechanisms to enable enhanced security for your packages.

([Back to the Table of Contents](#toc))

<h2 id="pkg-rel-state">
Q. What is the difference between beta package versions and released package versions?
</h2>

When you create a package version using `force:package:version:create`, we set the state of the package version to beta. Beta package versions are internal test candidates that are used in test cycles including CI. To prevent a beta package version from being inadvertently installed in production orgs, we prevent the installation of beta package versions in any org that is not a scratch org, sandbox org or Developer Edition org.

As part of your development process, you end up creating lots and lots of package versions that are all of beta state. When a package version passes all tests including UAT (User Acceptance Testing), you can promote that package version to be a release candidate by issuing the following CLI command:

```force:package:version:promote --package expense-manager@1.3.12-26```

```force:package:version:promote --package 04tB0000000IB1EIAW```

A package version promoted to *released* state can be installed in any org.

For a given major.minor.patch package version number (see [here](#ver-num) for more info about version numbers), there can be at most one package version in *released* state. Once you set a package version to released state, you cannot undo that operation. It is recommended that you increment major, minor and/or patch version number in your sfdx-project.json file once you promote a package version. We will try and automate this in future releases using constructs similar to *npm version minor*.

([Back to the Table of Contents](#toc))

<h2 id="pkg-deps">
Q. Can my packages depend on one another?
</h2>
Yes. One of the value propositions of packages is that you can develop and maintain a set of interdependent packages. By supporting dependencies, packages promote modular development with a dependency framework.


The following are some of the main use cases around dependencies:


1. An unlocked package depending on an AppExchange Package. This is the use case where you have purchased an AppExchange package and want to customize it. In such a scenario, the unlocked package that you are going to build will be dependent on the installed AppExchange package and will contain all of the customizations made to the AppExchange package. For E.g: you might have added additional custom fields to custom objects that are part of the AppExchange package, or have created new reports and dashboards to better visualize the information. All of these customizations can be part of an unlocked packafe.
2. An unlocked package depending on another unlocked package. Maybe you are developing a *Reimburse Me App* to enable your employees to submit expense reports. This App depends on some backend integration functionality that has already been built that enables Payroll to take care of approved expenses. In such a scenario the *Reimburse Me App* unlocked package will depend on the *Payroll Processor* unlocked package.

Keep the following in mind when you think of package dependencies:
1. Multi-level dependencies are supported. E.g.: pkg-z can depend on pkg-y which in turn depends on pkg-x.
2. A single package can depend on multiple unlocked packages and AppExchange packages.

See [here](#specify-pkg-dep) to see how you can express dependencies in `sfdx-project.json` and see [here](#complex-pkg-deps) for a more advanced example.

([Back to the Table of Contents](#toc))

<h2 id="specify-pkg-dep">
Q. How can I specify dependencies among packages?
</h2>

See [here](#pkg-deps) to know more about package dependencies.

You express dependencies within the packageDirectories section of sfdx-project.json.

The following are the different types of supported dependencies

**An unlocked package depending on another package in the same dev hub**

```
"dependencies": [

   {
      "package": "utils-pkg",
      "versionNumber": "2.5.0.LATEST"
    }
]
```

This expresses that the current package depends on another package of version number 2.5 with package alias utils-pkg.. This package can be a managed second-generation package or another unlocked package. By using the keyword LATEST, you can iterate on the two packages simultaneously.


**An unlocked package depending on another package developed in a different dev hub**

There are two main use cases for this:

 * The package that you are depending on was developed by another company in another hub
 * The package that you are depending on is an AppExchange App.

```
"dependencies": [
   {
      "package" : "apex-lib@1.7.4"
    }
]
```

This expresses that the current package depends on another package with package alias `apex-lib@1.7.4`. This is the way to specify dependencies if the package that the current package depends on, was developed in a different dev hub. You cannot use the `LATEST` keyword in this scenario.

For a more complex scenario of package dependencies, please see [here](#complex-pkg-deps).

([Back to the Table of Contents](#toc))

<h2 id="pkg-dep-enforcement">
Q. When are package dependencies enforced?
</h2>

1. Package dependencies are used during development time. E.g.: if you are developing packages and if your apex class in package pkg-y is invoking another apex class that is present in package pkg-x, you should express that pkg-y depends on pkg-x for the package version creation of pkg-y to succeed. (See [here](#specify-pkg-dep) for how to express these dependencies)

2. Package dependencies are enforced at installation time. If pkg-y depends on pkg-x, you will have to install pkg-x before you can install pkg-y; otherwise the installation of pkg-y fails with an error message. Also, you have to ensure that the appropriate versions of the package are installed in the org. For E.g.: if pkg-y ver 4.0 has a Lightning Page that uses an apex controller from pkg-x ver 3.0, attempting to install pkg-y ver 4.0 fails in an org where pkg-x ver 2.0 is installed. See [here](#dep-pkg-install) for how you can automate the installation of dependent packages.

([Back to the Table of Contents](#toc))

<h2 id="complex-pkg-deps">
Q. Can you give me an example of an sfdx-project.json file with many interdependent packages?
</h2>

See [here](#pkg-deps) to know more about package dependencies. See [here](#specify-pkg-dep) for a simple example of package dependencies.

Let’s consider the following scenario:

Package *Reimburse Me App* depends on two packages - *Expense Manager* and *Travel Booker*. *Expense Manager* depends on an apex library built in-house as an unlocked package - *Utils*. *Travel Booker* is a package that extends a purchased AppExchange App *Travel Anywhere!*

**packageDirectories section of sfdx-project.json file for Travel Booker unlocked package**

*(working on ver 3.2 of the Travel Booker package that depends on ver 4.5 of Travel Anywhere! AppExchange package installed in production org. The package version id of ver 4.5 of the AppExchange App is 04tB0000000IB1EIAW)*

```
{
    "path": "travel-booker-workspace",
    "package": "travel-booker",
    "versionName": "v 3.2",
    "versionDescription": "Summer 2018 Release",
    "versionNumber": "3.2.0.NEXT",
    "dependencies": [
        {
            "package" : "travel-anywhere@4.5.0"
        }
    ]            
}
```

**packageDirectories section of sfdx-project.json file for Expense Manager package**

*(working on ver 4.7 of the Expense Manager package that depends on ver 2.6 of Utils package whose package id is 0HoB00000004CFpKAL)*

```
{
    "path": "expense-manager-workspace",
    "package": "expense-manager",
    "versionName": "v 4.7",
    "versionDescription": "Summer 2018 Release",
    "versionNumber": "4.7.0.NEXT",
    "dependencies": [
        {
            "package": "utils",
            "versionNumber": "2.6.0.LATEST"
        }
    ]               
}

```

**packageDirectories section of sfdx-project.json file for Reimburse Me package**

*(working on ver 3.8 of the Reimburse Me package that depends on ver 3.2 of Travel Booker package and ver 4.7 of Expense Manager package)*

Note: you have to declare all of the transitive dependencies in the order in which these packages should be installed from a dependency stand-point.

```
{
    "path": "reimburse-me-workspace",
    "package": "reimburse-me",
    "versionName": "v 3.8",
    "versionDescription": "Summer 2018 Release",
    "versionNumber": "3.8.0.NEXT",
    "dependencies": [
        {
            "package" : "travel-anywhere@4.5.0"
        },
        {
            "package": "travel-booker",
            "versionNumber": "3.2.0.LATEST"
        },
        {
            "package": "utils",
            "versionNumber": "2.6.0.LATEST"
        },
        {       
            "package": "expense-manager",
            "versionNumber": "4.7.0.LATEST"
        }
    ]            
}
```

**A note on package aliases**

See [here](#pkg-alias) to better understand package aliases. For the sfdx-project.json to look like what's shown above, it is presumed that there is a `packageAliases` section in the `sfdx-project.json` that does the alias-to-id mapping.

```
"packageAliases": {
   "travel-anywhere@4.5.0": "04tB0000000IB1EIAW",
   "travel-booker": "0HoB00000004CFuKAM",
   "utils": "0HoB00000004CFpKAL",
   "expense-manager": "0HoB00000004CFuKAN",
   "reimburse-me" : "0HoB00000004CFuKAP"
}
```

([Back to the Table of Contents](#toc))

<h2 id="dep-pkg-install">
Q. How can I automatically install dependent packages?
</h2>
Let's presume your package pkgC depends on package pkgB which in turn depends on pkgA. In the target org, we require packages of appropriate versions to be installed in this order: pkgA, pkgB and pkgC. 

In Winter '19, we have made the process of installing dependent packages easier by surfacing info about dependent packages including the minimum version required and dependency order. You can use `force:data:soql:query` and run a statement like `SELECT Dependencies FROM SubscriberPackageVersion WHERE Id='04tB0000000NmnHIAS` to get dependency information in correct order. In this example, `04tB0000000NmnHIAS` is the package version id of pkgC.

See [this](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_dev2gp_extract_dependency_info.htm) in the product guide for more info.

([Back to the Table of Contents](#toc))

<h2 id="pkg-owner">
Q. Who owns the unlocked packages?
</h2>
The Dev Hub in which the package was developed is the owner of the package. The association between the dev hub and a package occurs when you execute the `force:package:create` command. The Dev Hub org against which this command is run is the owner of the package.

Please keep in mind the following about Dev Hub and unlocked package:

1. A Dev Hub can own many packages but a package is associated with one and only one Dev Hub.
2. The Unlocked Packages feature should be *enabled* in Setup -> Dev Hub page in the Dev Hub before `force:package:create` command can be successfully executed.
3. Once created, the ownership of a package cannot be transferred from one dev hub to another dev hub.
4. Please ensure that the packages installed in your production org (s) are owned by your official Dev Hub, not a trial Dev Hub org. If your Dev Hub org expires, your packages may become inoperable.
5. Even though a Dev Hub owns a package, the package metadata is not stored in the Dev Hub - that happens only if the package is installed in that org using the `force:package:install` command. 

([Back to the Table of Contents](#toc))

<h2 id="namespace">
Q. What is a namespace and should I use one with my unlocked package?
</h2>

A namespace is a unique identifier that is owned by your company when you register it with Salesforce. If you associate a namespace (like Acme) with a package (see [here](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_reg_namespace.htm) for how to associate namespaces with Dev Hub), the API names of all metadata added to the package will have the prefix Acme__. Hence, namespaces offer a mechanism to name and organize metadata in your org.


Keep the following in mind when you think of namespaces and packages:

1. Namespaces are optional for unlocked packages. There are many use cases where you won’t want to use namespaces with unlocked packages. If you intend to move your metadata from unpackaged state in your production org to unlocked packages (see [here](#organize-md) for more info), create packages without a namespace.
2. A Salesforce DX Dev Hub can be linked to multiple namespaces so it’s possible for you to use different namespaces for different packages.
3. Multiple packages can be associated with the same namespace.
4. A given package can be associated with only one namespace.
5. A package can be associated with a namespace only during package creation (`force:package:create`) and cannot be changed, once associated.
6. One of the known issues with namespaced unlocked packages is that the Apex in such a package is hidden (in installed orgs) and the experience associated with debug logs is sub-optimal (as info in debug logs are obfuscated). It is on the roadmap to fix this so that Apex in namespaced unlocked packages is visible in installed orgs, but the earliest that can happen is Summer '19. Until we fix this, it is recommended that you use no-namespace unlocked packages. Another known issue is the inability to remove a metadata from a package *directly* in the installed org (see [here](#remove-md) for more info). 

([Back to the Table of Contents](#toc))

<h2 id="delete-pkg">
Q. How can I delete my package and package versions?
</h2>

It is currently **not** possible to delete package or package versions. In a future release, we will provide capabilities for the same. For now, you can rename them (using `force:package:update` and `force:package:version:update` Salesforce CLI commands) to denote that you are no longer using them.

Click [here](#common-pkg-ops) for more info about packaging operations.

([Back to the Table of Contents](#toc))

<h2 id="vcs">
Q. What is the relationship between version control system and packages?
</h2>

When it comes to unlocked packages, the source of truth is your version control system (vcs). Having said that, packages don’t explicitly link to a vcs. The step where you link the metadata that you want in a package to your package is in the `force:package:create` step and that requires the directory name of your SFDX project, not a vcs repo. Here are some best practices you can follow to ensure your vcs tracks changes made to your packages:

1. Use the `--tag` field in the `force:package:version:create` or `force:package:version:update` commands to store any vcs related tagging information (E.g.: commit info).
2. Use version control system commits to tag the set of metadata that was associated with a particular successful package version creation. This way, your version control system knows the state of metadata for any given package version.

([Back to the Table of Contents](#toc))

<h2 id="locked-pkgs">
Q. What are Locked Packages? Can I use them?
</h2>

Locked packages were in Pilot mode for a brief time and that pilot is no longer available. We may bring back Locked packages in future but that is uncertain with no set date. 
Unlocked Packages are Generally Available (GA) in Winter '19 and we recommend that you use unlocked packages.

([Back to the Table of Contents](#toc))

<h2 id="pkg-info">
Q. How can I get more info about Salesforce DX and Unlocked Packages?
</h2>

We have lots of additional information that goes into the details of how to work with Salesforce DX and packages. Following are some helpful resources:

## Salesforce DX

[Trailblazer Community for Salesforce DX](https://success.salesforce.com/_ui/core/chatter/groups/GroupProfilePage?g=0F93A000000HTp1)

[Trailhead Modules on Salesforce DX](https://trailhead.salesforce.com/trails/sfdx_get_started)

[Salesforce DX Blog Series](https://developer.salesforce.com/blogs/2018/02/getting-started-salesforce-dx-part-1-5.html)

[Salesforce DX Dev Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_intro.htm)

[Salesforce CLI Command Reference](https://developer.salesforce.com/docs/atlas.en-us.214.0.sfdx_cli_reference.meta/sfdx_cli_reference/cli_reference.htm)

[Salesforce DX Setup Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_setup.meta/sfdx_setup/sfdx_setup_intro.htm)

## Unlocked Packages
[Trailblazer Community for Packaging](https://success.salesforce.com/_ui/core/chatter/groups/GroupProfilePage?g=0F93A000000Lg5U)

[Trailhead Module on Unlocked Packages](https://trailhead.salesforce.com/trails/sfdx_get_started/modules/unlocked-packages-for-customers)

[Trailhead Module on Quickstart: Unlocked Packages](https://trailhead.salesforce.com/trails/sfdx_get_started/projects/quick-start-unlocked-packages)

([Back to the Table of Contents](#toc))
