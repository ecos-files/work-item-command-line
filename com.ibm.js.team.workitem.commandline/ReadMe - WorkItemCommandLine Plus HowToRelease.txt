To Build the Work Item Command Line "Plus" from Eclipse:

1) Select the client version by changing the Build Path to include from lib600 or lib6061, etc.  The 6061 library does not work on 606 and vice versa.
   1a) From the Package Explorer select the package and select Configure Build Path -> Libraries
   1b) Select all jars above "Libraries". Remove.
   1c) Add Jars... select all from for example lib6061. Just select jars. Add.
   Note: opencsv-3.7.jar and commons-lang3-3.10 are in both lib606 and lib6061.
   TODO: Clean up version selection process and minimize jars in deployment.
2) Compile.
3) Create a Launch Configuration that includes the work item command line and dependent libraries.
4) Select the package com.ibm.js.team.workitem.commandline
5) File -> Export -> Java -> Runnable Jar
6) Select the launch configuration you chose
7) Select the destination file --  e.g. wic2020-0512-6061.jar -- best to include the target version in the file name
8) Select "package required libraries" in the jar file.
9) Export

The exported jar file (~115mb). This can be made smaller by removing the unneeded libraries before or after exporting.

The command can be executed using the java -jar wic2020-0512-6061.jar and parameters
> java -jar wic2020-0512-6061.jar -findidconflicts /debug repository="https://myserver:9443/ccm" passwordFile=pw.txt

password file format is "serverFragment userid password" and is used so the password is not in the console history or screenshots...
jazzdev myuserid mypassword

The first word server fragment is looked up in the url and if it matches that userid/password is used:  (repositoryUrl.indexOf(serverFragment) != -1)

Note: The log4j files have been updated to use log4j-api-2.17.1.jar log4j-core-2.17.1.jar and related jars.
 