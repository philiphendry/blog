---
author: philiphendry
comments: true
date: 2008-03-31 19:36:40+00:00
layout: page
slug: microsoft-powershell
title: Microsoft PowerShell
wordpress_id: 65
---

## Tools

 

  
  * [PowerGui](http://powergui.org/index.jspa) for editing scripts and includes intellisense. 
 

#### 

 

## Overview

 

The Windows PowerShell is an re-write and advancement of previous command line shells and represents a significant improvement. Instead of treating input and output to commands as text, PowerShell is built on top of .Net and treats everything as an object providing must greater flexibility to manipulate data whilst maintaining higher type safety and reducing the overall code size.

 

## Command-Lets

 

Commands that are built-in to and executed within the shell are called _Command-Lets_ and use a standard case-insensitive naming convention in the form <verb>-_<name>_. A parameter-less _get-command_ call will list all the current cmdlets registered with the shell but parameters can be used to also lists aliases, functions and executables available to the shell (run _get-command *_ to see all commands.)

 

If you're wondering where all the commands are for a particular command then the following command will list all the commands with the same noun :

 

_get-command -Noun <noun>_

 

### Parameters

 

Cmdlet parameters are always specified with a leading '-' and neither slash is allowed Parameter names do not have to be typed in full to cmdlets - only enough has to be specified that uniquely identifies the parameter name. Also some parameter names are optional and do not need to be provided with their corresponding value - however, the value must appear in the correct position according to its syntax.

 

There are a number of common parameters that all cmdlets support (WhatIf, Confirm, Verbose, Debug, Warn, ErrorAction, ErrorVariable, OutVariable and OutBuffer.) For a detailed description:

 

_get-help about_commonparameters_

 

### Getting Help

 

Help can be found for all command-lets by typing:

 

_get-help <cmdlet-name> - detailed_

 

The _-detailed_ option can be dropped for brief help or replaced with _-full_ for completed help or _-examples_ for just the examples, or _-parameter <name>_ for help on a particular cmdlet parameter.

 

Wildcards can be liberally used when hunting for help. For example the following returns a topic list for all cmdlets starting with 'get':

 

_get-help get*_

 

### .Net Integration

 

PowerShell integrates heavily with .Net and, in fact, all cmdlets are .Net object and can be demonstrated by piping a cmdlet into the _get-member_ cmdlet which lists the properties and methods available. It also indicates the .Net type that the objects are instances of and can therefore be searched for in MSDN :

 

_get-service | get-member_

 

To display all the current property values of an object instance (or replace the * with a comma-separated list of property names for specific properties) :

 

_get-service schedule | format-list -property *_

 

To access any of these properties or methods isolate the object instance and call using the standard dot notation:

 

_(get-service schedule).stop()_

 

### Paths

 

Windows applications can be executed from the shell if they exist in the current path. To see the current path :

 

_$env:path_

 

or to add to the path (for this session only) :

 

$env:path += ";mydirectory"

 

## Pipelining

 

Examples of pipelining using the '|' operator have already been shown. It is important to remember, however, that it's objects that are passed through the pipes instead of text. The text displayed in the console window is only resolved once the pipeline has been completed.

 

### Filtering Pipelines

 

Objects can be removed from a pipeline by using the _Where-Object_ cmdlet which performs a test on each object and only allows those that pass to continue on the pipeline. The available parameters are:

 

  
  * _FilterScript_ : a script block that contain one or more PowerShell commands and is surrounded by curly bracketes {} and evaluates to true or false. (note : the current object is represented in the scrip block as _$__) 
 

Typically these script blocks will use comparison operators that include :

 

  
  * _-eq : _equal to 
   
  * _-ne_ : not equal to 
   
  * _-lt_ : less than 
   
  * _-gt_ : greater than 
   
  * _-le_ : less than or equal 
   
  * _-ge_ : greater than or equal 
   
  * _-like_ : wildcard comparison 
   
  * _-notlike_ : not like the wildcard comparison 
   
  * _-contains_
   
  * _-notcontains_
 

and multiple comparisons can be joined with logical operators :

 

  
  * _-and_
   
  * _-or_
   
  * _-not_
   
  * _!_ : same as _-not_
 

### Processing Pipelines

 

The _ForEach-Object_ cmdlet can be used to execute a script block against each object in the pipeline (or specified with the _inputObject_ parameter.) The script block is specified using the _process_ parameter and the current object in the pipeline can be referenced as _$_._

 

### Creating New Objects

 

New objects can be created from the pipeline using the _Select-Object_ cmdlet and specifying the properties to create from the pipeline objects by specifying a comma-separated list of property names to the _Property_ parameter. The pipeline will now contain new objects of type _PSCustomObject_. For example :

 
    
    Get-WmiObject -Class Win32_LogicalDisk | Select-Object -Property Name,FreeSpace





The properties of these custom objects can now be manipulated during pipelining.





### Sorting the Pipeline





Sorting is simple with the _Sort-Object_ cmdlet :




    
    Get-ChildItem | Sort-Object -Property LastWriteTime -Descending


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)



## Variables





Variables names are defined with a leading $ and can contain alphanumeric characters or underscores. A variable is created by simply typing the name :




    
    $temp





but can also be initialised at the same time :




    
    $temp = Get-ChildItem


[](http://11011.net/software/vspaste)



Typing _$temp_ will now list its contents which is the object list that _Get-ChildItem_ would have returned if typed.





There are a number of cmdlets to manipulate variables :






  
  * _Get-Variable_ : if a parameter is not specified all current defined variables will be printed 


  
  * _New-Variable_


  
  * _Clear-Variable_ : deletes the contents of the variable setting it to NULL 


  
  * _Remove-Variable _: deletes the contents and deallocates the storage for the variable 


  
  * _Set-Variable_





Environment variables defined in Windows are accessible by prefixing the variable name with _env:_. For example :




    
    $env:windir


[](http://11011.net/software/vspaste)




[](http://11011.net/software/vspaste)



## Scripts





Powershell commands can be grouped into a text file with the extension _ps1_ then run from the console at which point the extension is optional. However, a security features requires that paths to scripts are always fully qualified and therefore must, at the minimum, reference the current directory.





## Profiles





To retain changes to aliases, functions and variables which would otherwise be lost at the end of a session, commands can be added to a profile that will maintain them between sessions. The execution policy must permit you to load configuration files otherwise PowerShell will display and error message.





There can be four profiles which are loaded in the order shown below :






  
  * _%windir%\System32\WindwsPowerShell\v1.0\profile.ps1_ 

      
Applies to all users and shells 


  
  * _%windir%\system32\WindowsPowerShell\v1.0\Microsoft.PowerShell_profile.ps1_ 

      
Applies to all users but only the PowerShell shell 


  
  * _%UserProfile%\My Documents\WindowsPowerShell\profile.ps1_ 

      
Applies only to the current user but all shells 


  
  * _%UserProfile%\My Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1 
        
_Applies only to the current user and the PowerShell shell 





The last profile is the one that is typically modified and it's path exists in the variable _$profile_. The file does not exist by default but you can test for its existence using _test-path $profile_, create it using _new-item -path $profile -itemtype file -force_ and edit it using _notepad $profile._





## PowerShell Drives





There are a number of drives available in the PowerShell as well as the usual C: and D: type drives :






  
  * _HKLM:_ - providing access to the HKEY_LOCAL_MACHINE registry hive. 


  
  * _HKCU:_ - providing access to the HKEY_CURRENT_USER registry hive. 


  
  * _Cert: _- accesses the digital signature certificate store. 


  
  * _Function:_ - access the currently registered PowerShell functions. 





The complete list of drives can be listed using the _get-psdrive_ cmdlet. Custom drives can be added using the _new-psrive_ cmdlet.





The drive functionality is enabled through PowerShell providers written in .Net. There are a couple of applicable cmdlets :






  
  * _get-help about_provider -_ information regarding providors 


  
  * _get-psprovider_ - displays a list of providers 





### Adding a new PowerShell Drive





New PowerShell drives can be added using the _New-PSDrive _cmdlet:





_new-psdrive -Name <Name> -PSProvider FileSystem -Root "C:\Temp"_





This type of command can make particular tasks much simpler by reducing the overall complexity of paths needing to be provided in a script. The drive will not exist beyond a session although, if needed, it can be manually removed using:





_remove-psdrive -Name <Name>_





## Working With Items





Because PowerShell tries to abstract itself away from file system concepts to a broader level that encompasses far more, the terms drive, file and folder are not usually seen. Instead the term 'Item' represents drives, files and folders for FileSystems in PowerShell as well as Keys in the Registry or Certificates in the Certificate Store.





Therefore, the command to fetch a collection of items from a particular location is :





_Get-ChildItem_





There are a number of properties that may be commonly used :






  
  * Name : only lists the name of each item in the collection 


  
  * Force : lists all items which would otherwise be hidden 


  
  * Path : specifies a location to select the items from instead of the current location 





Other Item commands include :






  
  * Clear-Item 


  
  * Copy-Item : (note : specify the Recurse parameter to copy the child items as well as the item itself - for folders, this will also copy the folder contents) 


  
  * Get-Item 


  
  * Invoke-Item : (note : performs the default action on the item determined by the default application handler in the registry. _Invoke-Item c:\temp_ will open a Explorer window for the C:\temp folder for example 


  
  * Move-Item 


  
  * New-Item : (note : specify the ItemType parameter to differentiate between Directories and Files. The Force parameter can be used to force an overwrite) 


  
  * Remove-Item : (note : specify the Recurse parameter to prevent having to confirm the delete if the item contains children) 


  
  * Rename-Item : (note : will raise an error if a move is attempted) 


  
  * Set-Item 





### Wildcards





A lot of cmdlets accept wildcards to match properties and they all use the same matching syntax which includes :






  
  * * : matches zero or more characters 


  
  * ? : matches exactly one character 


  
  * [<character list>] : creates a character set to a single character. Combined with a trailing *, this will match one or more occurrences of characters in the set. 


  
  * Exclude : a property that can be used to specify a wildcard expression that will used to filter out matching items. 





Note : when specifying a recursive search using a wildcard, specify the wildcard in the _Include_ property instead of in the _Path _since otherwise the _Path_ wildcard will immediately filter to only the items in the first path removing all the sub-folder entries.





For example use :





_Get-ChildItem -Path . -Include *.* -Recurse_





instead of :





_Get-ChildItem -Path .\*.* -Recurse # WRONG_





### Substitution





PowerShell will automatically substitute the contents of a variable inside a double-quoted string but not for single-quoted strings :





"$MyVar" => My Var Contents





'$MyVar' => $MyVar





### Escape Sequences





A back-tick (`) can be used to escape a number of characters listed below :-



<table cellpadding="2" cellspacing="0" border="0" width="300" ><tbody >
    <tr >
      
<td width="150" valign="top" >**Escape Sequence**
</td>

      
<td width="150" valign="top" >**Description**
</td>
    </tr>

    <tr >
      
<td width="150" valign="top" >`n
</td>

      
<td width="150" valign="top" >Newline
</td>
    </tr>

    <tr >
      
<td width="150" valign="top" >`r
</td>

      
<td width="150" valign="top" >Carriage return
</td>
    </tr>

    <tr >
      
<td width="150" valign="top" >`t
</td>

      
<td width="150" valign="top" >Horizontal tab
</td>
    </tr>

    <tr >
      
<td width="150" valign="top" >`a
</td>

      
<td width="150" valign="top" >Alert
</td>
    </tr>

    <tr >
      
<td width="150" valign="top" >`b
</td>

      
<td width="150" valign="top" >Backspace
</td>
    </tr>

    <tr >
      
<td width="150" valign="top" >`’
</td>

      
<td width="150" valign="top" >Single quote
</td>
    </tr>

    <tr >
      
<td width="150" valign="top" >`”
</td>

      
<td width="150" valign="top" >Double quote
</td>
    </tr>

    <tr >
      
<td width="150" valign="top" >`0
</td>

      
<td width="150" valign="top" >Null
</td>
    </tr>

    <tr >
      
<td width="150" valign="top" >``
</td>

      
<td width="150" valign="top" >A single backvtick
</td>
    </tr>

    <tr >
      
<td width="150" valign="top" >`
</td>

      
<td width="150" valign="top" >Used at the end-of-line, the newline is treated as a breaking space.
</td>
    </tr>
  </tbody></table>



## Formatting Output





None of the cmdlets format output. Instead, formatting is handled by the following cmdlets :






  
  * Format-List (useful properties : Property) 


  
  * Format-Table (useful properties : AutoSize, Wrap, GroupBy) 


  
  * Format-Wide (useful properties : Property, Column) 


  
  * Format-Custom 





When a cmdlet is run and a formatter isn't specified, the shell will determine what to display and whether to display in a list or table.





## Redirecting Output





There are a number of commands named with the verb _Out-*_ that format the objects pipelined to them and send the data out of PowerShell. They will always appear at the end of a pipeline since they do not forward any objects. The commands include :






  
  * Out-Host (useful properties : Paging) 


  
  * Out-Null (note : error information is not discarded!) 


  
  * Out-Printer (useful properties : Name) 


  
  * Out-File (useful properties : FilePath, Encoding, Width) (note : defaults to UNICODE and formats to file as if it were the console window so use the Width property) 





## Aliases





To save typing an alias can be defined for cmdlets with a number already having been defined. To list the current aliases :





_get-alias_





The Windows PowerShell Alias provider makes the aliases available in a folder :





_get-childitem alias:_





Aliases can be created (_set-alias)_ and deleted _(remove-item)_ but will only exist for the session unless the commands are added to the profile. Aliases cannot be defined for a command that requires a parameter, however, a function can be defined instead see (_get-help function_).





## Security





By default, Windows execution policies prevent Powershell scripts from running by double-clicking the files and, if they can run, whether they need to be digitally signed. For more info see _get-help about signing_. To find the current execution policy run _get-executionpolicy_. To change the current policy run _set-executionpolicy_.





## Accessing and Managing Folders





The following commands are available for all resources that have an associated drive such as certificate store and the Registry.






  
  * _get-location_ : fetches the current directory 


  
  * _set-location_ : changes the current directory (useful properties : Path, PassThru) (note : can now change to a UNC directory) 


  
  * _push-location_ : changes the current directory but also puts the previous on an ordered stack of directories. 


  
  * _pop-location_ : changes directory to a directory popped from the ordered stack. 





## Windows Management Instrumentation (WMI)





The WMI objects are usually fairly hard to discover, however, PowerShell introduces the _Get-WmiObject_ cmdlet which has the following parameters :






  
  * _List_ : lists the registered WMI classes 


  
  * _ComputerName_ : performs the requested action against a remote computer. If not specified defaults to the local computer 


  
  * _Namespace _: replaces the default WMI namespace of _root/cimv2_


  
  * _Class _: specifies the name of the class to read from the default (or specified) namespace (note : this is one of the parameters for which the name can be omitted and just the property value specified) 





## Creating .NET and COM Objects





The _New-Object_ cmdlet is used to created .Net objects and pass arguments to the constructors. It cannot be used to create static classes and will respond with an error, indicating it cannot find a constructor. Here are the main properties :






  
  * _TypeName_ : specifies the name of the .Net type to create (note : PowerShell attempts to load from the System namespace if it can't find a specific match for the class therefore the full System class path does not necessarily need to be specified) 


  
  * _ArgumentList_ : specifies a comma-separated list of constructor arguments to pass 


  
  * _ComObject_ : specifies the name of a COM object to create (note : PowerShell can use Runtime-Callable Wrappers to access COM objects and therefore suffer the same problems as that .Net has when calling COM objects) 


  
  * _Strict_ : the cmdlet will display a warning if the required COM object is being accessed through a Runtime-Callable Wrapper (and therefore consideration can be made to the problems associated with using RCWs) 





The result of _New-Object_ can be assigned to a variable rather than used in pipelines for convenience :





_$AppObj = New-Object -TypeName Diagnostics.EventLog - ArgumentList Application_





### Using Internet Explorer COM Objects





Internet Explorer can be created with :





$ie =New-Object -ComObject InternetExplorer.Application 
      
$ie.Visible=$true 
      
$ie.Navigate(http://www.google.co.uk) 
      
$ie.Document.Body.InnerText# Displays the HTML 
      
$ie.Quit()# $ie no longer contains a valid reference 
      
$ie =$null# sets the variable to null 
      
Remove-Variable $ie


[](http://11011.net/software/vspaste)



### Using Static Classes





Static classes can be accessed simply by surrounding the class name in square brackets :





_[System.Environment] | Get-Member -Static_





To access a static property or method use '::' :





_[System.Environment]::OSVersion_
