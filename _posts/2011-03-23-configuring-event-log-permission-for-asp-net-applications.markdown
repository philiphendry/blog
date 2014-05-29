---
author: philiphendry
comments: true
date: 2011-03-23 14:40:25+00:00
layout: post
slug: configuring-event-log-permission-for-asp-net-applications
title: Configuring Event Log Permission for Asp.net applications
wordpress_id: 350
categories:
- ASP.NET
- Security
---

I’ve been having some problems with writing to a custom event log as well as the standard ‘Application’ event log where it failed with the following error :

   
    
    <span style="color:#000000;">    Stack Trace: 
        
        
        [Win32Exception (0x80004005): Access is denied]
        
        [InvalidOperationException: Cannot open log for source 'Application'. You may not have write access.]
        System.Diagnostics.EventLog.OpenForWrite(String currentMachineName) +1008783
        System.Diagnostics.EventLog.InternalWriteEvent(UInt32 eventID, UInt16 category, EventLogEntryType type, String[] strings, Byte[] rawData, String currentMachineName) +216
        System.Diagnostics.EventLog.WriteEntry(String message, EventLogEntryType type, Int32 eventID, Int16 category, Byte[] rawData) +264
        System.Diagnostics.EventLog.WriteEntry(String source, String message, EventLogEntryType type, Int32 eventID, Int16 category, Byte[] rawData) +87
        System.Diagnostics.EventLog.WriteEntry(String source, String message, EventLogEntryType type) +14
        ASP.test_testeventlogaccess_aspx.WriteToApplication(Object sender, EventArgs e) in c:\Inetpub\wwwroot\connect\Test\TestEventLogAccess.aspx:20
        System.Web.UI.WebControls.Button.OnClick(EventArgs e) +111
        System.Web.UI.WebControls.Button.RaisePostBackEvent(String eventArgument) +110
        System.Web.UI.WebControls.Button.System.Web.UI.IPostBackEventHandler.RaisePostBackEvent(String eventArgument) +10
        System.Web.UI.Page.RaisePostBackEvent(IPostBackEventHandler sourceControl, String eventArgument) +13
        System.Web.UI.Page.RaisePostBackEvent(NameValueCollection postData) +36
        System.Web.UI.Page.ProcessRequestMain(Boolean includeStagesBeforeAsyncPoint, Boolean includeStagesAfterAsyncPoint) +1565
    </span>







What made this harder to figure was it worked in one environment but not another! The difference was impersonation was turned on in the web.config for the failing web site :




    
    <span style="color:#000000;">
      </span><span style="color:#0000FF;"><</span><span style="color:#800000;">system.web</span><span style="color:#0000FF;">></span><span style="color:#000000;">
        </span><span style="color:#0000FF;"><</span><span style="color:#800000;">identity </span><span style="color:#FF0000;">impersonate</span><span style="color:#0000FF;">="true"</span><span style="color:#FF0000;"> </span><span style="color:#0000FF;">/></span><span style="color:#000000;">
      </span><span style="color:#0000FF;"></</span><span style="color:#800000;">system.web</span><span style="color:#0000FF;">></span>





This meant that the user being used to access the event log was not the AppPool identity but rather the ASP.NET IUSR_ identity (which has not been overridden with the optional username/password attributes above in the config above.)





Microsoft has a knowledge base article describing [how to configure event log permissions](http://support.microsoft.com/kb/2028427) but there were two extra steps I had to perform before I could add the IUSR identity to the event log permissions :





### Fetching the SID for an account





The SDDL string that needs to be added according to the knowledge base article referenced above requires the SID for the IUSR account. There are a couple of ways to do this listed below. Either script block should be saved to a .vbs file and run as a parameter to cscript.exe from a command line :




    
    <span style="color:#000000;">    strComputer </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">.</span><span style="color:#800000;">"</span><span style="color:#000000;">
        </span><span style="color:#0000FF;">Set</span><span style="color:#000000;"> objWMIService </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">GetObject</span><span style="color:#000000;">(</span><span style="color:#800000;">"</span><span style="color:#800000;">winmgmts:\\</span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> strComputer </span><span style="color:#000000;">&</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">\root\cimv2</span><span style="color:#800000;">"</span><span style="color:#000000;">)
        </span><span style="color:#0000FF;">Set</span><span style="color:#000000;"> objAccount </span><span style="color:#000000;">=</span><span style="color:#000000;"> objWMIService.Get(</span><span style="color:#800000;">"</span><span style="color:#800000;">Win32_UserAccount.Name='IUSR_<COMPUTER NAME>',Domain='<COMPUTER NAME>'</span><span style="color:#800000;">"</span><span style="color:#000000;">)
        Wscript.Echo objAccount.SID
        
        
        </span><span style="color:#0000FF;">On</span><span style="color:#000000;"> </span><span style="color:#0000FF;">Error</span><span style="color:#000000;"> </span><span style="color:#0000FF;">Resume</span><span style="color:#000000;"> </span><span style="color:#0000FF;">Next</span><span style="color:#000000;">
        strComputer </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">.</span><span style="color:#800000;">"</span><span style="color:#000000;">
        </span><span style="color:#0000FF;">Set</span><span style="color:#000000;"> objWMIService </span><span style="color:#000000;">=</span><span style="color:#000000;"> </span><span style="color:#0000FF;">GetObject</span><span style="color:#000000;">(</span><span style="color:#800000;">"</span><span style="color:#800000;">winmgmts:\\</span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> strComputer </span><span style="color:#000000;">&</span><span style="color:#000000;"> </span><span style="color:#800000;">"</span><span style="color:#800000;">\root\cimv2</span><span style="color:#800000;">"</span><span style="color:#000000;">)
        </span><span style="color:#0000FF;">Set</span><span style="color:#000000;"> colItems </span><span style="color:#000000;">=</span><span style="color:#000000;"> objWMIService.ExecQuery(</span><span style="color:#800000;">"</span><span style="color:#800000;">Select * from Win32_UserAccount</span><span style="color:#800000;">"</span><span style="color:#000000;">,,</span><span style="color:#800080;">48</span><span style="color:#000000;">)
        </span><span style="color:#0000FF;">For</span><span style="color:#000000;"> </span><span style="color:#0000FF;">Each</span><span style="color:#000000;"> objItem in colItems
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">AccountType: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.AccountType
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">Caption: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.Caption
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">Description: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.Description
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">Disabled: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.Disabled
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">Domain: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.Domain
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">FullName: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.FullName
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">InstallDate: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.InstallDate
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">Lockout: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.Lockout
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">Name: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.Name
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">PasswordChangeable: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.PasswordChangeable
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">PasswordExpires: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.PasswordExpires
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">PasswordRequired: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.PasswordRequired
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">SID: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.SID
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">SIDType: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.SIDType
            Wscript.Echo </span><span style="color:#800000;">"</span><span style="color:#800000;">Status: </span><span style="color:#800000;">"</span><span style="color:#000000;"> </span><span style="color:#000000;">&</span><span style="color:#000000;"> objItem.Status
        </span><span style="color:#0000FF;">Next</span>





### Remove the IUSR Identity from the Guest Users Group





The last modification which is not mentioned in the knowledge base article is that the Guest Users group is already explicitly denied access to the event log (at least on my Windows Server 2003 machine) and therefore adding the SID for this account will not have an account. To solve this I removed the IUSR account from the Guest Users group and everything worked.





### Debugging





As an aside to be able to test and debug this on both a production and test server I created an .aspx file with no code-behind with the following code :




    
    <span style="color:#000000;">    </span><span style="background-color:#FFFF00;color:#000000;"><%</span><span style="background-color:#F5F5F5;color:#000000;">@ Page Language</span><span style="background-color:#F5F5F5;color:#000000;">=</span><span style="background-color:#F5F5F5;color:#800000;">"</span><span style="background-color:#F5F5F5;color:#800000;">C#</span><span style="background-color:#F5F5F5;color:#800000;">"</span><span style="background-color:#F5F5F5;color:#000000;"> </span><span style="background-color:#FFFF00;color:#000000;">%></span><span style="color:#000000;">
        </span><span style="background-color:#FFFF00;color:#000000;"><%</span><span style="background-color:#F5F5F5;color:#000000;">@ Import Namespace</span><span style="background-color:#F5F5F5;color:#000000;">=</span><span style="background-color:#F5F5F5;color:#800000;">"</span><span style="background-color:#F5F5F5;color:#800000;">System.Diagnostics</span><span style="background-color:#F5F5F5;color:#800000;">"</span><span style="background-color:#FFFF00;color:#000000;">%></span><span style="color:#000000;">
        
        
        </span><span style="color:#0000FF;"><!</span><span style="color:#FF00FF;">DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"</span><span style="color:#0000FF;">></span><span style="color:#000000;">
        
        </span><span style="color:#0000FF;"><</span><span style="color:#800000;">html </span><span style="color:#FF0000;">xmlns</span><span style="color:#0000FF;">="http://www.w3.org/1999/xhtml"</span><span style="color:#FF0000;"> </span><span style="color:#0000FF;">></span><span style="color:#000000;">
        </span><span style="color:#0000FF;"><</span><span style="color:#800000;">head </span><span style="color:#FF0000;">runat</span><span style="color:#0000FF;">="server"</span><span style="color:#0000FF;">></span><span style="color:#000000;">
            </span><span style="color:#0000FF;"><</span><span style="color:#800000;">title</span><span style="color:#0000FF;">></span><span style="color:#000000;">Test Event Log Access</span><span style="color:#0000FF;"></</span><span style="color:#800000;">title</span><span style="color:#0000FF;">></span><span style="color:#000000;">
            
            </span><span style="color:#0000FF;"><</span><span style="color:#800000;">script </span><span style="color:#FF0000;">runat</span><span style="color:#0000FF;">="server"</span><span style="color:#0000FF;">></span><span style="background-color:#F5F5F5;color:#000000;">
            
            protected </span><span style="background-color:#F5F5F5;color:#0000FF;">void</span><span style="background-color:#F5F5F5;color:#000000;"> WriteToMyEventLog(object sender, EventArgs e)
            {
                EventLog.WriteEntry(</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">MyEventLog</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">, </span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">This is a test</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">, EventLogEntryType.Error);
            }
        
            protected </span><span style="background-color:#F5F5F5;color:#0000FF;">void</span><span style="background-color:#F5F5F5;color:#000000;"> WriteToApplication(object sender, EventArgs e)
            {
                EventLog.WriteEntry(</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">Application</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">, </span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">This is a test</span><span style="background-color:#F5F5F5;color:#000000;">"</span><span style="background-color:#F5F5F5;color:#000000;">, EventLogEntryType.Error);
            }        
            </span><span style="color:#0000FF;"></</span><span style="color:#800000;">script</span><span style="color:#0000FF;">></span><span style="color:#000000;">
            
        </span><span style="color:#0000FF;"></</span><span style="color:#800000;">head</span><span style="color:#0000FF;">></span><span style="color:#000000;">
        </span><span style="color:#0000FF;"><</span><span style="color:#800000;">body</span><span style="color:#0000FF;">></span><span style="color:#000000;">
            </span><span style="color:#0000FF;"><</span><span style="color:#800000;">form </span><span style="color:#FF0000;">id</span><span style="color:#0000FF;">="form"</span><span style="color:#FF0000;"> runat</span><span style="color:#0000FF;">="server"</span><span style="color:#0000FF;">></span><span style="color:#000000;">
            </span><span style="color:#0000FF;"><</span><span style="color:#800000;">div</span><span style="color:#0000FF;">></span><span style="color:#000000;">
                </span><span style="color:#0000FF;"><</span><span style="color:#800000;">asp:Button </span><span style="color:#FF0000;">runat</span><span style="color:#0000FF;">="server"</span><span style="color:#FF0000;"> ID</span><span style="color:#0000FF;">="btnWriteToMyEventLog"</span><span style="color:#FF0000;"> Text</span><span style="color:#0000FF;">="Write to My Event Log"</span><span style="color:#FF0000;"> OnClick</span><span style="color:#0000FF;">="WriteToMyEventLog"</span><span style="color:#FF0000;"> </span><span style="color:#0000FF;">/></span><span style="color:#000000;">
                </span><span style="color:#0000FF;"><</span><span style="color:#800000;">asp:Button </span><span style="color:#FF0000;">runat</span><span style="color:#0000FF;">="server"</span><span style="color:#FF0000;"> ID</span><span style="color:#0000FF;">="btnWriteToApplication"</span><span style="color:#FF0000;"> Text</span><span style="color:#0000FF;">="Write to Application"</span><span style="color:#FF0000;"> OnClick</span><span style="color:#0000FF;">="WriteToApplication"</span><span style="color:#FF0000;"> </span><span style="color:#0000FF;">/></span><span style="color:#000000;">
            </span><span style="color:#0000FF;"></</span><span style="color:#800000;">div</span><span style="color:#0000FF;">></span><span style="color:#000000;">
            </span><span style="color:#0000FF;"></</span><span style="color:#800000;">form</span><span style="color:#0000FF;">></span><span style="color:#000000;">
        </span><span style="color:#0000FF;"></</span><span style="color:#800000;">body</span><span style="color:#0000FF;">></span><span style="color:#000000;">
        </span><span style="color:#0000FF;"></</span><span style="color:#800000;">html</span><span style="color:#0000FF;">></span><span style="color:#000000;">
    </span>
