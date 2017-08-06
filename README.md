# sudoask: fake sudo which logs requests or uses preset responses

Pretty much the only time I have need of sudo is when working with Arch User Repository helpers 
(Otherwise I prefer machinectl shell, policykit (eek!), or daemons communicating via pipes for privileged operations).
In those cases it's only needed to have pacman install the package once built.
I otherwise don't have sudo installed but AUR helpers tend to utilise it.

This repository is a set of scripts that attempts to emulate common uses of sudo
(and later, probably su as well).
"Emulation" is done by echoing a request to a know pipe, where an admin can watch the request.
The requester will then wait for an exit status to be written to a reply pipe.
The fake sudo will then exit with that response code without actually doing anything.

It is intended that using this setup the admin will manually run the commands if they feel it should be so,
before writing an exit code to the reply pipe.
This is only intended for commands that should be rare/sensitive,
as evidently manual intervention can quickly get quite tedious.

Alternatively, a "presets" mechanism can be used.
A configuration directory in the caller's homedir can hold preset responses, addressed by hash of the command.
The preset response can include stdout and stderr data as well as an exit code.
This can be useful for commands that are called repeatedly where the result will not change frequently,
or to default to "permission denied" for some commands but not others.

The actual script which does this preset and request logic is wrapped by another script emulating sudo's cli interface.
This wrapper attempts to trap the most common options passed to sudo but will bail if something unfamiliar is used.
