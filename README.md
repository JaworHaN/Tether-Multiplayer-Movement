# Tether-Multiplayer-Movement
Tether Multiplayer Movement Plugin Usage Guide

## Client Authoritative Movement System
This plugin’s movement system uses a client-authoritative approach combined with server-side smoothing to deliver a more immediate and natural-feeling movement experience in multiplayer games. Movement is calculated on the client, and the resulting position data is sent to the server; the server validates and replicates that data to other players while applying linear interpolation (LERP) smoothing to reconcile small position differences. Instead of abrupt corrections, position differences are eased together, producing a steadier, more natural flow between client and server.

The main advantage is that player control feels extremely instant and responsive: because movement is computed locally, input latency is effectively removed when a player presses a key, the character moves immediately. This is particularly noticeable in platformers, physics-driven games, and any multiplayer experience that requires precise control. The approach also preserves the local movement experience even under high-ping conditions: problems common to traditional replication (delayed movement, stuttering, sudden position jumps) are far less noticeable because the client is the primary source of motion. Server-side LERP smoothing further reduces rubberbanding and jitter by turning hard corrections into smooth adjustments, so both the local player and other clients perceive more stable, consistent movement. In fast-paced or constantly moving co-op scenarios, this method noticeably improves the multiplayer feel.

However, there is a significant downside: because movement is computed on the client, the system is theoretically vulnerable to cheating. Malicious users could manipulate client-side data to gain speed hacks, teleportation, or other unnatural behaviors. For that reason, this approach is not recommended for competitive PvP titles, ranked games, or any project that requires strong anti-cheat guarantees. In those cases, a server-authoritative model remains the safer option. For co-op games, casual multiplayer experiences, party games, or projects where strict competitive integrity is not required, this system can provide a smoother, more responsive, and overall more enjoyable movement experience.

## Player Tether System
The plugin also includes a player tether system that allows players to be connected together as if they were linked by a real rope. Once connected, their movement becomes physically related: if one player moves too far away, the connection begins to pull them back, creating the feeling that both characters are tied together by a flexible rope. This allows players to influence each other’s movement in a natural and intuitive way.

The system simulates rope-like behavior rather than simply limiting distance. Adjustable physics-based properties such as rope elasticity allow the connection to stretch slightly before applying tension, creating a more dynamic interaction between players. This makes the mechanic especially suitable for cooperative gameplay where players must coordinate their movement and positioning.

The tether system is also highly customizable. Developers can configure parameters such as maximum distance, elasticity, and pulling strength directly within the plugin. Multiple players can also be connected within the same system, enabling a variety of cooperative mechanics and group-based gameplay interactions.

# Usage

You can integrate most of the system quickly by making the existing BP_MyTetherCharacter class the parent class of your own character. This will automatically set up a large part of the required functionality.
If you only want to use the Movement Replication system, simply add the Movement Replication Component to your character and disable the Replicate Movement option on the character. After that, you can start using the movement system provided by the plugin. 
