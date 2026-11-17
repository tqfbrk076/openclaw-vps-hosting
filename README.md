# OpenClaw VPS hosting: how much RAM you need, what a year actually costs, and whether a $119.99 KVM special is enough (RackNerd one-click install, sizing rules, and the API bill)

OpenClaw needs three things from a server: full root access, Docker support, and storage that survives a reboot. Shared hosting fails on all three, which is why every host selling OpenClaw hosting sells it on a VPS. The sizing floor is 2 vCPU and 4 GB of RAM if you install it yourself over SSH; RackNerd's one-click installer inside its ctrl panel asks for 8 GB instead. Budget for two bills rather than one, because the VPS is only half of it: your model API usage goes straight to OpenAI, Anthropic, Google or your own local models, and none of the hosts below mark that up. RackNerd sits at the cheap, unmanaged end of this market, with KVM specials, full root SSH, an 8 GB plan at **$119.99/year**, and OpenClaw listed as a one-click app.

> **Quick pick:** the 8 GB KVM special at $119.99/year (7 vCPU, 8 GB RAM, 150 GB disk) is the one that matches RackNerd's own one-click requirement for OpenClaw. The 4 GB special at $59.99/year only makes sense if you're installing over SSH with Docker yourself and running one or two messaging channels. 👉 [Check the current RackNerd KVM specials and the one-click app list](https://bit.ly/RacKnerd)

## What OpenClaw actually needs from a VPS

Root access is not a preference here. It's what lets you install and roll back plugins, ClawHub skills and MCP connectors, and it's the layer that lets you pin a version instead of accepting whatever the host pushes. Docker runs the runtime the way the official documentation describes it. Persistent storage keeps sessions and skills across a reboot, so your agent doesn't forget its configuration every time the node restarts.

The version you'll most likely land on is OpenClaw 2.0, specifically v2026.8.1, which reworked installation, the Control UI, skills, automations, plugins and security. That's the release Bluehost ships in its image; on a self-managed box you decide when to move past it.

## OpenClaw VPS hosting prices at RackNerd

RackNerd sells "KVM VPS Specials" on annual prepay, and its ctrl panel includes a one-click app catalogue with OpenClaw, Hermes Agent, AnythingLLM, Ollama, LibreChat, n8n, Coolify and Portainer on it. These were the two tiers listed on the specials page when it was checked on 9 November 2026:

| Plan | vCPU / RAM / disk | Price | Billing | Order |
| --- | --- | --- | --- | --- |
| KVM VPS Special 4GB | 3 vCPU / 4 GB / 60 GB | $59.99/yr (about $5.00/mo) | Annual, paid upfront | [Order the 4 GB KVM special](https://bit.ly/RacKnerd) |
| KVM VPS Special 8GB | 7 vCPU / 8 GB / 150 GB | $119.99/yr (about $10.00/mo) | Annual, paid upfront | [Order the 8 GB KVM special](https://bit.ly/RacKnerd) |

Both are KVM virtual machines on Intel Xeon hardware with RAID-10 SSD storage and a 1 Gbps port, and both come with full root SSH, the ctrl panel (reboot, OS reinstall, HTML5 VNC console, root password reset, inbound firewall rules, upgrades), and OS choice across AlmaLinux, Rocky Linux, CentOS, Fedora, Debian and Ubuntu. Custom ISO installs go through a support ticket. Provisioning is usually automatic and quick; RackNerd's own ordering guide puts most deployments at 5 to 15 minutes.

RackNerd rotates these specials, so treat the table as the current state of the page rather than a permanent catalogue. If a tier disappears, the one-click catalogue and root access stay the same on whatever replaces it.

## 4 GB or 8 GB? Where the advice conflicts

RackNerd's own catalogue is stricter than the general guidance floating around. It lists OpenClaw at a **4 vCPU / 8 GB minimum**, next to Hermes Agent and AnythingLLM at 2 vCPU / 4 GB. Bluehost and OVHcloud both put the floor lower: Bluehost calls 2 vCPU / 4 GB / 100 GB NVMe the functional minimum and warns that below it, OpenClaw stops running reliably once you connect multiple channels and run several Automations; OVHcloud says 2 vCores and 4 GB is enough for basic onboarding, with heavier browser automation needing more memory and storage.

Both statements can be true at once. A 4 GB box with one Telegram channel and two scheduled jobs will run. Add WhatsApp, Slack and Discord, a few Automations and a browser session that's actually driving pages, and the same box gets tight. RackNerd's 8 GB requirement reflects the workload it expects the button to support, not a marketing upsell.

The price of following that advice is $60 a year, and it buys 4 GB more RAM, three more vCPU and 90 GB more disk. If you're buying a RackNerd special specifically for the one-click OpenClaw install, buy the 8 GB one. If you're comfortable running `docker` yourself, the 4 GB tier is a legitimate sandbox and a cheap way to find out whether OpenClaw earns a permanent slot in your stack.

One more consideration that changes the maths: if you point OpenClaw at local models through Ollama instead of a paid API, the API bill drops toward zero but the model competes with the agent for the same RAM. Running both on one 8 GB box is possible; it isn't roomy.

## What a month really costs, and why the headline numbers don't compare

Two costs, and only one of them shows up on the host's pricing page.

**The VPS.** RackNerd charges annually, so $119.99 is money leaving your account in one hit, and the "about $10/month" figure is arithmetic rather than a payment schedule. Contabo publishes prices that include tax and are calculated as a 24-month subscription equivalent, which is why its 4 vCPU / 8 GB / 100 GB tier shows €4.40/month for the first 24 months against a €5.50 display price; whoever renews after that pays the non-discounted rate. GoDaddy quotes a flat monthly figure with its Starter plan from $8.99/mo. Three different number formats, three different cash-flow realities.

**The API.** OpenClaw itself is free and open source, so what you actually spend on intelligence is whatever your chosen provider charges for the tokens your agent burns. GoDaddy states plainly that there are no per-message fees, no platform commissions and no markup on AI usage, and that what you spend goes to your provider rather than a middleman. Contabo frames the same structure as one flat monthly fee for the server plus your own model keys. Nobody in this comparison takes a cut of your tokens, so the API side is a function of how chatty your agent is, not of which host you pick.

👉 [Start with the RackNerd 8 GB special and a full year prepaid](https://bit.ly/RacKnerd)

## Three things that cause trouble after the install

**The gateway port.** OpenClaw ships with token or password authentication, but exposing the gateway directly to the public internet invites trouble, prompt injection included. OVHcloud's guidance is the one worth copying: harden SSH, write strict firewall rules, and reach the dashboard through Tailscale serve instead of opening ports to the world. RackNerd's ctrl panel has an inbound firewall you can use for the same job, and the HTML5 VNC console is your way back in if a rule locks you out of SSH.

**Updates.** Root means you own them. GoDaddy manages the security and uptime of the underlying VPS while leaving OpenClaw application updates to you; Bluehost maintains hardware, network and virtualization while the OS, configuration and applications are yours; Contabo supports the VPS itself and the initial one-click install, with maintenance and troubleshooting of the bundled open-source software left to the end user. RackNerd sells its specials as unmanaged KVM VPS and supports the platform layer. No host in this group maintains your OpenClaw install for you.

**Restarts.** OpenClaw records whether each Automations run fired, was delivered and completed, and Bluehost says missed runs resume after a restart without re-running the ones that already finished. That behaviour depends on the host coming back predictably, which is a reason to care about KVM isolation and a control panel that can reboot, reinstall and recover without a support ticket.

| Provider | What support covers | What stays with you |
| --- | --- | --- |
| RackNerd | The VPS platform, ctrl panel, initial one-click app install | OS, OpenClaw config, updates, security hardening |
| GoDaddy | VPS infrastructure, uptime, backups | OpenClaw application updates |
| Bluehost | Hardware, network, virtualization | OS, OpenClaw configuration, applications |
| Contabo | VPS, network, hardware, initial one-click setup | The open-source software inside the image |

## How the managed and preinstalled options compare

**GoDaddy** is the only one here that markets the absence of a terminal. Starter runs from $8.99/mo, deployment is one-click, and there's no Docker or SSH work. You still get root access if you want it, plus daily backups, 99.9% uptime, DDoS protection, snapshot backups and a free SSL for the first year. Setup is usually minutes, though GoDaddy warns it can occasionally stretch to 24 hours or longer. It suits someone who wants an agent answering WhatsApp at 2 a.m. without learning what a container is.

**Contabo** sells four preinstalled OpenClaw tiers: 4 vCPU / 8 GB / 100 GB at €4.40/mo, 6 vCPU / 12 GB / 200 GB at €6.00/mo, 12 vCPU / 48 GB / 400 GB at €20.00/mo and 18 vCPU / 96 GB / 600 GB at €39.20/mo, all for the first 24 months with tax included. Inbound traffic is unlimited and unmetered, outbound is unlimited under a fair-use policy. It's the cheapest route to a preinstalled image with a European provider, and the trade is a 24-month commitment and self-managed software.

**OVHcloud** bundles anti-DDoS on every VPS and gives you root to run either a Docker setup or a bare-metal `npm install` on Debian or Ubuntu. It's the pick if you want to build the runtime your way and don't mind that no price was published in the material available here.

**Bluehost** sells three NVMe tiers (NVMe 4, 8 and 16) with NVMe 4 as the functional minimum at 2 vCPU / 4 GB DDR5 / 100 GB NVMe, names its locations as Virginia, Arizona, London, Toronto and Amsterdam, and quotes no prices in the OpenClaw copy itself. Bluehost also states that NVMe 4 placed third out of 37 plans in VPSBenchmarks' Best VPS 2026 list for plans under $15, last updated September 2026, with the top network score in that group; that's a vendor citing a third-party benchmark, not an independent test you can audit from here.

## Which one fits you

- **You want an agent live today and no terminal:** GoDaddy Starter, $8.99/mo, one-click, application updates still yours.
- **You want root, a one-click install and the lowest annual outlay:** RackNerd's 8 GB KVM special at $119.99/year, then open One-Click Apps in ctrl and pick OpenClaw; credentials appear in the panel when it's ready.
- **You want the install done for you in Europe on a long contract:** Contabo's preinstalled tiers, cheapest at €4.40/mo for the first 24 months including tax.
- **You want to build the runtime yourself:** OVHcloud with Docker or npm, anti-DDoS included.

Two things to confirm before you pay. First, that the plan gives you Docker and persistent storage, since without them the plugin and skill ecosystem collapses and your run history resets on reboot; on RackNerd that means root SSH plus either a manual Docker install or the one-click Portainer app. Second, the billing period and what happens at renewal, because annual prepay and 24-month-equivalent pricing both make the first number look smaller than the total.

👉 [Pick a RackNerd KVM special, choose your datacenter, and install OpenClaw from the ctrl panel](https://bit.ly/RacKnerd)
