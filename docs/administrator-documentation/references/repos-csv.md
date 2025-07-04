---
sidebar_label: Creating a repos.csv file
description: How to create a repos.csv file that defines your repositories and, potentially, an organizational hierarchy for them.
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# Creating a repos.csv file

Whether you are configuring mass ingestion of repositories into Moderne or you are defining an organizational hierarchy, you will need to create a `repos.csv` file that defines your repositories and configuration around them. 

This doc will provide you with everything you need to know about creating and using this file.

## Supported columns



| Column name | Required | Description                                                                                                                                                                                                                                                                                                                                   | Mass ingest | Org hierarchy |
| ----------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- | ------------- |
| cloneUrl    | `true`   | The URL of the repository that should be ingested. <br /><br />Example: `git@github.com:google/guava.git` or `https://github.com/openrewrite/rewrite`                                                                                                                                                                                             | ✅           | ✅             |
| branch      | `true`*  | The branch of the repository that should be checked out. This column can't be used with the `changeset` column. <br /><br /><sup>*</sup>Required for the agent (for Moderne Platform admins) or DX (for Moderne DX admins). Not required for mass ingestion or `mod git clone csv` (it will default to the default branch of the repository for both of those if none are provided). <br /><br />Example: `main`                                                                                                            | ✅           | ✅             |
| changeset   | `false`  | If provided, this will check out the repository at this specific commit SHA. This column can't be used with the `branch` column. <br /><br />Example: `aa5f25ac0031`                                                                                                                                                                              | ✅           |               |
| gradleArgs  | `false`  | Build arguments that are added to the end of the Gradle command line when building LSTs.<br /><br />Example: `-Dmyprop=myvalue`                                                                                                                                                                                                                   | ✅           |               |
| java        | `false`  | Configures the JDK to use during LST generation.<br /><br />Example: `17` or `17-tem` or `17.0.6-tem`                                                                                                                                                                                                                                             | ✅           |               |
| jvmopts     | `false`  | JVM options added to tools building LSTs. Must be configured before you can run the build command if non-standard VM options are required.<br /><br />Example: `-Xmx4G`                                                                                                                                                                           | ✅           |               |
| mavenArgs   | `false`  | Build arguments are added to the end of the Maven command line when building LSTs.<br /><br />Example: `-Pfast`                                                                                                                                                                                                                                   | ✅           |               |
| org*        | `false`  | If you want to configure an organizational hierarchy, you can provide one or more organization columns. Each column will specify an organization the repository should be part of. The column name should be `org` plus a number such as: `org1,org2,org3`. There is no limit for how many orgs you can define.<br /><br />Example: `openrewrite` |             | ✅             |




## Generating this file

To kick-start a `repos.csv` file, we recommend using "[repo fetchers](https://github.com/moderneinc/repository-fetchers)". Repo fetchers are scripts that generate a CSV file of all of your repositories by making API calls to your source code manager.

## Defining an organizational hierarchy

Org hierarchy is best explained with an example. Consider an organization that consists of two teams and two directors:

```
All
├── Director A
|       └── Team 1
|       └── Team 2
├── Director B

```

The following CSV file would represent this organizational structure:

```bash showLineNumbers
cloneUrl,branch,org1,org2,org3
"https://github.com/apache/maven-doxia","master","Team 1","Director A","ALL"
"https://github.com/Netflix/photon","main","Team 2","Director A","ALL"
"https://github.com/Netflix/ribbon","master","Director A","ALL"
"https://github.com/apache/maven-doxia","master","Director B","ALL"
```

In the above file, we define 5 organizations (ALL, Director A, Director B, Team 1, and Team 2) and 3 repositories. Notice that one repository can be defined multiple times if you want two distinct organizations to have access to it.

Also note that organizations on the left are children of organizations on the right. For instance, `Team 1` is a child of `Director A` which is a child of `ALL`.

:::tip
Comma-separated files (CSV) are best manipulated using a dedicated editor such as Microsoft Excel, Google Sheets, or Apple Numbers. Here is what the above CSV would look like on Apple Numbers:

<figure>
  ![](./assets/numbers-csv-screenshot.png)
  <figcaption></figcaption>
</figure>
:::

## Formatting details

* A single row may not have more columns than what is defined in the first row.
* A single repository may be associated with multiple organizations by being referenced in multiple rows. In the above example, `maven-doxia` is associated with both `Director B` and `Team 1`.
* You can add empty orgs to the beginning, middle, or end of a row to make them line up as desired (although you don't need to).
* You can choose to surround elements with quotes or not depending on your preference – just keep in mind general CSV formatting rules.
* You can order columns in any way.

<figure>
  ![](./assets/repos-csv-example.png)
  <figcaption>_An example demonstrating various orgs lining up with blanks in between_.</figcaption>
</figure>
