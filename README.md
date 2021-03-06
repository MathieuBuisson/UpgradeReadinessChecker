# DeploymentReadinessChecker  

[![Build status](https://ci.appveyor.com/api/projects/status/w6dha583tp0gkiio/branch/master?svg=true)](https://ci.appveyor.com/project/MathieuBuisson/deploymentreadinesschecker/branch/master) [![Coverage Status](https://coveralls.io/repos/github/MathieuBuisson/DeploymentReadinessChecker/badge.svg?branch=master)](https://coveralls.io/github/MathieuBuisson/DeploymentReadinessChecker?branch=master) [![Documentation Status](https://readthedocs.org/projects/deploymentreadinesschecker/badge/?version=latest)](http://deploymentreadinesschecker.readthedocs.io/en/latest/?badge=latest)  


Pester-based tool to validate that a (potentially large) number of machines meet the prerequisites for a software deployment/upgrade.  
It generates a NUnit-style test result file for each target machine and creates a visual, dynamic HTLM report encompassing data from all the test results, for example :  


![Image of HTML Report](https://i0.wp.com/theshellnut.com/wp-content/uploads/2016/08/Overview-Report.png)


This module contains 1 cmdlet : **Test-DeploymentReadiness**.  
It requires PowerShell version 4 (or later).


## Test-DeploymentReadiness :


Validates that one or more computers meet the prerequisites for a software deployment/upgrade.
It generates a NUnit-style test result file for each computer and creates a visual, dynamic HTLM report 
encompassing data from all the test results.  

The list of computers to check is specified via the ComputerName parameter.

The deployment or upgrade prerequisites are specified in a Pester-based validation script located in the 
sub-directory \ReadinessValidationScript.  
Test-DeploymentReadiness can only invoke one validation script at a time, even if there are multiple scripts named 
*.Tests.ps1 in the ReadinessValidationScript sub-directory.


### Parameters :


**ComputerName :** To specify one or more computers against which the prerequisites checks will be performed.

If the validation script has a ComputerName parameter, the function passes one computer at a time to its ComputerName parameter, via the Script parameter of Invoke-Pester.  


**Credential :** To specify the credentials to connect remotely to the target computers.

If the validation script has a Credential parameter, the function passes the value of its own Credential parameter to the validation script, via the Script parameter of Invoke-Pester.  


**OutputPath :** To specify in which directory the output test results files and the summary report should be located.
If the directory doesn't exist, it will be created.
If not specified, the defaut output path is the current directory.  
If not specified, it defaults to $($pwd.ProviderPath) .


**TestParameters :** If the test script used to validate the prerequisites take parameters, their names and values can be specified as a hashtable via this parameter.
Then, the function will pass these into the Script parameter of Invoke-Pester, when calling the validation script.  
To see the format of the hashtable for this parameter, please refer to the examples by running : Get-Help Test-DeploymentReadiness -Examples  


**Tag :** If the Pester validation script contains Describe blocks with tags, only the tests in Describe blocks with the specified Tag parameter value(s) are run.
Wildcard characters and Tag values that include spaces or whitespace characters are not supported.  


**ExcludeTag :** If the Pester validation script contains Describe blocks with tags, tests in Describe blocks with the specified Tag parameter values are omitted.
Wildcard characters and Tag values that include spaces or whitespace characters are not supported.

Just like the ExcludeTag parameter of Invoke-Pester, when you specify multiple ExcludeTag values, this omits tests that have any of the listed tags (it ORs the tags).  


**ValidationScript :** This is a dynamic parameter which is made available (and mandatory) whenever there is more than one test script in the sub-folder \ReadinessValidationScript\.  
This is because Test-DeploymentReadiness can only invoke one validation script at a time, so if there is more than one, the user has to specify which one.

This parameter expects the name (not the full path) of one of the test file present in <ModuleFolder>\ReadinessValidationScript\.  
If no value is specified when there is more than one validation script available, the error message will tell the user what are the possible values.  
(See the last example in the Examples section of the help.)
