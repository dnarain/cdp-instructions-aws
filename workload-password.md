# Set Workload Password

CDP uses SSO, so once you log into the console, you don't need your password to 
access any of the services. However, there are some services (e.g. ssh) that aren't
SSO Integrated.  For these, you need to use your Workload Password.

Before we get to Workload Password, we need to understand what a Workload Username
is.  When you log into the system, CDP creates a "workload user", which is a userid
that you will use to access 

When you use your my.cloudera.com credentials to log in, you are actually using
the Cloudera SSO system and your workload username will be prefixed with csso_

