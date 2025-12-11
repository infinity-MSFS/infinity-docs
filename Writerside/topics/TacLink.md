# TacLink

Taclink is a cutting-edge multiplayer and networking framework engineered for high-performance military simulation within Microsoft Flight Simulator (MSFS). It enables a suite of advanced features designed to enhance the realism and immersion of multiplayer experiences, particularly for military aviation enthusiasts.

## Key Features
- **Air-to-Air TACAN**: TacLink provides a fully functional Tactical Air Navigation (TACAN) system, allowing aircraft to navigate using player-emitted navigation aids.
- **Synchronized Radar**: `WIP` TacLink enables real-time radar data sharing between players, allowing for accurate tracking of friendly and enemy aircraft.
- **Air-to-Air Refueling (AAR) Support**: `WIP` TacLink facilitates realistic air-to-air refueling operations, including tanker tracking and fuel transfer mechanics.
- **Combat Systems Integration**: `WIP` TacLink supports the synchronization of combat systems, including weapon targeting and engagement, across multiple players.
- **Damage Modeling**: `WIP` TacLink allows for shared damage states between players, ensuring that damage inflicted in multiplayer scenarios is accurately represented.
- **Low Latency Networking**: TacLink is optimized for low-latency communication, ensuring smooth and responsive multiplayer interactions even in complex scenarios.
- **Extensible Architecture**:  TacLink is designed with extensibility in mind, allowing developers to easily integrate new features and systems as needed.

## Integration
TacLink is built to seamlessly integrate with existing MSFS aircraft and systems. Developers can leverage TacLink's APIs to incorporate its features into their aircraft, enhancing the multiplayer experience without extensive modifications to existing systems.

### Air to-Air TACAN
TacLink's Air-to-Air TACAN system works by reading key simulation variables (L Vars) from MSFS that allow the application to build a TACAN signal based on the aircraft's position, heading, and distance to other players. This signal is then broadcasted to other TacLink-enabled aircraft in the multiplayer session, allowing them to tune into the TACAN channel and receive navigation information. The output is written back to MSFS L Vars, enabling cockpit instruments to display accurate TACAN data.
#### Variables
- `T38_UFCP_AAT_CHAN` - The TACAN channel being transmitted by the aircraft. (1-126)
- `T38_UFCP_AAT_BAND` - The TACAN band being transmitted by the aircraft. (0 = X, 1 = Y)
- `T38_UFCP_AAT_ACTIVE` - Whether the Air-to-Air TACAN is active (1) or inactive (0).
- `T38_NRST_TCN_DIS` - The bearing to the nearest TACAN station (0-359 degrees).
- 
**_Note that these variables are subject to change as TacLink is actively developed._**