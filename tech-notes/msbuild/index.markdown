---
author: philiphendry
comments: true
date: 2008-03-25 08:15:02+00:00
layout: page
slug: msbuild
title: MSBuild
wordpress_id: 48
---

### References

 

  
  * [Overview](http://msdn2.microsoft.com/en-us/library/ms171452.aspx)
   
  * [Reference](http://msdn2.microsoft.com/en-us/library/0k6kkbsd.aspx)
   
  * [Task Reference](http://msdn2.microsoft.com/en-us/library/7z253716.aspx)
   
  * [Open Source Build Tasks](http://msbuildtasks.tigris.org/)
   
  * [Reserved Properties](http://msdn2.microsoft.com/en-us/library/ms164309.aspx)
   
  * [Well-known Item Metadata](http://msdn2.microsoft.com/en-us/library/ms164313.aspx)
 

### Tools

 

  
  * [Visualization in MSBuild Sidekick v2](http://blogs.microsoft.co.il/blogs/tfsidekicks/archive/2007/12/03/visualization-in-msbuild-sidekick-v2.aspx)
   
  * [Debugger](http://www.wintellect.com/cs/blogs/jrobbins/archive/2007/12/03/msbuild-debuggers.aspx)
   
  * [WiX helper tool](http://www.wintellect.com/cs/blogs/jrobbins/archive/2007/12/03/paraffin-1-02-numerous-bug-fixes.aspx) and [WiX](http://www.tramontana.co.hu/wix/) itself 
   
  * [PackageThis](http://www.codeplex.com/packagethis) would be useful if you wanted to build an offline version of the MSBuild MSDN documentation. 
   
  * [MSBuild Extension Pack](http://www.codeplex.com/MSBuildExtensionPack/Release/ProjectReleases.aspx?ReleaseId=18025) which includes 170 tasks!!
 

### Overview

 

#### Running MSBuild Projects

 

Here's how to run an MSBuild script :

 

MSBuild.exe filename.proj /property:PropertyName=Value

 

Of course, you can always use _/p:_ instead of _/property:_.

 

#### Projects

 

The **Project** element is the root element in any MSBuild project file. **InitialTargets** are executed before the **DefaultTargets** or the specified target is executed.

 
    
    <Project
        DefaultTargets="Compile"
        InitialTargets="Clean;Compile"
        xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    
        <!-- Import the contents of another project file -->
        <Import Project="..." />
    
        <!-- ProjectExtensions data is ignored by MSBuild -->
        <ProjectExtensions>...</ProjectExtensions>
    
        <PropertyGroup>
            <PropertyName>PropertyValue</PropertyName>
        </PropertyGroup>
    
        <ItemGroup>
    
        </ItemGroup>
    
        <!-- PropertyGroup and ItemGroup elements can be nested 
        inside a Choose element to provide different sets 
        of data based on certain conditions. -->
        <Choose>
            <When Condition="..."></When>
            <Otherwise></Otherwise>
        </Choose>
    
        <Target Name="..."
            DependsOnTargets="...;...;..."
            Inputs="..."
            Outputs="..." >
    
            <!-- Tasks must appear as a child of a Target -->
            <aTaskName Parameter="...">
    
                <!-- Retrieve the output of a Task using the 
             Output element. Specify either PropertyName 
             or ItemName -->
                <Output TaskParameter="Name of the Task output parameter"
                    PropertyName="Name of property to store output in"
                    Condition="..."  />
            </aTaskName>
    
            <!-- If ContinueOnError is false for a failed task, the OnError 
          tasks are executed. There can be zero or more OnError elements
          and must be the last elements in the Target. -->
            <OnError/>
    
        </Target>
    
        <!-- Maps the task referenced in a Task element to the assembly 
        containing the implementation. Can specify AssemblyFile instead 
        of AssemblyName -->
        <UsingTask TaskName="..." AssemblyName="..." />
    
    </Project>


[](http://11011.net/software/vspaste)







#### Items and Collections





Define a user-named collection of items by placing them in an **ItemGroup** then reference the collection with _@(ItemCollectionName)_. The _@()_ syntax can also include a separator used to build a string so, for example, the _Exec_ task below builds a command with parameters _/c:first /c:second /c:third_ using the _@(Phrase, ' /c:') _syntax.




    
    <Exec Command="findstr /i /c:@(Phrase, ' /c:') phrases.txt"/>





[](http://11011.net/software/vspaste)





Items can contain metadata and accessed using the _%(ItemMetadata)_ syntax. [Well-know Item Metadata](http://msdn2.microsoft.com/en-us/library/ms164313.aspx) contains a list of metadata items already defined for filenames. This allows for an expansion like :




    
    <Exec Command="demo.exe /name:%(filename) @(filesToProcess)"/>


[](http://11011.net/software/vspaste)



Wildcards can be used to specify more than one file and from sub-directories. For example _Images\**\*.jpg_ specifies all jpg files found beneath all sub-directories of the _Images_ folder.





<ItemGroup> 
      
<Compile Include = "file1.cs" /> 
        
<Compile Include = "file2.cs" /> 
        
<Compile Include = "file3.cs;file4.cs" Exclude = "file5.cs" /> 
          
<Compile Include = "file6.cs" > 
            
<!-- Meta data can be added to an item by placing it 
            
beneath the item element itself. --> 
            
<Culture>En</Culture> 
            
</ItemName> 
        
</ItemGroup>













Transforms can be used to perform a one-to-one conversion of one item collection into another. However, the transform must use metadata to specify the new items such that the new list will be a product of the original. The following example converts a list of RESX files into a new list but with the 'resources' extension:





_@(RESXFile -> '%(filename).resources')_





Another example that creates new filenames under a different folder and having a comma separator:





_@(RESXFile -> 'Toolset\%(filename)%(extension)', ',')_





#### Properties





Properties are added to **PropertyGroup** then referenced with _$(PropertyName). _There are also a number of [reserved](http://msdn2.microsoft.com/en-us/library/ms164309.aspx) properties and the contents of a property can be xml which itself can contain properties that must be defined beforehand.




    
    <PropertyGroup>
      <BuildDir>Build</BuildDir>
    </PropertyGroup>
    





[](http://11011.net/software/vspaste)









Environment variables can be referenced by simply using the name of a variable using the property syntax such as _$(PATH)_. If there's a possibility that the variable may not exist then a default can be set using a condition such as :




    
    <ToolsPath Condition="'$(TOOLSPATH)' == ''">c:\tools</ToolsPath>





[](http://11011.net/software/vspaste)





Registry entries can be read using the syntax _$(HKEY_LOCAL_MACHINE\ \KeyName\KeyName\@Value)_ or remove _@Value_ for the default value.





#### Targets





Targets group and order tasks and define entry points into the build process. See the Tasks section for an example.





Targets can also be used to perform incremental builds such that a target will only execute if a set of input files are older that a set of output files. This works best when there is a one-to-one mapping, but where there's a many-to-one mapping, the single output file will depend on the newest date stamp in the input file collection. Here's how to specify this :




    
    <Target Name="Build" Inputs="@(InputFiles)" Outputs="app.exe">
        <Csc Sources="@(InputFiles)" OutputAssembly="app.exe" />
    </Target>





Of particular use are the transforms described in the Items and Collections section above. For example:




    
    <Target Name="CopyOutputs" 
            Inputs="@(BuiltAssemblies)" 
            Outputs="@(BuiltAssemblies -> '$(OutputPath)%(Filename)%(Extension)')">
        <Copy SourceFiles="@(BuiltAssemblies)" DestinationFolder="$(OutputPath)"/>
    </Target>





#### Batching Targets





Batching can be used in conjunction with the _Inputs_ and _Outputs_ attributes of the _Target_ element in order to skip up-to-date batches. For example, the following example shows that the _%(Culture)_ metadata reference in the _Outputs_ attribute batches the _@(Res)_ item collection based on the _Culture_ metadata. This means that only those satellite assemblies that are out-of-date will be re-built.




    
    <Project
        xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    
        <ItemGroup>
            <Res Include="Strings.fr.resources">
                <Culture>fr</Culture>
            </Res>
            <Res Include="Strings.jp.resources">
                <Culture>jp</Culture>
            </Res>
            <Res Include="Menus.fr.resources">
                <Culture>fr</Culture>
            </Res>
            <Res Include="Dialogs.fr.resources">
                <Culture>fr</Culture>
            </Res>
            <Res Include="Dialogs.jp.resources">
                <Culture>jp</Culture>
            </Res>
            <Res Include="Menus.jp.resources">
                <Culture>jp</Culture>
            </Res>
        </ItemGroup>
    
        <Target Name="Build"
            Inputs="@(Res)"
            Outputs="%(Culture)\MyApp.resources.dll">
    
            <AL Resources="@(Res)"
                TargetType="library"
                OutputAssembly="%(Culture)\MyApp.resources.dll"
    
        </Target>
    
    </Project>


[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)



#### [](http://11011.net/software/vspaste)[](http://11011.net/software/vspaste)Tasks





Tasks are written in managed code, that implements the **ITask** interface, then mapped to MSBuild with a **UsingTask** element.




    
    <Target Name="aTargetName" DependOnTargets="TargetName;TargetName">
      <TaskName Parameter="$(PropertyName)" />
    </Target>





If you want to ignore any errors that a task may raise then call it with the _ContinueOnError_ attribute set:




    
    <Delete Files="@(Files)" ContinueOnError="true"/>





There's a table at the end of this document that highlights the list of standard tasks available.





#### Batching Tasks





Batching divides an item collection based on item metadata and passes each batch to a task separately. The following example highlights how the presence of _%(ExampColl.Number)_, a metadata reference, informs MSBuild to group by the _Number_ metadata and execute the task for each batch. _For each execution of the task @(ExampColl) _will contain each group and not the entire collection.




    
    <Project xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        
        <ItemGroup>
            <ExampColl Include="Item1">
                <Number>1</Number>
            </ExampColl>
            <ExampColl Include="Item2">
                <Number>2</Number>
            </ExampColl>
            <ExampColl Include="Item3">
                <Number>3</Number>
            </ExampColl>
            <ExampColl Include="Item4">
                <Number>1</Number>
            </ExampColl>
            <ExampColl Include="Item5">
                <Number>2</Number>
            </ExampColl>
            <ExampColl Include="Item6">
                <Number>3</Number>
            </ExampColl>
        </ItemGroup>
    
        <Target Name="ShowMessage">
            <Message Text = "Number: %(ExampColl.Number) -- Items in ExampColl: @(ExampColl)"/>
        </Target>
    
    </Project>





This example can be further extended to have two _ItemGroup_s, ExampColl1 and ExampColl2, and a metadata reference of _%(Number). _This will batch each collection based on the same metadata item.





The _%(Identity)_ metadata references a built-in item that, since it's a unique value for each item in a collection, can be used to batch individually.


[](http://11011.net/software/vspaste)




  


    


      


        


          


            


              


                

#### Writing a Task


              


            


          


        


      


    


  



  

A task can be written by either:



  


    
  * Implementing the [ITask](http://msdn2.microsoft.com/en-us/library/microsoft.build.framework.itask.aspx) interface. 


    
  * Deriving from the helper class [Task](http://msdn2.microsoft.com/en-us/library/microsoft.build.utilities.task.aspx) defined in Microsoft.Build.Utilities.dll. 

  

  

An _Execute_ method must be implemented and properties can be created that MSBuild will set immediately before calling _Execute_. For example:



  
    
    using System;
    using Microsoft.Build.Framework;
    using Microsoft.Build.Utilities;
    
    namespace MyTasks
    {
        public class SimpleTask : Task
        {
            public override bool Execute()
            {
                return true;
            }
    
            private string myProperty;
            public string MyProperty
            {
                get { return myProperty; }
                set { myProperty = value; }
            }
        }
    }



  

#### Logging



  

[Logging](http://msdn2.microsoft.com/en-us/library/bb651789.aspx), by default, is sent to the console and can be turned off with a _/noconsolelogger_ command line switch. MSBuild also supports a file logger by specify the _/l_ switch as shown below:



  

_/logger:FileLogger,Microsoft.Build.Engine;logfile=MyLog.log;append=true;verbosity=diagnostic;encoding=utf-8_



  

This example highlights the _logger_ switch and the ability to write a custom logger by implementing the [ILogger](http://msdn2.microsoft.com/en-us/library/microsoft.build.framework.ilogger.aspx) interface. There's a ['How to'](http://msdn2.microsoft.com/en-us/library/ms171471.aspx) on MSDN.



  

#### Standard Tasks





[](http://11011.net/software/vspaste)







[](http://11011.net/software/vspaste)







<table cellpadding="2" width="546" border="1" cellspacing="0" ><tbody >
    <tr >
      
<td width="176" valign="top" >**Task Name**
</td>

      
<td width="368" valign="top" >**Description**
</td>
    </tr>

    <tr >
      
<td width="179" valign="top" >AL
</td>

      
<td width="368" valign="top" >Assembly Linker which creates an assembly with a manifest from one or more modules or resource files.
</td>
    </tr>

    <tr >
      
<td width="181" valign="top" >AspNetCompiler
</td>

      
<td width="368" valign="top" >Wraps aspnet_compiler.exe.
</td>
    </tr>

    <tr >
      
<td width="182" valign="top" >AssignCulture
</td>

      
<td width="368" valign="top" >If provided a list of filenames with a Culture identifier embedded in the name, then it will assign the metaData Culture to it.
</td>
    </tr>

    <tr >
      
<td width="183" valign="top" >CallTarget
</td>

      
<td width="368" valign="top" >Invokes a specified target.
</td>
    </tr>

    <tr >
      
<td width="183" valign="top" >Copy
</td>

      
<td width="368" valign="top" >Copies files to a destination folder, or copies and renames if a list of destination files is specified with a one-to-one relationship with the source files.
</td>
    </tr>

    <tr >
      
<td width="183" valign="top" >CreateItem
</td>

      
<td width="368" valign="top" >Allows a new list to be created from another whilst also be filtered and new metadata being added.
</td>
    </tr>

    <tr >
      
<td width="183" valign="top" >CreateProperty
</td>

      
<td width="368" valign="top" >Creates a new property using the passed value. Use an embedded **Output** task to create a named property.
</td>
    </tr>

    <tr >
      
<td width="183" valign="top" >Csc
</td>

      
<td width="368" valign="top" >Compiles C#.
</td>
    </tr>

    <tr >
      
<td width="183" valign="top" >Delete
</td>

      
<td width="368" valign="top" >Deletes a list of files.
</td>
    </tr>

    <tr >
      
<td width="183" valign="top" >Error
</td>

      
<td width="368" valign="top" >Stops the build and logs an error based on an evaluated conditional statement.
</td>
    </tr>

    <tr >
      
<td width="183" valign="top" >Exec
</td>

      
<td width="368" valign="top" >Runs a specified program with the specified arguments.
</td>
    </tr>

    <tr >
      
<td width="183" valign="top" >FindUnderPath
</td>

      
<td width="368" valign="top" >Discovers whether a list of files exist beneath a specified path.
</td>
    </tr>

    <tr >
      
<td width="183" valign="top" >GenerateApplicationManifest
</td>

      
<td width="368" valign="top" >Generates a ClickOnce application manifest or a native manifest.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >GenerateBootstrapper
</td>

      
<td width="368" valign="top" >Provides an automated way to detect, download, and install an application and its prerequisites.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >< td>
</td>

      
<td width="368" valign="top" >< application ClickOnce corresponding the indicating and locations, update settings specifying mode, online or install as such traits deployment identifying deployment, for identity unique a defining by an of describes manifest A manifest.>
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >GenerateResource
</td>

      
<td width="368" valign="top" >Converts between .txt and .resx (XML-based resource format) files and common language runtime binary .resources files that can be embedded in a runtime binary executable or compiled into satellite assemblies. This task is typically used to convert .txt or .resx files to .resource files. The GenerateResource task is functionally similar to [resgen.exe](http://msdn2.microsoft.com/en-us/library/ccec7sz1.aspx).
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >GetAssemblyIdentity
</td>

      
<td width="368" valign="top" >Retrieves the assembly identities from the specified files and outputs the identity information.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >GetFrameworkPath
</td>

      
<td width="368" valign="top" >Retrieves the path to the .NET Framework assemblies. If several versions of the .NET Framework are installed, this task returns the version that MSBuild is designed to run on.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >GetFrameworkSdkPath
</td>

      
<td width="368" valign="top" >Retrieves the path to the Windows Software Development Kit (SDK).
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >LC
</td>

      
<td width="368" valign="top" >Wraps LC.exe, which generates a .license file from a .licx file. For more information on LC.exe, see [License Compiler (Lc.exe)](http://msdn2.microsoft.com/en-us/library/ha0k3c9f.aspx).
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >MakeDir
</td>

      
<td width="368" valign="top" >Creates directories and, if necessary, any parent directories.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >Message
</td>

      
<td width="368" valign="top" >Logs a message during a build.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >MSBuild
</td>

      
<td width="368" valign="top" >Builds MSBuild projects from another MSBuild project. Unlike using the Exec Task to start MSBuild.exe, this task uses the same MSBuild process to build the child projects.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >ReadLinesFromFile
</td>

      
<td width="368" valign="top" >Reads a list of items from a text file.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >RegisterAssembly
</td>

      
<td width="368" valign="top" >Reads the metadata within the specified assembly and adds the necessary entries to the registry, which allows COM clients to create .NET Framework classes transparently. The behavior of this task is similar, but not identical, to that of the [Assembly Registration Tool (Regasm.exe)](http://msdn2.microsoft.com/en-us/library/tzat5yw6.aspx).
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >RemoveDir
</td>

      
<td width="368" valign="top" >Removes the specified directories and all of its files and subdirectories.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >ResGen
</td>

      
<td width="368" valign="top" >Use the [GenerateResource Task](http://msdn2.microsoft.com/en-us/library/ms164295.aspx) task to convert .txt and .resx files to and from common language runtime binary .resources files.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >ResolveAssemblyReference
</td>

      
<td width="368" valign="top" >Determines all assemblies that depend on the specified assemblies, including second and nth-order dependencies.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >ResolveComReference
</td>

      
<td width="368" valign="top" >Takes a list of one or more type library names or .tlb files and resolves those type libraries to locations on disk.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >ResolveKeySource
</td>

      
<td width="368" valign="top" >Determines the strong name key source.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >ResolveNativeReference
</td>

      
<td width="368" valign="top" >Resolves native references.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >SGen
</td>

      
<td width="368" valign="top" >Creates an XML serialization assembly for types in the specified assembly. This task wraps the XML Serializer Generator Tool (Sgen.exe). For more information, see [XML Serializer Generator Tool (Sgen.exe)](http://msdn2.microsoft.com/en-us/library/bk3w6240.aspx).
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >SignFile
</td>

      
<td width="368" valign="top" >Signs the specified file using the specified certificate.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >Touch
</td>

      
<td width="368" valign="top" >Sets the access and modification times of files.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >UnregisterAssembly
</td>

      
<td width="368" valign="top" >Unregisters the specified assemblies for COM interop purposes. Performs the reverse of the [RegisterAssembly task](http://msdn2.microsoft.com/en-us/library/dakwb8wf.aspx).
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >Vbc
</td>

      
<td width="368" valign="top" >Wraps vbc.exe, which produces executables (.exe), dynamic-link libraries (.dll), or code modules (.netmodule). For more information on vbc.exe, see [Visual Basic Compiler](http://msdn2.microsoft.com/en-us/library/s4kbxexc.aspx).
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >VCBuild
</td>

      
<td width="368" valign="top" >Wraps vcbuild.exe, which builds Visual C++ projects and solutions which contain Visual C++ projects. For more information, see [VCBUILD Reference](http://msdn2.microsoft.com/en-us/library/hw9dzw3c.aspx).
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >Warning
</td>

      
<td width="368" valign="top" >Logs a warning during a build based on an evaluated conditional statement.
</td>
    </tr>

    <tr >
      
<td width="184" valign="top" >WriteLinesToFile
</td>

      
<td width="368" valign="top" >Writes the paths of the specified items to the specified text file.
</td>
    </tr>
  </tbody></table>



#### Conditions





A table lifted straight from the MSDN docs...



<table cellpadding="2" width="545" border="1" cellspacing="0" ><tbody >
    <tr >
      
<td width="188" valign="top" >**Condition**
</td>

      
<td width="355" valign="top" >**Description**
</td>
    </tr>

    <tr >
      
<td width="189" valign="top" >_'stringA' == 'stringB'_
</td>

      
<td width="354" valign="top" >
        

Evaluates to true if stringA equals stringB. For example: 
            
_ Condition="'$(CONFIG)'=='DEBUG'"_ 

            
Single quotes are not required for simple alphanumeric strings or boolean values. However, single quotes are required for empty values.


      
</td>
    </tr>

    <tr >
      
<td width="190" valign="top" >_'stringA' != 'stringB'_
</td>

      
<td width="354" valign="top" >
        

Evaluates to true if stringA is not equal to stringB. For example: 
            
_ Condition="'$(CONFIG)'!='DEBUG'"_ 

            
Single quotes are not required for simple alphanumeric strings or boolean values. However, single quotes are required for empty values.


      
</td>
    </tr>

    <tr >
      
<td width="190" valign="top" >_<, >, <=, >=_
</td>

      
<td width="354" valign="top" >
        

Evaluates the numeric values of the operands. Returns true if the relational evaluation is true. Operands must evaluate to a decimal or hexadecimal number. Hexadecimal numbers must begin with "0x". 
            
Note: In XML, the characters < and > must be escaped. The symbol < is represented as &lt;. The symbol > is represented as &gt;.


      
</td>
    </tr>

    <tr >
      
<td width="190" valign="top" >_Exists('stringA')_
</td>

      
<td width="354" valign="top" >
        

Evaluates to true if a file or folder with the name stringA exists. For example: 
            
_ Condition="!Exists('$(builtdir)')" 
              
_Single quotes are not required for simple alphanumeric strings or boolean values. However, single quotes are required for empty values.


      
</td>
    </tr>

    <tr >
      
<td width="190" valign="top" >_HasTrailingSlash('stringA')_
</td>

      
<td width="354" valign="top" >
        

Evaluates to true if the specified string contains either a trailing backward slash (\) or forward slash (/) character. 
            
For example: 

            
_ Condition="!HasTrailingSlash('$(OutputPath)')" 
              
_Single quotes are not required for simple alphanumeric strings or boolean values. However, single quotes are required for empty values.


      
</td>
    </tr>

    <tr >
      
<td width="190" valign="top" >_!_
</td>

      
<td width="354" valign="top" >Evaluates to true if the operand evaluates to false.
</td>
    </tr>

    <tr >
      
<td width="190" valign="top" >_And_
</td>

      
<td width="354" valign="top" >Evaluates to true if both operands evaluate to true.
</td>
    </tr>

    <tr >
      
<td width="190" valign="top" >_Or_
</td>

      
<td width="354" valign="top" >Evaluates to true if at least one of the operands evaluates to true.
</td>
    </tr>

    <tr >
      
<td width="190" valign="top" >_()_
</td>

      
<td width="354" valign="top" >Grouping mechanism that evaluates to true if expressions contained inside evaluate to true.
</td>
    </tr>
  </tbody></table>

  


    


      


        


          


            


              


                


                  


                    


                      


                        


                          


                            


                              


                                


                                  

#### 


                                


                              


                            


                          


                        


                      


                    


                  


                


              


            


          


        


      


    


  



