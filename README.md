# DISCONNECTED (Spoofing Mod)

So I decided to actually lock in and learn C# by messing around with Duck Game's networking.
This is basically a "cheat" mod built on top of ImGui. It exploits the fact that the game blindly trusts the `connection` field in packets, allowing you to impersonate the host or other players.

## Features

### 1. Spoof Kick (F3)
Lists everyone in the lobby. You click a button, they get disconnected.
*   **How it works:** It sends a standard `NMKicked` packet to the target.
*   **The Exploit:** The game checks *who* sent the kick packet. I simply set the `packet.connection` field to `Network.host`. The victim receives the packet, sees it "came from the host," and obediently disconnects.

// Logic behind the kick
Send.Message(new NMKicked(targetProfile) 
{ 
    connection = Network.host // <--- The magic line
}, targetProfile.connection);
2. Chat Spoof (F4)
Lets you force other players to say whatever you want in the chat.
UI: Select a victim from the list on the left, type in the box, press Enter.
How it works: Constructs an NMChatMessage.
The Exploit: Similar to the kick, I overwrite the packet's origin to match the victim's connection.
Note: You won't see the message in your own chat (because the game doesn't loop back packets you "received" from others), but the entire lobby will see it coming from the victim. I added a local log window so you can track what you sent.
code
C#
// Logic behind the chat spoof
NMChatMessage packet = new NMChatMessage(victim, text, index);
packet.connection = victim.connection; // <--- Impersonating the victim
Send.Message(packet, NetMessagePriority.ReliableOrdered);
3. ImGui Implementation
I used IlyaKotomin/ImGuiModTemplate as a base but had to rewrite a bunch of stuff.
Input Fix: Duck Game tries to steal keyboard focus for gameplay. I wrote a manual input handler that captures keystrokes (including Shift + Symbols) and feeds them into ImGui when the menu is open.
Movement Lock: While you are typing, DuckGame.Input.ignoreInput is triggered so your duck doesn't run off a cliff.
Visuals: Supports colored nicks (renders the Vec3 colors properly) and strips those annoying |RED| tags from raw names.
Controls
F3 - Open Kick Menu
F4 - Open Chat Spoof Menu
Console Commands:
kickbind [Key] - Change kick menu key.
chatspoofbind [Key] - Change chat menu key.
Installation
Download the release.
Unpack the folder into: C:\Users\YOUR_USER\AppData\Roaming\DuckGame\Mods
Make sure the folder structure is correct (the .dll and content folder must be inside).
Disclaimer
I created this to test things out and learn reversing. Don't abuse it too much (or do, I don't care, just don't blame me if you get banned from lobbies).
