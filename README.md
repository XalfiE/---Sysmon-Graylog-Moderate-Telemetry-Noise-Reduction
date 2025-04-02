# 🛡️ Sysmon + Graylog (Moderate Telemetry)

This repository provides a **moderate-level telemetry configuration** for Sysmon along with **Graylog pipeline rules** and **dashboard widgets** to deliver strong endpoint visibility with minimal noise. It is ideal for teams who want useful signals without log overload.

---

## 📂 Repository Contents

| Path | Description |
|------|-------------|
| `config/sysmon.xml` | Sysmon configuration file (moderate-level) |
| `config/nxlog-sysmon-graylog.conf` | NXLog config to forward Sysmon logs to Graylog |
| `graylog/pipeline-rules-sysmon.conf` | Graylog pipeline rules to drop noisy Sysmon Event ID 1 logs |
| `graylog/sysmon_dashboard.json` | Graylog dashboard JSON to visualize endpoint activity |
| `README.md` | This documentation |

---

## 🚀 Deployment Instructions

### 1. Install Sysmon with the provided configuration

```bash
Sysmon64.exe -accepteula -i config/sysmon.xml
```

> 💡 Use `-c config/sysmon.xml` to update Sysmon later.

---

### 2. Deploy NXLog for Sysmon log forwarding

Update your NXLog configuration using:

```conf
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
```

📁 File: `config/nxlog-sysmon-graylog.conf`

---

### 3. Import Graylog Pipeline Rules

1. Go to **System > Pipelines** in the Graylog UI
2. Create a new pipeline and copy contents from:
   `graylog/pipeline-rules-sysmon.conf`
3. Attach the pipeline to the stream or input handling Sysmon logs

---

### 4. Import the Graylog Dashboard

1. Go to **Dashboards** → **Create Dashboard**
2. Use **More Actions → Import Dashboard**
3. Upload: `graylog/sysmon_dashboard.json`

---

## 🧠 Sysmon Config Highlights (Moderate-Level)

This configuration includes:

| Event ID | Event Description |
|----------|--------------------|
| 1        | Process Create |
| 3        | Network Connect |
| 7        | Image Load |
| 11       | File Create |
| 12–13    | Registry Create/Delete |
| 22       | DNS Query |
| 23       | File Delete |
| 25       | Process Tampering |
| 26       | File Block Executable |
| 255      | Config Change |

Excluded: Clipboard (24), Named Pipes (28), WMI Events (19–21)

---

## 🧩 Features

- ✅ Clean, tuned **Sysmon config** for high-signal telemetry
- ✅ **NXLog config** for GELF-formatted log forwarding
- ✅ **Graylog pipeline rules** to drop common noise
- ✅ **Importable dashboard** with:
  - Top processes
  - Suspicious command lines
  - Network connections
  - DNS lookups

---

## 🙋 Support

Want help tuning this to match your environment? Open an issue or reach out via Discussions. Contributions welcome!

Building cool things in tech ☁️ | Open-source & security 🚀 | Fuel my work ☕ → https://www.buymeacoffee.com/alfie
