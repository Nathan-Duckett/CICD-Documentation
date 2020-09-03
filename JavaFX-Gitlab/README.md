# JavaFX - Gitlab

This document serves for historical purposes to outline the CI/CD system which was developed to support the development of the Mission Control software created for a group project in ENGR301 and ENGR302. This will provide the steps to replicate the process and what was required to get the features we supported.

This CI/CD pipeline was fully written by me with some helpful ideas provided by a range of sources but the execution ran on my own custom servers.

## Outline of the CI/CD features

The CI/CD pipeline has been refined over time to support many features.

The main features provided are:

- Building the application
- Performing automated javadoc generation and documentation spell checking
- Performing static analysis to ensure consistent code style and no known bugs
- Executing a unit test suite and support for integration tests using GUI functionality
- Deploying documentation information for test coverage and javadocs
- Packaging the application for release

All of these features provide us with the ensurity that the code which makes it through to master is working as expected.
This code is run using a Directed Acyclic Graph which ensures that the code is not blocked by previous stages and can run through each different check while still preventing a user from merging if there is an issue.

## Setup of the CI/CD pipeline

In this folder there is the `.gitlab-ci.yml` file which contains the configuration for the Gitlab CI/CD for this project. This requires three types of runners to execute this code outlined below. By default each of these runners ran on a specific host using shell capabilities.

### Shell runner
This runner was a default Gitlab runner. This runner required the support for Java 11, Maven, NodeJS and Git. These tasks could also be run on the custom GUI runner but was put on a separate runner to ensure parallel processing can occur.

### GUI runner
This runner enabled our support to run GUI testing and package our application. This runner was custom built using a bash script to automatically configure and run the services.
It is based on the Kubuntu 20.04 OS running in a KVM machine allocated 4 cores and 8gb RAM. This is required to have a GUI to support the integration tests which will take over as a user, and use the display, and mouse/keyboard to perform testing.

To configure this machine use the [script at this link](https://github.com/Nathan-Duckett/Build-Scripts/blob/master/server-builds/build-CI-linux-runner.sh).

This utilizes 4 arguments within the script execution to configure the machine.
- The URL for the CI runner
- The token for the CI runner
- A Gitlab PAT for additional features
- A Gitlab Project ID for additional features

These additional features include 3 different utilities, 2 of which I developed and another was developed by another team member.

I have created a pair programming matrix utility which will scan your provided project for pairs assigned to tickets and track them in a markdown table which will automatically be uploaded using the Wiki Updater.

The other team member has created a tool which will add up all of the time spent by each individual person and will then be uploaded using the Wiki Updater. This breaks down by each sprint and shows the time contribution by each member which was required for course marking.

The final utility as mentioned before was the Wiki Updater which would write the contents passed in from STDOUT into a Wiki page on Gitlab.


### Windows Runner
The windows runner is a basic Windows 10 machine with support for Java 11, Maven and Git. This is used to perform the same test suite on windows (excluding GUI tests) to ensure that the code is cross-platform compatible with linux.

This has a limitation to not allow GUI tests as any CI job is run as *user zero* which will prevent the code being allowed to run with GUI components breaking the tests.