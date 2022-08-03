Work Item Command Line "Plus" Enhancements
V4.1+20220422

This package extends the open source RTC Work Item Command Line V4.1 found at:  
https://github.com/jazz-community/work-item-command-line

The Work Item Command Line is a stand-alone Java application used to perform special functions against the repository.

CAUTION: The Command Line can be used to make irreversible and destructive changes to your repository data. Back up is recommended before making changes.

Choose either the 6.0.6 6.0.6.1 or 7.0.2 jar files. Different client libraries are used for each version of RTC/EWM.

See the included Readme - WorkItemCommandLine Version 4.1.md file for the overview, usage, and commands available in the open source command line. 
Later versions of the command line may be available on the github location.  The following additional commands are added for 
the Work Item Command Line Plus:

Find if there are attribute Id's that conflict with enumeration literal id's
-findidconflicts
  /debug
  /trace
  repository="https:..."
  user=ADMIN 
  password=ADMIN
  passwordFile=pw.txt
  projectArea="JKE Banking"
  /showProjects
  /showCatalog
  /verbose
  

Find any string in the project area states  
-findinprojectareas 
  /debug
  /trace
  repository="https:..."
  user=ADMIN 
  password=ADMIN
  passwordFile=pw.txt
  projectArea="JKE Banking"
  search="type=\"task\""

Check that for each work item link there is a backlink
-validateoslclinks 
   /trace 
   /debug 
   repository="http:..." 
   passwordFile=pw.txt
   projectArea="JKE Banking"
   

Validate work item states and write them to files if exportFolder provided
-validateworkitems
   /trace 
   /debug 
   repository="http:..." 
   user=ADMIN 
   password=ADMIN
   passwordFile=pw.txt
   projectArea="JKE Banking"
   search="myattributeid"
   exportFolder="./tempExport"
   query="Some Work Items" 
  

The jar file is executed from the java command line. In the examples below the jar file is renamed wicplus.jar. 
Choose either the 6.0.6 or 6.0.6.1 or 7.0.2 jar file. Examples:

java -jar wic6061plus.jar -findidconflicts /debug repository="https://mytestserver1:9443/ccm" passwordFile=pw.txt 

For the samples below the file is renamed to wicplus.jar. 

The /trace and /debug switches can be used to show details of the execution.

Other features of the work item command line are included but not tested. 
_________
PASSWORD FILE

The command can use a password file to give user id and password to the remote servers. 
passwordFile="resources/pw.txt" 
The content of the password file is a subset of the server url and the user id and password... pw.txt... 

mytestserver1 ADMIN ADMIN 
productionP1 clmadmin som3thingdiff1cult597 

Only enough of the url to match is needed for the first word in the file. So "mytestserver1" would match "https://mytestserver1:9443/ccm". 
This allows the command to be used without adding the passwords to the command history.
________
REPODEBUG ENABLEMENT

IMPORTANT: The user must have JAZZADMIN rights and the Repodebug Advanced Property must be enabled to run some tools.
The validateworkitems uses the Repodebug service.  

If enabling the Repodebug Advanced Property does not work completely, additional steps may be needed:

RepoDebug must be enabled in the /ccm or /jazz Advanced Properties, 
and the user specified in the tool must have JazzAdmin privilege. The java options may also need to be changed and the server restarted.
set JAVA_OPTS=%JAVA_OPTS% -Dcom.ibm.team.repository.debug.users=JazzAdmins
set JAVA_OPTS=%JAVA_OPTS% -Dcom.ibm.team.repository.debug.accessServiceEnabled=true
See: https://www.ibm.com/support/pages/enabling-repodebug-service-jts-and-rtc-was-liberty-application-server

The repodebug -> repository -> rawitemstates service is used by the command line, so the privilege is the same for the command line as it is for the web page.
 https://m4:9443/jazz/repodebug/repository/rawItemState
 

ref: https://www.ibm.com/support/pages/enabling-repodebug-service-jts-and-rtc-was-liberty-application-server

Note: Do not use the REPODEBUG web interface to edit states that contain HTML content or HTML encoding errors may occur.

________
COMMON PARAMETERS
Most commands use the following parameters. 

  /debug
    Show extra logging which helps with debug analysis
  
  /trace
    Show very detailed logging and log any read states
  
  exportFolder="./workitemexport"
    Folder to export work item states
    
  repository="https:myserver:9443/jazz"
    The URL of the repository
  
  query="Some Work Items" 
    Work Item query to identify which items to process in the command.
    
  querysource="My Team"
    If the query is shared, this parameter shows the team area where it is shared
  
  user=ADMIN 
  password=ADMIN
    User and password
  
  passwordFile=pw.txt
    Password file. 
    The file is required for validateoslclinks OSLC and REST transaction to resolve the passwords for the referenced server
  
  projectArea="JKE Banking"
    Project area name.
    Some users have problems with underscore(_) and dash(-) in project area names. If so < can be substitued for _ and > can be substituted for -
      projectArea="JKE-Banking_with_style" becomes projectArea="JKE>Banking<with<style" 
   
  search="some string"
  search="type=\"task\""
    The string to search for in project areas and states.
    
    /statesOnly
      Only check states, not links
    
    /linksOnly
      Only check links, not states
      
    /local
      Only check local links, not OSLC links

________
COMMANDS

=============================
Find Id Conflicts

-findidconflicts
  /debug
  /trace
  repository="https:..."
  user=ADMIN 
  password=ADMIN
  passwordFile=pw.txt
  projectArea="JKE Banking"
  
Usage:

-findidconflicts /trace /debug repository="https://com.mycompany.myserver2:9443/jazz" passwordFile="resources/pw.txt" 

This command will read all project areas configuration.xml files in the repository and identify if any attributeTypeId= is the same as a reserved id. 
If an customization includes an attributeTypeId= a reserved value, the project area must be edited and references manually changed. For example, in Eclipse the configuration.xml would be opened in the project area editor, the ADMIN user would search for where "tags" is specified (e.g. "attributeTypeId="tags"), and replace these with a unique id such as "com.mycompany.myapp.tags" both where tags is defined and where it is referenced. The attributeTypeId's are only stored in the configuration.xml. Using reserved id's can cause problems indexing the repository. 

Usage:

An example command: 
java -jar wicplus.jar -findidconflicts /debug repository="https://m4:9443/jazz" passwordFile="resources/pw.txt" 

Options include:
/verbose 
/showprojects - Log the xml of the project configuration
/showcatalog - Log the xml of the catalog of projects

Sample output:
In this case a project area was found that included an enumeration with the attributeTypeId="tags" 

Starting Team Platform ... 
Executing command: findidconflicts 

0 [main] TRACE com.ibm.js.team.workitem.commandline.commands.FindEnumerationIdConflictsCommand - Loading repository catalog: https://m4:9443/jazz/oslc/workitems/catalog 
287 [main] TRACE com.ibm.js.team.workitem.commandline.commands.FindEnumerationIdConflictsCommand - Searching "Simple Scrum [ _KFyt4PdpEemV0ZSiCrAwpw ] https://m4:9443/jazz/oslc/contexts/_KFyt4PdpEemV0ZSiCrAwpw/workitems/services.xml 
287 [main] TRACE com.ibm.js.team.workitem.commandline.commands.FindEnumerationIdConflictsCommand - Loading project history: https://m4:9443/jazz/process/project-areas/_KFyt4PdpEemV0ZSiCrAwpw/history?pageSize=1 
337 [main] TRACE com.ibm.js.team.workitem.commandline.commands.FindEnumerationIdConflictsCommand - Loading process area configuration xml: https://m4:9443/jazz/process/project-areas/_KFyt4PdpEemV0ZSiCrAwpw/history-detail/_KFyt4PdpEemV0ZSiCrAwpw/_UxZjwlhHEeqGN4o1O4lEDw/process-specification 
410 [main] WARN com.ibm.js.team.workitem.commandline.commands.FindEnumerationIdConflictsCommand - 
*** Reserved attributeTypeId="tags" found in project: Simple Scrum [ _KFyt4PdpEemV0ZSiCrAwpw ] https://m4:9443/jazz/process/project-areas/_KFyt4PdpEemV0ZSiCrAwpw 
416 [main] TRACE com.ibm.js.team.workitem.commandline.commands.FindEnumerationIdConflictsCommand - Searching "Formal Project Management Project [ _xOGxcNDuEemsP4VAIIMthA ] https://m4:9443/jazz/oslc/contexts/_xOGxcNDuEemsP4VAIIMthA/workitems/services.xml 
416 [main] TRACE com.ibm.js.team.workitem.commandline.commands.FindEnumerationIdConflictsCommand - Loading project history: https://m4:9443/jazz/process/project-areas/_xOGxcNDuEemsP4VAIIMthA/history?pageSize=1 
450 [main] TRACE com.ibm.js.team.workitem.commandline.commands.FindEnumerationIdConflictsCommand - OK. No reserved attributeTypeId's found found in project: Formal Project Management Project [ _xOGxcNDuEemsP4VAIIMthA ] https://m4:9443/jazz/process/project-areas/_xOGxcNDuEemsP4VAIIMthA 
450 [main] TRACE com.ibm.js.team.workitem.commandline.commands.FindEnumerationIdConflictsCommand - Searching "Jazz Project [ _uEEeYNDuEemsP4VAIIMthA ] https://m4:9443/jazz/oslc/contexts/_uEEeYNDuEemsP4VAIIMthA/workitems/services.xml 
451 [main] TRACE com.ibm.js.team.workitem.commandline.commands.FindEnumerationIdConflictsCommand - Loading project history: https://m4:9443/jazz/process/project-areas/_uEEeYNDuEemsP4VAIIMthA/history?pageSize=1 
482 [main] TRACE com.ibm.js.team.workitem.commandline.commands.FindEnumerationIdConflictsCommand - OK. No reserved attributeTypeId's found found in project: Jazz Project [ _uEEeYNDuEemsP4VAIIMthA ] https://m4:9443/jazz/process/project-areas/_uEEeYNDuEemsP4VAIIMthA 


=============================
Find String In Project Areas

The findinprojectareas command line is used to search project areas in a repository. The projects are not modified. After finding the desired project area the project area would be edited using Eclipse or web UI. 

-findinprojectareas /trace /debug repository="https://com.mycompany.myserver1:9443/jazz" passwordFile="resources/pw.txt" search="search="type=\"mytype\""" 
This command reads all project areas configuration.xml files in the repository and identifies in which project the search string exists. Special characters must be escaped. 


 
=============================
Validate OSLC Links 

-validateoslclinks 
   /trace 
   /debug 
   repository="http:..." 
   passwordFile=pw.txt
   projectArea="JKE Banking"
   query="Some Work Items"
   querysource="My Team, Their Team" 

Check that for each work item link there is a backlink. This command verifies that each work item in a query has a back link from the linked resource. It extends the work item command line to read remote items when they do not have global configuration enabled. 

For shared queries use the querysource= parameter, passing a list of process areas; e.g. querysource="Jazz Project,Formal Project"


Usage: 

The command uses a password file to give user id and password to the remote servers. 
passwordFile="resources/pw.txt" 

The content of the password file is a subset of the server url and the user id and password... pw.txt... 
myserver1 ADMIN ADMIN 
myserver2 clmadmin som3thingdiff1cult597 

An example command: 
-validateoslclinks /trace /debug repository="https://myserver.mycompany.com:9443/jazz" passwordFile="resources/pw.txt" projectArea="Jazz Project" query="Items to Verify" 

This will use the trace and debug output for the repository and project area, running the query accessible by the userid of the server specified in the password file. When an item in the query links to another item the password file is used to look up the remote server. 

The remote links for each work item are tested... the types of links include: 

WorkItemLinkTypes.AFFECTED_BY_DEFECT /* "com.ibm.team.workitem.linktype.cm.affectedByDefect" */ 
"http://open-services.net/ns/cm#affectedByDefect 
WorkItemLinkTypes.AFFECTS_PLAN_ITEM /* "com.ibm.team.workitem.linktype.cm.affectsPlanItem" */ 
"http://open-services.net/ns/cm#affectsPlanItem 
WorkItemLinkTypes.CONTRIBUTES_TO_WORK_ITEM /* "com.ibm.team.workitem.linktype.trackedworkitem" */ 
"http://open-services.net/ns/cm#trackedWorkItem 
WorkItemLinkTypes.RELATED_WORK_ITEM /* "com.ibm.team.workitem.linktype.relatedworkitem.related" */ 
"http://open-services.net/ns/cm#relatedChangeRequest 
WorkItemLinkTypes.TRACKS_WORK_ITEM /* "com.ibm.team.workitem.linktype.tracksworkitem" */ 
"http://open-services.net/ns/cm#tracksWorkItem 

WorkItemLinkTypes.IMPLEMENTS_REQUIREMENT /* "com.ibm.team.workitem.linktype.implementsRequirement" */ 
"http://open-services.net/ns/cm#implementsRequirement 
WorkItemLinkTypes.RELATED_REQUIREMENT /* "com.ibm.team.workitem.linktype.rm.relatedRequirement" */ 
"http://open-services.net/ns/cm#affectsRequirement 

WorkItemLinkTypes.BLOCKS_EXECUTION_RECORD /** "com.ibm.team.workitem.linktype.blocksTestExecutionRecord" */ 
"http://open-services.net/ns/cm#blocksTestExecutionRecord 
WorkItemLinkTypes.AFFECTS_EXECUTION_RESULT /* "com.ibm.team.workitem.linktype.affectsExecutionResult" */ 
"http://open-services.net/ns/cm#affectsTestResult 
WorkItemLinkTypes.RELATED_TEST_CASE /* "com.ibm.team.workitem.linktype.qm.relatedTestCase" */ 
"http://open-services.net/ns/cm#:relatedTestCase 
WorkItemLinkTypes.RELATED_TEST_PLAN /* "com.ibm.team.workitem.linktype.qm.relatedTestPlan" */ 
"http://open-services.net/ns/cm#relatedTestPlan 
WorkItemLinkTypes.RELATED_TEST_SCRIPT /* "com.ibm.team.workitem.linktype.qm.relatedTestScript" */ 
"http://open-services.net/ns/cm#relatedChangeRequest 
WorkItemLinkTypes.TESTED_BY_TEST_CASE /* "com.ibm.team.workitem.linktype.testedByTestCase" */ 
"http://open-services.net/ns/cm#elatedTestScript 
WorkItemLinkTypes.RELATED_EXECUTION_RECORD 
"http://open-services.net/ns/cm#relatedTestExecutionRecord 

WorkItemLinkTypes.CONTRIBUTES_TO_WORK_ITEM /* "com.ibm.team.workitem.linktype.trackedworkitem" */,
"http://open-services.net/ns/cm#trackedWorkItem"
WorkItemLinkTypes.TRACKS_WORK_ITEM /* "com.ibm.team.workitem.linktype.tracksworkitem" */,
"http://open-services.net/ns/cm#tracksWorkItem"


=============================
-validateworkitems
    /trace 
   /debug 
   repository="http:..." 
   user=ADMIN 
   password=ADMIN
   passwordFile=pw.txt
   projectArea="JKE Banking"
   search="myattributeid"
   exportFolder="./tempExport"
   query="Some Work Items"
   querysource="My Team" 
   /local 
   /linksOnly
   /statesOnly
   
Validate work item states and write them to files if exportFolder parameter is provided. The Validate Work Items -validateworkitems is used to both validate and export the work item states stored in the repository. 

Usage:

-validateworkitems repository="https://com.mycompany.myserver:9443/jazz" passwordFile="resources/pw.txt"  projectArea="Jazz Project" query="Items to Verify" exportFolder="workitemexport"

-validateworkitems repository="https://com.mycompany.myserver:9443/jazz" user="celsius" password="passwordForCelsius" projectArea="Jazz Project" id="352" exportFolder="workitemexport"

The user id and password can be specified or put in a password file, as describe above.

For shared queries use the querysource= parameter, passing a list of process areas; e.g. querysource="Jazz Project,Formal Project"


The validate work items command does the following:

1) For the specified id or each item in the query...
2) Log in the repository and read all states of the item
3) For each state xml search the xml for invalid characters and parse the XML.
4) Using the repository API load a work item
5) For each referenced custom attribute, project area, and planned for iteration, load the reference state and validate that
6) If the exportFolder parameter is provided, create a file for each work item state, project area, and referenced attributes. This may be many large files.
7) For each linked work item, load the target work item and check that it exists. Those states are not checked. The following types of links are checked:
   parent, child, blocks, depends on, copied from, copied, duplicate, duplicate of, related
   For tracks, contributes to, and similar OSLC links, see the validateoslclinks command
8) If /local is specified, then the repodebug is not used and the states are not loaded. Other validation occurs.
9) If /linksOnly is specified, then only the links and not the states is checked
10) If /statesOnly is specified, then only the states and not the links are specified.
11) The querysource parameter specifies which team area to check for the query if it is shared. If not provided the personal query is used. The user must have access to the query.


========================
NOTICE 

The Work Item Command Line is distributed (execution only) via the MIT license.
No support is provided or implied.

The depoloyed package includes client libraries including log4j V2.17.1 or later. Other libraries are also included.
