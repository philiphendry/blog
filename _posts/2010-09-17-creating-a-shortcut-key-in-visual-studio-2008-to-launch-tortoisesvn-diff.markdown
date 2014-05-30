---
author: philiphendry
comments: true
date: 2010-09-17 09:18:18+00:00
layout: post
slug: creating-a-shortcut-key-in-visual-studio-2008-to-launch-tortoisesvn-diff
title: Creating a shortcut key in Visual Studio 2008 to launch TortoiseSvn Diff
wordpress_id: 328
categories:
- Visual Studio
---

I wanted a quick and easy way to launch the TortoiseSvn configured diff tool showing me the differences between my saved working copy and the head version in source control. This article shows how.

First configure the external tools by selecting Tools –> Externals Tools… from the VS menu. Then add entries for the Title, Command, Arguments and Initial directory as shown below. The Command is the path to TortoiseProc.exe and those $(<param>) commands are built in parameters replaced by Visual Studio (press the black arrow to see a list.)

[![image](/assets/2010/09/image_thumb.png)](/assets/2010/09/image.png)

To aid debugging it’s useful to select ‘Prompt for arguments’ to see what VS is creating for the list of arguments.

Once done follow the instructions provided by [Microsoft](http://msdn.microsoft.com/en-us/library/80cb6ks3(v=VS.90).aspx) to assign a shortcut key to the external tool. Here’s how my Tools menu looks with diff assigned the CTRL+SHIFT+ATL+D keypress :

[![image](/assets/2010/09/image_thumb1.png)](/assets/2010/09/image1.png)

**Update:**

Here are another couple of ideas. Add TortoiseProc.exe /command:update /path:"$(solutionpath)" and the same again for the commit command. Now I can press Ctrl+Alt+Shift+C to commit any solution changes or Ctrl+Alt+Shift+U to update the solution to the head revision.

For a list of available TortoiseProc commands see [http://tortoisesvn.net/docs/release/TortoiseSVN_en/tsvn-automation.html](http://tortoisesvn.net/docs/release/TortoiseSVN_en/tsvn-automation.html)
