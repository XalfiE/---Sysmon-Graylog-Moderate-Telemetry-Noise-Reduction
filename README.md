This project provides a pre-tuned Sysmon configuration and a set of Graylog pipeline rules to enable powerful Windows endpoint telemetry with minimal noise. Ideal for security teams who want signal-rich logging without drowning in benign process activity.

📋 Features
✅ Sysmon Configuration (sysmon.xml)
Moderate telemetry — a balance between visibility and log volume

Tracks key activity:

Process execution (EID 1)

Network connections (EID 3)

File creation/deletion (EID 11, 23)

Registry changes (EID 12–13)

DNS queries (EID 22)

Image loads (EID 7)

Process tampering (EID 25)

File block execution (EID 26)

Config changes (EID 255)

Excludes high-volume noise like clipboard events, named pipes, and WMI

✅ Graylog Pipeline Rules (pipeline-rules-sysmon.conf)
Automatically drops known-benign process activity:

cmd.exe /c echo, powershell -noprofile

Background services (svchost.exe, conhost.exe)

Google/Windows/Adobe updaters

Common browser executions (Chrome, Edge, Firefox)

Designed to reduce alert fatigue from Sysmon Event ID 1

✅ NXLog Config Snippet (nxlog-sysmon-graylog.conf)
Collects Sysmon logs from the Windows Event Log

Forwards logs to Graylog via GELF over UDP

Easy to integrate into existing NXLog deployments

🚀 Deployment Instructions
1. Install Sysmon
Download Sysmon from Microsoft Sysinternals:

Sysmon64.exe -i sysmon.xml

💡 Use -c sysmon.xml to update config later

2. Deploy NXLog
Update your NXLog config to include:
<Input sysmon>
    Module      im_msvistalog
    Query       <QueryList>\
                    <Query Id="0">\
                        <Select Path="Microsoft-Windows-Sysmon/Operational">*</Select>\
                    </Query>\
                </QueryList>
</Input>

<Output out_graylog>
    Module      om_udp
    Host        your.graylog.server
    Port        12201
    OutputType  GELF
</Output>

<Route r>
    Path        sysmon => out_graylog
</Route>

Restart the NXLog service after saving changes.


3. Import Graylog Pipeline Rules
Go to System > Pipelines in the Graylog web UI

Create a pipeline called Sysmon Cleanup

Paste in the rules from pipeline-rules-sysmon.conf

Attach the pipeline to your Sysmon input stream

🧩 Recommended Use Cases
Mid-sized enterprise environments

SIEMs with cost-sensitive ingestion models

Blue teams and threat hunters seeking clarity without full verbosity

📂 Files
sysmon.xml – Sysmon config with moderate-level telemetry

pipeline-rules-sysmon.conf – Graylog rules for suppressing noise

nxlog-sysmon-graylog.conf – Example NXLog config for Sysmon forwarding

