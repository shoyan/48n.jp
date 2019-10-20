---
layout: post
title: "Gradleのjavaプラグインとは"
date: 2018-01-21 00:52:36 +0900
comments: true
categories: java gradle
---

build.gradleでよく見かける`apply plugin: ‘java'`という記述について説明します。この記述をするとgradleにjavaプラグインが追加されます。プラグインはgradleの機能を拡張するためのもので、javaプラグインを追加するとjavaに関連するタスクが使えるようになります。

何もプラグインを追加していないgradleタスクは以下の通りです。

```bash
$ gradle tasks

> Task :tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------

Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.

Help tasks
----------
buildEnvironment - Displays all buildscript dependencies declared in root project 'gs-gradle'.
components - Displays the components produced by root project 'gs-gradle'. [incubating]
dependencies - Displays all dependencies declared in root project 'gs-gradle'.
dependencyInsight - Displays the insight into a specific dependency in root project 'gs-gradle'.
dependentComponents - Displays the dependent components of components in root project 'gs-gradle'. [incubating]
help - Displays a help message.
model - Displays the configuration model of root project 'gs-gradle'. [incubating]
projects - Displays the sub-projects of root project 'gs-gradle'.
properties - Displays the properties of root project 'gs-gradle'.
tasks - Displays the tasks runnable from root project 'gs-gradle'.

To see all tasks and more detail, run gradle tasks --all

To see more detail about a task, run gradle help --task <task>


BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
```

Javaプラグインを定義したあとのタスクは以下の通りです。

```bash
$ gradle tasks
Starting a Gradle Daemon (subsequent builds will be faster)

> Task :tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------ 
Build tasks
-----------
assemble - Assembles the outputs of this project.
build - Assembles and tests this project.
buildDependents - Assembles and tests this project and all projects that depend on it.
buildNeeded - Assembles and tests this project and all projects it depends on.
classes - Assembles main classes.
clean - Deletes the build directory.
jar - Assembles a jar archive containing the main classes.
testClasses - Assembles test classes.

Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.

Documentation tasks
-------------------
javadoc - Generates Javadoc API documentation for the main source code.

Help tasks
----------
buildEnvironment - Displays all buildscript dependencies declared in root project 'gs-gradle'.
components - Displays the components produced by root project 'gs-gradle'. [incubating]
dependencies - Displays all dependencies declared in root project 'gs-gradle'.
dependencyInsight - Displays the insight into a specific dependency in root project 'gs-gradle'.
dependentComponents - Displays the dependent components of components in root project 'gs-gradle'. [incubating] help - Displays a help message.
model - Displays the configuration model of root project 'gs-gradle'. [incubating]
projects - Displays the sub-projects of root project 'gs-gradle'.
properties - Displays the properties of root project 'gs-gradle'.
tasks - Displays the tasks runnable from root project 'gs-gradle'.

Verification tasks
------------------
check - Runs all checks.
test - Runs the unit tests.

Rules
-----
Pattern: clean<TaskName>: Cleans the output files of a task.
Pattern: build<ConfigurationName>: Assembles the artifacts of a configuration.
Pattern: upload<ConfigurationName>: Assembles and uploads the artifacts belonging to a configuration.

To see all tasks and more detail, run gradle tasks --all

To see more detail about a task, run gradle help --task <task>


BUILD SUCCESSFUL in 5s
1 actionable task: 1 executed
```

Build tasks、Documentation tasks、Verification tasksが追加されています。
