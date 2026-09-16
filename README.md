# 🔗 EtherChannel Configuration Using Manual ON Mode

## 📌 Project Overview

This project demonstrates the configuration and implementation of **Layer 2 EtherChannel using Manual (ON) Mode** in **Cisco Packet Tracer**.

In the previous labs, EtherChannel was configured using dynamic negotiation protocols:

* 🔹 **PAgP (Port Aggregation Protocol)** — Cisco proprietary
* 🔹 **LACP (Link Aggregation Control Protocol)** — IEEE standard

This project focuses on the third EtherChannel configuration method: **Manual / ON Mode**.

Unlike PAgP and LACP, **ON Mode does not use any negotiation protocol**. EtherChannel is formed only when the interfaces on **both sides of the connection are manually configured with `mode on`**.

The project also covers **channel groups, Port-Channel interfaces, trunk configuration, Layer 2 EtherChannel, STP interaction, verification, and troubleshooting**.

> 🎯 **Project Focus:** Layer 2 EtherChannel + Manual ON Mode + Port-Channel + Trunking + STP

# 📋 Case Study

A company wants to combine multiple physical links between its network switches into logical EtherChannel connections.

Previously, dynamic EtherChannel negotiation was implemented using PAgP and LACP. For this implementation, the company wants to configure EtherChannel **manually without using a negotiation protocol**.

To achieve this, **Manual ON Mode** is used.

In ON Mode, EtherChannel is formed only when the interfaces on **both connected switches are configured with `channel-group <number> mode on`**.

Because there is no negotiation between the switches, the configuration must be consistent on both sides.

### Requirements

1. 🔗 Multiple physical links must be combined into EtherChannel
2. ⚙️ EtherChannel must be configured using Manual / ON Mode
3. 🚫 No PAgP or LACP negotiation should be used
4. 🔢 Matching channel group numbers must be configured on both sides of each EtherChannel
5. 🌐 EtherChannel connections must operate as trunk links
6. 🛡️ STP must prevent Layer 2 loops in the redundant topology
7. 🧪 EtherChannel operation must be verified using Cisco IOS commands

# 🎯 Project Objectives

By completing this project, you will learn how to:

* 🔹 Understand Manual / ON Mode EtherChannel
* 🔹 Understand the difference between dynamic and manual EtherChannel
* 🔹 Configure EtherChannel using `mode on`
* 🔹 Create EtherChannel channel groups
* 🔹 Configure Port-Channel interfaces
* 🔹 Configure Port-Channel interfaces as trunk links
* 🔹 Combine multiple physical interfaces into one logical link
* 🔹 Understand EtherChannel and STP interaction
* 🔹 Configure multiple EtherChannel groups
* 🔹 Verify EtherChannel using Cisco IOS commands
* 🔹 Identify Manual ON Mode in EtherChannel verification output
* 🔹 Troubleshoot EtherChannel configuration issues

# 🏢 Network Design

The topology consists of multiple Cisco switches connected using redundant physical links.

Each EtherChannel is created by combining **3 physical links** between neighboring switches.

The topology creates a redundant Layer 2 path, allowing **STP** to prevent Layer 2 loops while EtherChannel treats each group of physical links as a single logical connection.

```text
                         ┌─────────────────┐
                         │    Switch 1     │
                         │                 │
                         │ Group 1         │
                         │ Group 2         │
                         └───────┬─────────┘
                           ║║║   │   ║║║
                    Group 1║║║   │   ║║║Group 2
                           ║║║   │   ║║║
                    ┌──────┴─────┴──────┐
                    │      Switch 2     │
                    │                   │
                    │ Group 2           │
                    │ Group 3           │
                    └────────┬──────────┘
                             ║║║
                         Group 3
                             ║║║
                    ┌────────┴──────────┐
                    │      Switch 3     │
                    │                   │
                    │ Group 3           │
                    └───────────────────┘

              ═══ 3 Physical Links = 1 EtherChannel
```

> ℹ️ **Note:** Exact physical interface numbers depend on the Packet Tracer topology. The important requirement is that the same physical links belonging to an EtherChannel are configured with the corresponding channel-group number on both switches.

# 🔄 EtherChannel Manual ON Mode

EtherChannel can be configured using three major methods:

| Method      | Protocol          | Configuration |
| ----------- | ----------------- | ------------- |
| **PAgP**    | Cisco Proprietary | Dynamic       |
| **LACP**    | IEEE Standard     | Dynamic       |
| **ON Mode** | No Negotiation    | Manual        |

In this project, **ON Mode** is used.

When an interface is configured with:

```bash
channel-group 1 mode on
```

the switch places the interface into **EtherChannel group 1 without using PAgP or LACP**.

### ⚠️ Important

For ON Mode EtherChannel to operate correctly:

```text
Switch A                         Switch B
────────                         ────────
channel-group 1 mode on   ↔      channel-group 1 mode on
```

Both sides must be manually configured for the corresponding EtherChannel.

There is **no negotiation** to verify whether the other side is correctly configured.

# ⚙️ Manual ON Mode Configuration Process

The configuration follows these main steps:

```text
1️⃣ Draw the network topology
        ↓
2️⃣ Identify switch-to-switch trunk links
        ↓
3️⃣ Select physical interfaces
        ↓
4️⃣ Configure channel-group with mode ON
        ↓
5️⃣ Configure the Port-Channel interface
        ↓
6️⃣ Configure Port-Channel as trunk
        ↓
7️⃣ Repeat on the neighboring switch
        ↓
8️⃣ Verify EtherChannel operation
```

# 🔧 Channel Group 1 Configuration

Channel Group 1 is configured between the first pair of switches.

### Switch 1

```bash
enable
configure terminal

interface range fa0/4 - 6
channel-group 1 mode on
exit

interface port-channel 1
switchport mode trunk
exit

do show etherchannel summary
```

### Switch 3

Configure the corresponding three physical interfaces connected to Switch 1:

```bash
enable
configure terminal

interface range fa0/1 - 3
channel-group 1 mode on
exit

interface port-channel 1
switchport mode trunk
exit
```

### Result

```text
Switch 1                         Switch 3
────────                         ────────
FA0/4 ──────────────── FA0/1
FA0/5 ──────────────── FA0/2
FA0/6 ──────────────── FA0/3
       ╲              ╱
        ╲ EtherChannel╱
         ╲  Group 1 ╱
```

The three physical interfaces are bundled into:

```text
Port-Channel 1
```

# 🔧 Channel Group 2 Configuration

Channel Group 2 is configured between **Switch 1 and Switch 2**.

### Switch 1

```bash
enable
configure terminal

interface range fa0/1 - 3
channel-group 2 mode on
exit

interface port-channel 2
switchport mode trunk
exit
```

### Switch 2

Configure the corresponding three physical interfaces:

```bash
enable
configure terminal

interface range fa0/1 - 3
channel-group 2 mode on
exit

interface port-channel 2
switchport mode trunk
exit
```

The three physical links are combined into:

```text
Port-Channel 2
```

# 🔧 Channel Group 3 Configuration

Channel Group 3 is configured between **Switch 2 and Switch 3**.

### Switch 2

```bash
enable
configure terminal

interface range fa0/4 - 6
channel-group 3 mode on
exit

interface port-channel 3
switchport mode trunk
exit
```

### Switch 3

Configure the corresponding interfaces:

```bash
enable
configure terminal

interface range fa0/4 - 6
channel-group 3 mode on
exit

interface port-channel 3
switchport mode trunk
exit
```

The three physical links are combined into:

```text
Port-Channel 3
```

# 🔢 EtherChannel Group Mapping

| Channel Group | Connected Switches  | Physical Links | Mode | Port-Channel   |
| ------------- | ------------------- | -------------: | ---- | -------------- |
| **Group 1**   | Switch 1 ↔ Switch 3 |              3 | ON   | Port-Channel 1 |
| **Group 2**   | Switch 1 ↔ Switch 2 |              3 | ON   | Port-Channel 2 |
| **Group 3**   | Switch 2 ↔ Switch 3 |              3 | ON   | Port-Channel 3 |

> 🎯 Each group combines **3 physical interfaces into 1 logical Port-Channel interface**.

# 🌐 Port-Channel Trunk Configuration

After creating the EtherChannel, the logical Port-Channel interface is configured as a trunk.

Example:

```bash
interface port-channel 1
switchport mode trunk
```

The same process is used for the other EtherChannel groups:

```bash
interface port-channel 2
switchport mode trunk
```

```bash
interface port-channel 3
switchport mode trunk
```

This allows the logical EtherChannel connection to carry VLAN traffic between the switches.

# 🔄 EtherChannel and STP

The topology contains redundant Layer 2 paths.

Without STP:

```text
Switch 1
   │
   │
Switch 2
   │
   │
Switch 3
   │
   └──────── Switch 1
```

This can create a **Layer 2 loop**.

STP detects the redundant path and places an appropriate path into a blocking state.

```text
              Switch 1
             /        \
            /          \
     EtherChannel    EtherChannel
          /              \
         /                \
    Switch 2 ─────────── Switch 3
             EtherChannel

                 ↓

        STP prevents the Layer 2 loop
```

Importantly, STP views an EtherChannel as a **single logical link**, rather than treating each bundled physical link as an independent path.

# 🧪 Verification

After configuration, verify the EtherChannel using:

```bash
show etherchannel summary
```

Example:

```text
Group  Port-channel  Protocol    Ports
------+-------------+-----------+----------------
1      Po1(SU)         -         Fa0/4(P)
                                 Fa0/5(P)
                                 Fa0/6(P)

2      Po2(SU)         -         Fa0/1(P)
                                 Fa0/2(P)
                                 Fa0/3(P)
```

### 🔍 Understanding the Output

In Manual / ON Mode, the **Protocol column is blank or shown as `-`**.

```text
Group     Port-Channel     Protocol
  1            Po1             -
```

This indicates that the EtherChannel is operating **without PAgP or LACP negotiation**.

### Port Status

```text
(P)
```

indicates that the physical interface is participating in the Port-Channel.

# 🛠️ Troubleshooting

If the EtherChannel does not form correctly, check the following:

### 1️⃣ Verify Both Sides Use ON Mode

```bash
show running-config
```

Look for:

```bash
channel-group 1 mode on
```

Both sides of the EtherChannel should use compatible manual ON configuration.

### 2️⃣ Verify Channel Group Numbers

For example:

```text
Switch A → channel-group 1 mode on
Switch B → channel-group 1 mode on
```

Make sure the intended physical links belong to the correct channel group.

### 3️⃣ Verify Port-Channel Status

```bash
show etherchannel summary
```

Check whether the physical interfaces appear as members of the Port-Channel.

### 4️⃣ Verify Trunk Configuration

```bash
show interfaces trunk
```

Check that the Port-Channel is operating as a trunk.

### 5️⃣ Check Physical Interfaces

```bash
show interfaces status
```

Make sure the physical interfaces are up and connected correctly.

### 6️⃣ Check STP

```bash
show spanning-tree
```

STP may place a redundant logical path into a blocking state to prevent a Layer 2 loop.

# ⚖️ Manual ON Mode vs Dynamic EtherChannel

| Feature                        | PAgP              | LACP             | ON Mode     |
| ------------------------------ | ----------------- | ---------------- | ----------- |
| Negotiation                    | ✅ Yes             | ✅ Yes            | ❌ No        |
| Protocol                       | Cisco Proprietary | IEEE Standard    | None        |
| Dynamic Formation              | ✅                 | ✅                | ❌           |
| Manual Configuration           | ❌                 | ❌                | ✅           |
| Modes                          | Auto / Desirable  | Active / Passive | On          |
| Requires Correct Configuration | ✅                 | ✅                | ⚠️ Critical |
| EtherChannel                   | ✅                 | ✅                | ✅           |

### Key Difference

```text
PAgP
Switch A ←→ Negotiation ←→ Switch B

LACP
Switch A ←→ Negotiation ←→ Switch B

ON Mode
Switch A ─────────────── Switch B
        Manual Configuration
        No Negotiation
```

# 🧠 Key Concepts

### 🔹 Manual / ON Mode

EtherChannel is manually configured without a negotiation protocol.

### 🔹 `channel-group`

Adds physical interfaces to an EtherChannel group.

Example:

```bash
channel-group 1 mode on
```

### 🔹 Port-Channel

The logical interface created from the bundled physical interfaces.

Example:

```bash
interface port-channel 1
```

### 🔹 Trunk

Allows VLAN traffic to travel between switches through the logical EtherChannel interface.

```bash
switchport mode trunk
```

### 🔹 No Negotiation

ON Mode does not use:

* ❌ PAgP
* ❌ LACP

Therefore, configuration consistency is especially important.

# 💻 Important Commands

### Enter Privileged Mode

```bash
enable
```

### Enter Global Configuration

```bash
configure terminal
```

### Select Multiple Interfaces

```bash
interface range fa0/1 - 3
```

### Configure Manual EtherChannel

```bash
channel-group 1 mode on
```

### Enter Port-Channel

```bash
interface port-channel 1
```

### Configure Trunk

```bash
switchport mode trunk
```

### Save Configuration

```bash
do write
```

or:

```bash
end
write memory
```

### Verify EtherChannel

```bash
show etherchannel summary
```

### Verify Trunk

```bash
show interfaces trunk
```

### Verify Running Configuration

```bash
show running-config
```

# 📊 Expected Verification

A successfully configured Manual ON Mode EtherChannel should show:

```text
Group     Port-Channel     Protocol
  1           Po1             -
  2           Po2             -
  3           Po3             -
```

The important point is:

```text
Protocol = -
```

because **no PAgP or LACP protocol is being used**.

The physical interfaces should also appear as members of the corresponding Port-Channel.

# 🎯 Project Outcome

After completing this lab, the network successfully implements **Layer 2 EtherChannel using Manual ON Mode**.

The project demonstrates:

* ✅ Multiple physical links bundled into EtherChannel
* ✅ Manual `mode on` configuration
* ✅ No PAgP or LACP negotiation
* ✅ Multiple EtherChannel groups
* ✅ Port-Channel interfaces
* ✅ Trunk configuration
* ✅ Redundant Layer 2 connectivity
* ✅ STP loop prevention
* ✅ EtherChannel verification using `show etherchannel summary`

# 📝 Conclusion

This project demonstrates the **Manual / ON Mode method of Layer 2 EtherChannel configuration**.

Unlike PAgP and LACP, ON Mode does not perform negotiation between connected switches. The administrator manually configures the physical interfaces using the same appropriate channel-group and `mode on` configuration on both sides.

The physical links are then represented as a single logical **Port-Channel**, which can be configured as a trunk to carry VLAN traffic between switches.

> 🚀 **Final Concept:**
> **ON Mode = Manual EtherChannel + No Negotiation + Matching Configuration on Both Sides**

# 🛠️ Technologies & Tools

* 🖥️ Cisco Packet Tracer
* 🔀 Cisco Layer 2 Switches
* 🔗 EtherChannel
* ⚙️ Manual / ON Mode
* 🌐 IEEE 802.1Q Trunking
* 🛡️ Spanning Tree Protocol (STP)
* 💻 Cisco IOS CLI

# 🏷️ Tags

`Cisco` `Networking` `EtherChannel` `Layer2` `PortChannel` `ManualMode` `OnMode` `Trunking` `STP` `PacketTracer` `Switching` `CCNA`
