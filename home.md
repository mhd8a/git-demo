Welcome to the git-demo wiki[Click hto for this link](https://github.com/mhd8a/git-demo/wiki/_new)



This repository is destined to provide GitHub Actions to build, package and deploy any ROS, CMake or Python projects in the BDC or Social Coding, provided that the credentials are available.

To see the list of all build jobs, check the [actions](https://github.boschdevcloud.com/bios-robotics/bosch_buildfarm/actions) page.

Per default, the crendential used by the jobs in this repository has access to all BIOS projects within the BDC.

The packages deployed from this package will be uploaded to the bios-rose Artifactory repository for Debian packages and the rosdep keys will automatically be updated in the [bosch_rosdistro](https://github.boschdevcloud.com/bios-robotics/bosch_rosdistro) index lists. Check this [page](https://github.boschdevcloud.com/bios-robotics/bosch_rosdistro/wiki/rosdep-lists) to see a list of all custom packages with custom rosdep keys available.

Important: To use the custom rosdep keys locally, you need to install bosch_rosdistro. Follow [these installation instructions](https://github.boschdevcloud.com/bios-robotics/bosch_rosdistro#building-installing-and-testing) for more information.

For more details on how to setup the bios-robotics Artifactory, visit this [site](https://artifactory.boschdevcloud.com/ui/repos/tree/General/bios-robotics-debian-local).

Important: To have your ROS or CMake project packaged and uploaded to Artifactory, make sure all of its declared dependencies can also be installed via apt, otherwise the packaging step will fail.

All builds are made using compass_it actions. For more details on how to set up your repository, check the [compass_it repository](https://github.boschdevcloud.com/bios-rose/compass_it).

Overview
In the picture below there is a brief overview of the information flow necessary to automate the packaging of your repository using the bosch_buildfarm and the destination of those packages.

For more details and instructions on the setup of automate the packaging process, check the following links:

[(CMake) How to create an automated release build job for a project](https://github.boschdevcloud.com/bios-robotics/bosch_buildfarm_provisioning/wiki/(CMake)-How-to-create-an-automated-release-build-job-for-a-project)
[(ROS) How to create an automated release build job for a project](https://github.boschdevcloud.com/bios-robotics/bosch_buildfarm_provisioning/wiki/(ROS)-How-to-create-an-automated-release-build-job-for-a-project)
