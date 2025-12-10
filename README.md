# **DISCONNECTED (Spoofing Mod)**

A learning project focused on exploring **Duck Game's networking**,
implemented as a cheat-style ImGui mod.\
The mod abuses the fact that the game blindly trusts the `connection`
field in network packets, allowing you to impersonate the host or any
other player.

## **Features**

### **1. Spoof Kick (F3)**

Shows a list of all players in the lobby. Click a button → they are
instantly disconnected.

**How it works:**\
Sends a standard `NMKicked` packet to the target.

**The Exploit:**\
Duck Game checks **who sent** the kick packet.\
By setting `packet.connection = Network.host`, the victim sees the
packet as if it came from the real host and disconnects.

``` csharp
// Kick spoof logic
Send.Message(new NMKicked(targetProfile)
{
    connection = Network.host // <--- The magic line
}, targetProfile.connection);
```

### **2. Chat Spoof (F4)**

Lets you force any player to say anything in chat.

UI: select a player on the left → type message → press Enter.

**How it works:**\
Creates an `NMChatMessage`.

**The Exploit:**\
Again, overwrite the connection to impersonate the victim:

``` csharp
// Chat spoof logic
NMChatMessage packet = new NMChatMessage(victim, text, index);
packet.connection = victim.connection; // <--- Impersonating the victim
Send.Message(packet, NetMessagePriority.ReliableOrdered);
```

**Notes:**\
- You won't see the spoofed message yourself, since the game doesn't
echo messages that "come from others."\
- A local log window is included to keep track of spoofed messages.

### **3. ImGui Integration**

Built using **IlyaKotomin/ImGuiModTemplate** as a base, heavily
modified.

#### ✔ Input Fix

Duck Game captures keyboard input aggressively.\
A manual input handler feeds keys (including shifted symbols) into ImGui
while the menu is open.

#### ✔ Movement Lock

While typing, `DuckGame.Input.ignoreInput` is enabled so your duck
doesn't run off cliffs.

#### ✔ Visual Enhancements

-   Properly renders colored nicknames\
-   Removes raw `|RED|` style tags

## **Controls**

  Action                 Key
  ---------------------- --------
  Open Kick Menu         **F3**
  Open Chat Spoof Menu   **F4**

### **Console Commands**

    kickbind [Key]       - Change kick menu key
    chatspoofbind [Key]  - Change chat menu key

## **Installation**

1.  Download the latest release.\

2.  Extract the folder into:

        C:\Users\YOUR_USER\AppData\Roaming\DuckGame\Mods

3.  Ensure the structure is correct:

    -   `.dll` in the mod folder\
    -   `content/` in the same folder

## **Disclaimer**

Created for **educational purposes** and networking experimentation.\
Use at your own risk.
