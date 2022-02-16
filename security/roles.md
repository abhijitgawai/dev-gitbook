# Roles & permissions

### Configurator role

Configurator is a role which has permission to change all system parameters.

### Pausable admin role

Pausable admin is a role which has permission to pause contracts. Primarily, pausable admins are bots that pause contracts automatically if an anomaly was detected. If the attack was made for one of the virtual accounts, the pausing contract could protect other funds from attackers.

### Unpausable admin role

This roles can call unpause method. The role is designed separetlly from pausable admins to make this undescure action more protected.
