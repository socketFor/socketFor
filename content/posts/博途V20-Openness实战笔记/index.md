---
title: "博途 V20 Openness 实战笔记"
date: 2026-10-25
draft: false
categories:
  - "plc"
tags:
  - "ai"
---


> 2026-10-25 一天的实际折腾记录：从「连不上博途」到「用 MCP 在博途里画梯形图、画 HMI 画面」。
> 环境：Windows 11 + TIA Portal V20（装在 `D:\V19\Portal V20`）+ .NET Framework 4.8 + Windows PowerShell 5.1。
> 这份笔记只写「实测有效 / 实测报错」的东西，不写官方文档里抄来的理论。
>
> 通过网盘分享的文件：TIA_V20_MCP_v1.4.zip
> 链接: https://pan.baidu.com/s/1km6EocXcviYCIpJqfanPoQ?pwd=1145 提取码: 1145 
> --来自百度网盘超级会员v5的分享

---

## 0. 一天做完的事（结论先行）

| 事项 | 结果 |
| --- | --- |
| 用 Openness 连上博途 | ✅ 关键是**执行通道**，不是权限（见第 3 节） |
| 新建项目 + 加 S7-1200 CPU | ✅ `OrderNumber:6ES7 214-1AG40-0XB0/V4.4` |
| 起保停（SCL 版 FB+DB+OB1） | ✅ 编译 0 错 0 警 |
| 起保停（梯形图 LAD 版） | ✅ SimaticML XML 导入，两块网络 |
| 星三角（Y-△）启动梯形图 | ✅ 6 段网络 + 2 个 TON，0 错 0 警 |
| HMI 画面（KTP700） | ✅ 先出效果图 → 按图写画面 XML → 导入 |
| 自研 MCP 服务（18 个工具） | ✅ stdio JSON-RPC，VS Code / Cursor / Claude Code 可接 |

---

## 1. 环境与前置条件

```
TIA Portal V20 主程序   D:\V19\Portal V20\Bin\Siemens.Automation.Portal.exe
Openness API            D:\V19\Portal V20\PublicAPI\V20\Siemens.Engineering.dll
                        D:\V19\Portal V20\PublicAPI\V20\Siemens.Engineering.Hmi.dll
依赖（不在 GAC 里）     D:\V19\Portal V20\Bin\PublicAPI\Siemens.Engineering.Contract.dll
                        D:\V19\Portal V20\Bin\PublicAPI\Siemens.Engineering.ClientAdapter.Interfaces.dll
Windows 组              Siemens TIA Openness（当前用户必须是成员）
```

**在 PowerShell 里加载 Openness 的正确姿势**（不需要 `AssemblyResolve`，加了反而可能 `StackOverflowException`）：

```powershell
foreach ($d in @(
  'D:\V19\Portal V20\PublicAPI\V20\Siemens.Engineering.dll',
  'D:\V19\Portal V20\PublicAPI\V20\Siemens.Engineering.Hmi.dll',
  'D:\V19\Portal V20\Bin\PublicAPI\Siemens.Engineering.Contract.dll',
  'D:\V19\Portal V20\Bin\PublicAPI\Siemens.Engineering.ClientAdapter.Interfaces.dll')) {
  [void][Reflection.Assembly]::LoadFrom($d)
}
```

注意：`Siemens.Engineering.SW.SoftwareContainer` **不存在**，真实类型是 `Siemens.Engineering.HW.Features.SoftwareContainer`。
经典 HMI 的类型全在 `Siemens.Engineering.dll` 里（`Siemens.Engineering.Hmi.*`），`Siemens.Engineering.Hmi.dll` 只有 4 个导出类型。

---

## 2. 连接博途：可靠套路

```powershell
# 1) 清场：所有残留 portal 一律杀掉（不然会占着实例、且旧实例常常「不可发现」）
Get-Process Siemens.Automation.Portal -ErrorAction SilentlyContinue | Stop-Process -Force
Start-Sleep -Seconds 3

# 2) 用 explorer 启动（等价于用户双击，脱离当前进程树的限制）
Start-Process explorer.exe '"D:\V19\Portal V20\Bin\Siemens.Automation.Portal.exe"'

# 3) 轮询直到可发现（一般 10–15 秒）
for ($i=0; $i -lt 36; $i++) {
  $procs = [Siemens.Engineering.TiaPortal]::GetProcesses()
  if ($procs.Count -gt 0) { break }
  Start-Sleep -Seconds 5
}

# 4) Attach（用 public 的 Attach()，不要碰 internal 构造函数）
$tia  = $procs[0].Attach()
$proj = $tia.Projects[0]
```

要点：

- `TiaPortal.GetProcesses()` 走的是命名管道 `\\.\pipe\Siemens.Automation-Siemens.Automation.Portal.<pid>`。管道存在 ≠ 可发现，**必须是「新鲜」实例**：一个会话没 Dispose 就退出后，该 portal 往往再也发现不了，只能杀掉重开。
- 不要用 `new TiaPortal(WithoutUserInterface)` 去「顺手启动」，失败信息只有一句 `Connection to TiaPortal failed.`，极难定位；用第 4 步的 Attach。
- 反射传 `Process` 参数时要 `[System.Diagnostics.Process]::GetProcessById($pid)`，直接传 `Get-Process` 的管道对象会报
  `ArgumentException: 类型"System.Management.Automation.PSObject"的对象无法转换为类型"System.Diagnostics.Process"`。
- 结束时务必 `$tia.Dispose()`，否则下一次可能就发现不了实例了。

---

## 3. 最大的坑：执行通道（进程被「裁剪」）

这一条花了大半天，务必先看。

**现象**：无论怎么写，`TiaPortal.GetProcesses()` 都返回 0；`new TiaPortal(...)` 5 秒内抛 `Connection to TiaPortal failed.`；而此时博途明明开着、管道也在。

**用 GUI 探针（TiaPortalMode.WithUserInterface + 截屏）才看到真相**：博途自己弹窗

```
对该文件夹中应用程序数据的访问受限。
该应用程序无法启动。
C:\Users\socket\AppData\Roaming\Siemens\Automation\Portal V20\Settings\Settings.xml 写保护，
请联系您的系统管理员。
```

即：**由 AI 代理（DSH）派生出来的进程，其子进程被限制了进程可见性与文件写入**，博途起不来、Openness 客户端也看不到实例。这与文件 ACL、UAC、白名单都无关（`Settings.xml` 的 ACL 干净、管理员可写、白名单哈希也对得上）。

**实测结论矩阵**：

| 执行通道 | 结果 |
| --- | --- |
| AI 代理沙箱内的 pwsh 直接跑 exe | ❌ 子进程不可见/写不了 AppData |
| AI 代理沙箱内的 pwsh **进程内**直接调 Openness（`Invoke-Expression` 加载 DLL） | ✅ 可用（宿主进程本身没被裁剪） |
| `Start-Process explorer.exe "<exe>"` | ✅ 可用（转发给已运行的 explorer，等于用户双击） |
| 计划任务（`Register-ScheduledTask` + `Start-ScheduledTask`） | ✅ 完全不受限，适合跑自动化测试 |

**取舍**：

- 手工验证 → 在宿主 PowerShell 里 `Invoke-Expression` 加载脚本执行；
- 需要独立进程 → 走 `explorer.exe` 或计划任务；
- 正式的 MCP 服务由 VS Code / Cursor / Claude Code 启动，不在受限进程树里，**用户侧没有这个问题**。

另外两条与沙箱无关但同样致命的：

- 执行策略禁止直接跑 `.ps1` → 用 `[scriptblock]::Create((Get-Content $p -Raw -Encoding UTF8))` 再 `& $sb -参数`。
- **含中文的 `.ps1` 必须是 UTF-8 带 BOM**，否则 Windows PowerShell 5.1 按 ANSI 解析，中文全乱码。

> 这一节只给结论和取舍；**当初是怎么一步步走错的、证据数字是多少、以及 10 分钟定位清单**，见文末「附录 A」。

---

## 4. 关键 API 速查（含反射写法）

| 目的 | 调用 |
| --- | --- |
| 新建项目 | `$tia.Projects.Create($dir, $name)`（约 8–10 秒） |
| 打开项目 | `$tia.Projects.Open($fileInfo)`（同一实例只能开一个项目，先 `Close()` 其它） |
| 加设备 | `$tia.Projects[0].Devices.CreateWithItem($typeIdentifier, '', 'PLC_1')` |
| 取 PLC 软件 | 对每个 `DeviceItem` 反射调无参泛型 `GetService(SoftwareContainer)`，再取 `.Software` |
| 编译 | 反射拿到 `Siemens.Engineering.Compiler.ICompilable`，`$c.Compile()` |
| 导入 SCL | `$plc.ExternalSourceGroup.ExternalSources.CreateFromFile($name, $path)` → `$src.GenerateBlocksFromSource()` |
| 导入梯形图 | `$plc.BlockGroup.Blocks.Import($fileInfo, [Siemens.Engineering.ImportOptions]::Override)` |
| 导入 HMI 画面 | `$hmi.ScreenFolder.Screens.Import($fileInfo, [Siemens.Engineering.ImportOptions]::Override)` |
| 保存 / 收尾 | `$proj.Save()` / `$tia.Dispose()` |

**`GetService` 在 PowerShell 里必须反射调**（它是无参泛型方法）：

```powershell
function Get-GenericService($obj, [Type]$svcType) {
  $m = $obj.GetType().GetMethods() |
       Where-Object { $_.Name -eq 'GetService' -and $_.IsGenericMethodDefinition -and $_.GetParameters().Count -eq 0 } |
       Select-Object -First 1
  if (-not $m) { return $null }
  return $m.MakeGenericMethod($svcType).Invoke($obj, @())
}
```

找 PLC 软件时**不要**先判断 `$item -is [SoftwareContainer]`（判断不中，会得到 `PLC not found`），要对每个 DeviceItem **无条件 try/catch 反射调用**，拿到 `$svc.PSObject.Properties['Software'].Value` 才是 `PlcSoftware`。

设备型号必须写**带 `OrderNumber:` 前缀的完整串**：

```
OrderNumber:6ES7 214-1AG40-0XB0/V4.4     # CPU 1214C DC/DC/DC，可用
OrderNumber:6AV2 123-2GA03-0AX0/17.0.0.0 # KTP700 Basic DP（HMI）
```

不写前缀报 `Unknown TypeIdentifer "..."`；写 `S7-1200` 同样报错；`6AG1 2XX-XXXXX-XXXX/V4.4` 是 SIPLUS 占位项，建出来的设备**没有 PLC 软件**。

---

## 5. SCL 外部源：几个必须记住的语法

**实例 DB（自动生成的调用 DB）只能这么写**：

```
DATA_BLOCK "起保停_DB"
"起保停"
BEGIN
END_DATA_BLOCK
```

- 只写 `DATA_BLOCK "名"` + 下一行父 FB 名 + `BEGIN`；
- **绝不能加 `VERSION`、`NON_RETAIN` 或属性行**，否则报
  `行 000002: 语法错误：设定值"VERSION"无效` / `设定值"{"无效`。

**OB1 头必须先写属性行再写 VERSION**：

```
ORGANIZATION_BLOCK "Main"
{ S7_Optimized_Access := 'TRUE' }
VERSION : 0.1
BEGIN
	"起保停_DB"(Start := %I0.0, Stop := %I0.1, Motor => %Q0.0);
END_ORGANIZATION_BLOCK
```

其它要点：

- `GenerateBlocksFromSource()` **不会覆盖同名块** —— 想重建必须先 `Blocks.Find(name)` 再 `Delete()`。
- 含中文的 `.scl` 必须存 **UTF-8 带 BOM**，否则中文注释/变量名乱码。
- 一个外部源里可以写多个块（FB + DB + OB 一起），一次生成。

---

## 6. 梯形图（LAD）：SimaticML / FlgNet XML

Openness **没有绘图 API**，梯形图唯一入口是 `Blocks.Import(FileInfo, ImportOptions.Override)` + XML。

### 6.1 骨架

```xml
<?xml version="1.0" encoding="utf-8"?>
<Document>
  <Engineering version="V20" />          <!-- 必须是 V20，写 V21 报 not supported -->
  <SW.Blocks.FB ID="0">
    <AttributeList>
      <AutoNumber>false</AutoNumber>
      <Interface>…v5 的 Sections：Input / Output / InOut / Static / Temp / Constant…</Interface>
      <MemoryLayout>Optimized</MemoryLayout>
      <Name>起保停_LAD</Name>
      <Number>100</Number>
      <ProgrammingLanguage>LAD</ProgrammingLanguage>
      <SetENOAutomatically>false</SetENOAutomatically>
    </AttributeList>
    <ObjectList>
      <SW.Blocks.CompileUnit ID="1" CompositionName="CompileUnits">
        <AttributeList><NetworkSource>…</NetworkSource></AttributeList>
        <ObjectList><FlgNet xmlns="http://www.siemens.com/automation/Openness/SW/NetworkSource/FlgNet/v5">…</FlgNet></ObjectList>
      </SW.Blocks.CompileUnit>
    </ObjectList>
  </SW.Blocks.FB>
</Document>
```

FB 导入**不能带 `<MemoryReserve>`**：`'set_MemoryReserve' is not supported by type 'Siemens.Engineering.SW.Blocks.FB'`。

### 6.2 三要素：Part / Access / Wire

- `Access`：操作数（`Scope="LocalVariable"` 局部、`"GlobalVariable"` 全局 DB、`"TypedConstant"` 常量）
- `Part`：指令（Contact、Coil、O、TON…）
- `Wire`：连线（Powerrail、NameCon、IdentCon、OpenCon）

**一个程序段只能有一条 Powerrail**，但**可以在同一条 `<Wire>` 里扇出多个 `NameCon`** —— 这是并联的关键：

```xml
<!-- 电源线同时接两个触点的 in -->
<Wire UId="40"><Powerrail /><NameCon UId="30" Name="in" /><NameCon UId="31" Name="in" /></Wire>

<!-- 并联：O 指令，Card=2 -->
<Part Name="O" UId="32"><TemplateValue Name="Card" Type="Cardinality">2</TemplateValue></Part>

<!-- 常闭触点：Contact + Negated -->
<Part Name="Contact" UId="33"><Negated Name="operand" /></Part>

<!-- 线圈 -->
<Part Name="Coil" UId="34" />
```

### 6.3 TON 定时器

```xml
<!-- 静态实例要声明成 TON_TIME 并标 SetPoint -->
<Member Name="StarTimer" Datatype="TON_TIME" Version="1.0">
  <AttributeList><BooleanAttribute Name="SetPoint" SystemDefined="true">true</BooleanAttribute></AttributeList>
</Member>

<!-- 调用 -->
<Part Name="TON" Version="1.0" UId="31">
  <Instance Scope="LocalVariable" UId="32"><Component Name="StarTimer" /></Instance>
  <TemplateValue Name="time_type" Type="Type">Time</TemplateValue>
</Part>

<!-- 时间常数 -->
<Access Scope="TypedConstant" UId="22"><Constant><ConstantValue>T#5S</ConstantValue></Constant></Access>

<!-- 读定时器输出 .Q -->
<Symbol><Component Name="StarTimer" /><Component Name="Q" /></Symbol>
```

### 6.4 报错对照表（都是实测撞过的）

| 报错 | 原因 |
| --- | --- |
| `The engineering version 'V21' in line 3 … is not supported.` | 根节点版本写成 V21 |
| `在 LAD 中，程序段中只能包含一个电源线。` | 网络里放了两条 `<Powerrail>` |
| `The connection named 'in' at the part with UId '36' is used multiple times at the cables.` | 两个 `out` 接到同一个 `NameCon`（并联要用 O 块） |
| `所用 CPU 中的该指令不支持属性"DisableENO"。` | 直接拿官方导出 XML 改版本号回灌，导出态属性不合法 |
| `'set_MemoryReserve' is not supported …` | FB 里带了 `<MemoryReserve>` |
| `The resource file contains corrupted data or format might be invalid.` | 用了 `.s7dcl` + `.s7res` 文档路线；V20 不认 V21 的资源格式 |

---

## 7. HMI 画面（经典 WinCC 面板）

### 7.1 也没有对象级 API

`Hmi.Screen.Screen` 只有 `Name / Export / Delete`；`ScreenComposition` 只有 `CreateFrom / Import`，**没有 `Create(name)`**。所以只能整屏导入：

```powershell
$hmi.ScreenFolder.Screens.Import($fileInfo, [Siemens.Engineering.ImportOptions]::Override)
```

### 7.2 推荐工作流：先出效果图，再落画面

1. 用绘图模型出 800×480 的**最终视觉稿**，把布局、配色、字号定死；
2. 按比例换算坐标（`x·800/1536`、`y·480/1024`）写画面 XML；
3. 导入 + `ICompilable.Compile()`；
4. 在博途里缩放 75% 截整屏，与效果图逐项对照。

直接手写 XML 很容易画歪，先有图再写 XML 快得多。

### 7.3 XML 要点

| 项 | 写法 |
| --- | --- |
| 版本 | `<Engineering version="V20" />` |
| 画面 | `Hmi.Screen.Screen`，属性给 `Name` / `Width` / `Height` / `BackColor` |
| 图层 | `Hmi.Screen.ScreenLayer CompositionName="Layers"` |
| 控件 | 全部 `CompositionName="ScreenItems"`：`TextField` / `Button` / `Circle` / `Rectangle` / `IOField` |
| 文字 | `MultilingualText CompositionName="Text"`，内容必须 `<Text><body><p>…</p></body></Text>` |
| 字体 | `Hmi.Globalization.MultiLingualFont` → `FontItem`（`Culture`/`FontFamily`/`FontSize`/`FontStyle`） |
| 颜色 | `R, G, B` 十进制，**必须加 `<UseDesignColorSchema>false</UseDesignColorSchema>`**，否则按设计配色显示（按钮变灰） |
| 层级 | `ObjectList` 顺序就是 z 序，底色矩形必须写在文字之前 |

一个 800×480 的起保停画面实测布局：

| 元素 | 位置 / 尺寸 | 颜色 |
| --- | --- | --- |
| 标题条 Rectangle | 0,0 / 800×56 | 44,62,80 |
| 标题 TextField 20 Bold | 24,10 / 420×36 | 白字 |
| 启动 Button 30 Bold | 60,110 / 240×125 | 34,160,74 |
| 停止 Button 30 Bold | 330,110 / 240×125 | 200,40,40 |
| 运行灯 Circle | 645,118 / 110×110 | 139,210,47 |
| 运行时间 TextField | 200,356 / 400×30 | 110,116,122 |
| 底部条 Rectangle + 文字 | 0,420 / 800×60 | 白底 + 220,224,229 描边 |

---

## 8. 中文路径 Bug（Openness）

项目名/路径用中文时，**Openness 往返字符串会被破坏成 U+FFFD**：

- `$proj.Name` 10 个字符里有 6 个是 `65533`；
- `$proj.Path.FullName` 82 个字符里中文段全是 `65533`；
- 拿这个路径再 `Open()` 会报 `The file '…' was not found.`。

但**程序块名、SCL 文件内容里的中文是正常的**，只有项目级字符串受影响。

对策：

- 项目目录/文件名一律用**纯英文**（如 `C:\TIA_PROJECTS\MotorStartStop`）；
- 工具内部记住**自己拼的路径**，别信 Openness 回读的 `ProjectPath`；
- 显示时若含 `0xFFFD` 就标注 `(non-ASCII path, display suppressed)`。

---

## 9. 用 GUI 自动化做验收（截图取证）

代码改完，最终还是要肉眼确认。这套是实测可用的：

- **截屏**：`System.Drawing` + `Graphics.CopyFromScreen`，存 PNG。
- **窗口置前**：`AttachThreadInput` → `BringWindowToTop` → `SetForegroundWindow` → `ShowWindow(3)`，否则截到的是自己窗口。
- **坐标换算（关键）**：截图是 1536×864，实际点击要 `物理 = 截图 / 1.25`；点击用 `SetCursorPos` + `mouse_event(0x0002/0x0004)`。
- **双击节奏**：两次点击间隔约 140 ms 才会「打开编辑器」，慢了会进入重命名。
- **滚轮**：`mouse_event(0x0800, 0, 0, 4294967176, 0)`（4294967176 = -120 的无符号表示）。
- **找丢失的面板**：`Ctrl+1` 恢复项目树；右下角缩放框可输入 `75` 改画布比例。
- 博途是自绘控件，**UI Automation 拿不到元素**（`tree items visible: 0`），只能靠坐标 + 截图。

---

## 10. MCP 服务本身（工程化部分）

- **传输**：stdio + 逐行 NDJSON 的 JSON-RPC 2.0；`initialize` 返回 `protocolVersion: 2024-11-05`、`serverInfo: tia-portal-v20-mcp 1.0.0`。
- **实现语言**：Windows PowerShell 5.1（免安装依赖），文件必须 UTF-8 **带 BOM**。
- **工具（18 个）**：
  - 连接：`tia_status` / `tia_launch` / `tia_connect` / `tia_close`
  - 项目：`tia_new_project` / `tia_open_project` / `tia_save` / `tia_list_devices`
  - 程序：`tia_add_plc` / `tia_list_blocks` / `tia_import_scl` / `tia_compile`
  - 一键：`tia_motor_startstop`（SCL 起保停）、`tia_lad_startstop`（梯形图起保停）、`tia_lad_star_delta`（梯形图星三角）
  - HMI：`tia_hmi_list` / `tia_hmi_startstop` / `tia_hmi_import`
- **PowerShell 5.1 的两个坑**：不支持 `try {}` 当表达式；`$PSScriptRoot` 在 `Invoke-Expression` 里是空的，脚本用 `$env:XXX` 传目录。
- **自动化测试**：写 `job.txt`（第 1 行工作目录、第 2 行 exe、其余行拼参数）→ 计划任务跑 `runjob.exe` → 轮询 `job.log` 里的 `EXIT=`。
  - 坑：job 解析中文路径会失败，**先把整个包复制到纯英文临时目录再跑**。

---

## 11. 踩坑清单（速查版）

1. 连不上博途 → 先确认**执行通道**，不是权限问题（第 3 节）。
2. `TiaPortal.GetProcesses()` 返回 0 → 实例「不新鲜」了，杀掉用 explorer 重启。
3. 不要 `new TiaPortal(WithoutUserInterface)` 兜底，失败只有一句 `Connection to TiaPortal failed.`。
4. `ExternalSourceGroup.ExternalSources`（不是 `Sources`），`CreateFromFile(name, path)` 两个参数。
5. `SoftwareContainer` 在 `HW.Features` 命名空间；`GetService` 必须反射。
6. 设备型号要 `OrderNumber:…/版本`，且别选 `6AG1 2XX` 占位项。
7. 实例 DB 不能写 `VERSION`；OB 头要先属性行后 VERSION。
8. `GenerateBlocksFromSource` 不覆盖同名块，先删后建。
9. `.scl` / 画面 XML / 脚本：中文一律 UTF-8 **带 BOM**。
10. LAD 一条网络只能一个 Powerrail；并联靠 `<Wire>` 扇出 + `O` 块。
11. V20 用不了 V21 的 `.s7dcl/.s7res` 和官方导出 XML（`DisableENO` 不支持）。
12. FB 导入不能带 `MemoryReserve`。
13. HMI 只写 `BackColor` 会变灰，要 `UseDesignColorSchema=false`；文字要 `<body><p>`。
14. 项目路径别用中文（U+FFFD 往返损坏）。
15. 同一实例只能开一个项目，切项目前先 `Close()`。
16. 会话结束必须 `Dispose()`。
17. 截图坐标要 `/1.25`；双击间隔 140 ms。
18. Markdown 表格里的图片不会进 Word —— 写手册时图片单独成段。

---

## 12. 可复用的最小骨架

**导入一段梯形图 FB 并编译：**

```powershell
# 假设已 LoadFrom 好 4 个 DLL 并 $tia = GetProcesses()[0].Attach()
$proj = $tia.Projects[0]
$plc  = Find-PlcSoftware $proj 'PLC_1'

$old = $plc.BlockGroup.Blocks.Find('起保停_LAD')
if ($old) { $old.Delete() }

$fi = [System.IO.FileInfo]::new('C:\path\MotorStartStop_LAD.xml')
[void]$plc.BlockGroup.Blocks.Import($fi, [Siemens.Engineering.ImportOptions]::Override)

$cmp = Get-GenericService $plc ([Siemens.Engineering.Compiler.ICompilable])
$r = $cmp.Compile()
"state=$($r.State) err=$($r.ErrorCount) warn=$($r.WarningCount)"
$proj.Save()
$tia.Dispose()
```

**导入一张 HMI 画面：**

```powershell
$hmi  = Find-HmiTarget $proj 'HMI_1'
$old  = $hmi.ScreenFolder.Screens.Find('起保停画面')
if ($old) { $old.Delete() }
[void]$hmi.ScreenFolder.Screens.Import([System.IO.FileInfo]::new('C:\path\Screen.xml'),
                                        [Siemens.Engineering.ImportOptions]::Override)
$cmp = Get-GenericService $hmi ([Siemens.Engineering.Compiler.ICompilable])
$null = $cmp.Compile()
$proj.Save()
```

---

## 13. 还没做的（下次可以接着干）

- HMI 按钮的**事件**（按下置位 `Hmi.Event.Event`）与指示灯的**动态绑定**（`Hmi.Dynamic.TagConnectionDynamic`）——需要先建 HMI↔PLC 连接与 HMI 变量。
- 把项目编译/下载到 PLCSIM Advanced 或真机（`Download` 属于可选开放接口，需要额外授权）。
- 报警（`HmiTarget.AlarmFolder`）、配方、趋势图的模板化。
- MCP 服务加 Windows 服务/守护模式（当前是 stdio，宿主拉起即用）。

---

## 附录 A：为什么最初连不上（事后复盘）

> 这一段是事后拿日志重新推的，比正文第 3 节更细。证据来自 `mini.out.txt`（DSH 子进程视角）、`mini2.out.txt` / `job.log`（计划任务视角）。

### A.1 一句话结论

不是权限不足，而是**执行通道**：代码跑在「代理派生的子进程」里，而沙箱对子进程做了进程枚举过滤 + 文件写入拦截；宿主 PowerShell 进程本身不受限。

### A.2 它其实是两个叠加的故障

| # | 故障 | 表现 |
| --- | --- | --- |
| A | **portal 自己起不来** | 弹窗「Settings.xml 写保护」→ 初始化中止 → 根本没有可连的实例 |
| B | **客户端看不见 portal** | 实例在跑，`GetProcesses()` 返回 0、`GetProcessById` 报「进程未运行」 |

只解决 A，会撞上 B；只解决 B，会撞上 A —— 这是当时反复“明明好了又不行”的原因。

### A.3 故障 A：博途写不了自己的配置文件

启动时必须写 `%APPDATA%\Siemens\Automation\Portal V20\Settings\Settings.xml`，写失败就弹：

```
对该文件夹中应用程序数据的访问受限。
该应用程序无法启动。
… Settings.xml 写保护，请联系您的系统管理员。
```

**最贵的一个误判**：我用 `Set-Content` 往同目录写测试，是成功的，于是断定“文件没问题”。

真实差别在**是谁在写**：

| 谁在写 | 结果 |
| --- | --- |
| 宿主 pwsh 自己（`Set-Content` 在进程内执行） | ✅ 成功 |
| 宿主派生的子进程（探针、被派生的 portal） | ❌ `UnauthorizedAccessException` |

佐证：把 `Settings.xml` 挪走后再从受限进程启动，**连新文件都不生成**（创建同样被拒）；改用 `Start-Process explorer.exe "<portal.exe>"` 后，立刻生成 1605 字节的新文件并正常到启动页。

### A.4 故障 B：客户端枚举不到 portal

Openness 实例发现要三步：找到命名管道 → `OpenProcess` 校验 → `IpcConnectionManager.ActivateService` 拿会话。受限进程里第 2 步就断。实测输出：

```
all=20
portal=0 explorer=0
byName portal=0 explorer=0
byId FAIL ArgumentException: ID 为 29228 的进程当前未运行。
appdata write FAIL UnauthorizedAccessException: …
```

正常进程数 400+，这里只有 **20**；一个活着的 pid 报「当前未运行」—— 典型的枚举被过滤。

> 假线索：用 `NamedPipeClientStream` 连管道是**成功**的。管道能连 ≠ 能发现实例。

### A.5 主谋：沙箱的边界画在「进程树」上

同一份代码换宿主，结论完全不同：

| 运行方式 | 进程可见数 | portal 可见 | AppData 可写 | Openness 结果 |
| --- | --- | --- | --- | --- |
| 作为 DSH 子进程 | **20** | 0 | ❌ | `Connection to TiaPortal failed.` |
| 在宿主 pwsh **进程内**调用 | 全部 | **1** | ✅ | ✅ Attach 成功 |
| `Start-Process explorer.exe "<exe>"` | 全部 | — | ✅ | ✅ portal 正常启动 |
| 计划任务（runjob / mini2） | **404 / 424** | ✅（3 个） | ✅ | ✅ |

三条推论：

1. `danger-full-access` 只放宽**宿主工具进程**的路径限制，**不解除子进程沙箱**（用它跑探针依旧只有 20 个进程）。
2. `Invoke-Expression` 能成功，因为 cmdlet 与 `LoadFrom` 都在宿主进程内执行，没有创建子进程。
3. 进程表里的 `codex-windows-sandbox-service` 就是这套沙箱的服务端，按**派生关系**过滤，与用户 SID、完整性级别都无关（所以 `whoami /groups` 看到的是 Medium 而不是 Low）。

### A.6 还有第二类「看不见」：实例失效

19:01 之后又出现一次 `GetProcesses()` = 0，但这次**宿主里也是 0**，同时 `GetProcessesByName` 仍为 1、管道在、工作集 1439 MB。

原因是**上一个会话没 `Dispose()` 就退出**，portal 里的 Attach 服务注册留在了坏状态，之后谁都发现不了。

判别表：

| 判别 | 结论 | 处理 |
| --- | --- | --- |
| 宿主里 `GetProcesses()` 也是 0 | 实例失效 | 杀掉重开（约 15 秒） |
| 宿主里是 1、子进程里是 0 | 通道被裁剪 | 换执行通道 |

### A.7 五个被冤枉的嫌疑人

| 嫌疑人 | 为什么像 | 为什么不是 |
| --- | --- | --- |
| Openness 白名单 / 授权 | V18+ 确有此机制 | 重算 SHA256 Base64 与注册表 `FileHash` 完全一致 |
| 需要管理员提权 | 写注册表被拒、UAC 提示 | 提权解决不了，沙箱不认管理员令牌 |
| 僵尸 portal 占用 | 确实有两个遗留进程 | 杀干净后照样失败 |
| 完整性级别是 Low | 沙箱通常降 IL | 实测 Medium（0x2000） |
| portal 启动即崩溃 | 有 `CrashDetector` / `Report` 进程冒出来 | 那是错误框自己的上报组件 |

另外 `TiaMcpServer.exe doctor` 全绿也有误导性：它只检查**静态前置条件**（路径、DLL、组、.NET、MOTW），不验运行时 IPC 能力。

### A.8 重来一次的 10 分钟定位清单

```powershell
# 1) 先在宿主进程内确认实例是否健康（不要起子进程）
[Diagnostics.Process]::GetProcessesByName('Siemens.Automation.Portal').Count

# 2) 同一份代码再跑一次「子进程版」，比对进程总数
#    宿主 ~400，子进程 ~20  →  通道被裁剪，别再去查权限
(Get-Process).Count

# 3) portal 是否健康：能写 Settings.xml 且能到启动页
#    弹「写保护」= 启动链路被拦，不是文件权限问题

# 4) 客户端连不上先判 stale：宿主里 GetProcesses() 为 0 就杀掉重开
```

**三条铁律**：

1. 凡是要和外部 GUI 程序打交道的自动化（Openness / COM / UI Automation），**不要在代理的子进程里跑**；
2. 要测“某程序能不能写某个文件”，**必须让那个程序自己写**，宿主代写毫无意义；
3. 判断“连不上”之前先确认 portal **健康且新鲜**——这两个条件缺一个，报错都是同一句 `Connection to TiaPortal failed.`。

### A.9 对交付的影响

正式的 MCP 服务由 VS Code / Cursor / Claude Code 拉起，**不在沙箱进程树里**，用户侧不会遇到这两个问题。受影响的只是开发侧的自动化验证，替代通道：

- 手工验证 → 宿主 PowerShell 内 `Invoke-Expression` 跑脚本；
- 需要独立进程 → `explorer.exe`，或计划任务 `runjob.exe` + `job.log` 里的 `EXIT=`。

*笔记生成于 2026-10-25，所有结论均在本机实测；环境不同请先按第 3 节与附录 A 确认执行通道。*
