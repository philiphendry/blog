---
author: philiphendry
comments: true
date: 2011-03-23 14:40:25+00:00
layout: post
slug: configuring-event-log-permission-for-asp-net-applications
title: Configuring Event Log Permission for Asp.net applications
wordpress_id: 350
tags:
- asp-net
- security
---

I’ve been having some problems with writing to a custom event log as well as the standard ‘Application’ event log where it failed with the following error :

   
    
    Stack Trace:                 
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

What made this harder to figure was it worked in one environment but not another! The difference was impersonation was turned on in the web.config for the failing web site :

```
<system.web> 
    <identity impersonate="true" /> 
</system.web>
```

This meant that the user being used to access the event log was not the AppPool identity but rather the ASP.NET IUSR_ identity (which has not been overridden with the optional username/password attributes above in the config above.)





Microsoft has a knowledge base article describing [how to configure event log permissions](http://support.microsoft.com/kb/2028427) but there were two extra steps I had to perform before I could add the IUSR identity to the event log permissions :





### Fetching the SID for an account





The SDDL string that needs to be added according to the knowledge base article referenced above requires the SID for the IUSR account. There are a couple of ways to do this listed below. Either script block should be saved to a .vbs file and run as a parameter to cscript.exe from a command line :

```
strComputer = "." 
Set objWMIService = GetObject("winmgmts:\\" & strComputer & "\root\cimv2") 
Set objAccount = objWMIService.Get("Win32_UserAccount.Name='IUSR_<COMPUTER NAME>',Domain='<COMPUTER NAME>'") 
Wscript.Echo objAccount.SID   

On Error Resume Next 
strComputer = "." 
Set objWMIService = GetObject("winmgmts:\\" & strComputer & "\root\cimv2") 
Set colItems = objWMIService.ExecQuery("Select * from Win32_UserAccount",,48) 
For Each objItem in colItems 
	Wscript.Echo "AccountType: " & objItem.AccountType 
	Wscript.Echo "Caption: " & objItem.Caption 
	Wscript.Echo "Description: " & objItem.Description 
	Wscript.Echo "Disabled: " & objItem.Disabled 
	Wscript.Echo "Domain: " & objItem.Domain 
	Wscript.Echo "FullName: " & objItem.FullName 
	Wscript.Echo "InstallDate: " & objItem.InstallDate 
	Wscript.Echo "Lockout: " & objItem.Lockout 
	Wscript.Echo "Name: " & objItem.Name 
	Wscript.Echo "PasswordChangeable: " & objItem.PasswordChangeable 
	Wscript.Echo "PasswordExpires: " & objItem.PasswordExpires 
	Wscript.Echo "PasswordRequired: " & objItem.PasswordRequired 
	Wscript.Echo "SID: " & objItem.SID 
	Wscript.Echo "SIDType: " & objItem.SIDType Wscript.Echo "Status: " & objItem.Status 
Next
```


### Remove the IUSR Identity from the Guest Users Group





The last modification which is not mentioned in the knowledge base article is that the Guest Users group is already explicitly denied access to the event log (at least on my Windows Server 2003 machine) and therefore adding the SID for this account will not have an account. To solve this I removed the IUSR account from the Guest Users group and everything worked.





### Debugging





As an aside to be able to test and debug this on both a production and test server I created an .aspx file with no code-behind with the following code :

```
<%@ Page Language="C#" %> 
<%@ Import Namespace="System.Diagnostics"%>   
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">  
<html xmlns="http://www.w3.org/1999/xhtml" > 
	<head runat="server"> 
	<title>Test Event Log Access</title>  
	<script runat="server">  
		protected void WriteToMyEventLog(object sender, EventArgs e) 
		{ 
			EventLog.WriteEntry("MyEventLog", "This is a test", EventLogEntryType.Error); 
		}  
		protected void WriteToApplication(object sender, EventArgs e) 
		{ 
			EventLog.WriteEntry("Application", "This is a test", EventLogEntryType.Error); 
		}  
	</script>  
	</head> 
	<body> 
		<form id="form" runat="server"> 
			<div> 
				<asp:Button runat="server" ID="btnWriteToMyEventLog" Text="Write to My Event Log" OnClick="WriteToMyEventLog" /> 
				<asp:Button runat="server" ID="btnWriteToApplication" Text="Write to Application" OnClick="WriteToApplication" /> 
			</div> 
		</form> 
	</body> 
</html>
```
