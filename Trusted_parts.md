---
layout: wiki
title: Trusted_parts
permalink: /wiki/Trusted_parts/
---

Security-critical elements of Qubes OS
======================================

Summary
-------

As stated in the architecture document, the threat model for Qubes include:

-   a compromised VM
-   compromised Internet connectivity (e.g. rogue ISP)

Qubes's goal is to contain an attacker within an already compromised VM. There are a number of system components that are exposed to interaction with untrusted entities, and their compromise is fatal to Qubes security.

Trusted non-Qubes-specific components
-------------------------------------

-   Xen hypervisor
-   xenstore
-   network PV frontends (exposed to potentially compromised netvm) and backends
-   VMs networking stack. Note that in order to take control over VM without its cooperation (e.g. enticing user to visit a malicious web page) the attacker would have to compromise two firewalls first. Also, dom0 has no network connectivity, thus it is not exposed.
-   block backend implemented in dom0 kernel
-   integrity of Fedora packages (meaning, they are not trojaned)
-   rpm and yum (both in dom0 and in VMs) must correctly verify signatures of the packages
-   terminal emulators and file editors used in dom0 to view VM-influenced logs (although somehow exotic, in the past there were vulnerabilities in terminal emulators allowing for arbitrary code execution by just displaying malicious content on the terminal).

At the current project stage, we cannot afford to spend time to improve them - all we can do is to limit the number and extent of these components.

Trusted Qubes-specific components
---------------------------------

-   dom0-side libvchan library
-   GUI virtualization code in dom0 (*qubes-guid*)
-   sound virtualization code in dom0 (*pacat-simple-vchan*); note at the current state, it parses no data from VM (just passes raw audio frames to pulseaudio), so it should be safe
-   VM memory manager (*qmemman*)
-   qrexec-related code in dom0 (*qrexec\_daemon*)
-   some Qubes rpc servers. The servers implementing qubes.Filecopy, qubes.[ReceiveUpdates?](/wiki/ReceiveUpdates) and qubes.[SyncAppMenus?](/wiki/SyncAppMenus) must be bullet-proof. In case of qubes.OpenInVM and qubes.VMShell, their incarnation require explicit consent from the user, and such consent basically grants control over the target VM to the source VM, thus they are not critical.

It is the priority of the project to design the system so that the amount of this code is as limited as possible, and to code them securely.