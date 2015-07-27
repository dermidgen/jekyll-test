---
layout: post
title:  "Standing Up a Pocketmine Server on Containers/Kubernetes"
date:   2015-07-27 04:58:00
categories: containers kubernetes minecraft mineserver pocketmine
author: Nick Caruso
---

# General Blog: Standing up a Pocketmine Server on Containers/Kubernetes

To follow up on this blog [General Blog: Working with Kubernetes and impact to Illumineto](/containers/kubernetes/2015/07/26/working-with-kubernetes.html), here's basically a short story of my adventures. Perhaps this is a great way for our next developer at Illumineto to get introduced to Containers. Give them the challenge of setting up Minecraft in the cloud. I'm thinking of writing this into a more professional blog for the next poor soul to learn from.

*So you want to run a Minecraft server for your kids and friends?**

The traditional path would be to run a server on your home PC, but that's a drag for a few reasons:

 1. You have to keep that server running full time.

 2. You have to expose ports in your internal home firewall.

Of course it's a lot cheaper running on a home server.

So go to the cloud, and use Containers. Here's the process and pains:

 1. Download a pre-built container from Docket Hub.

 2. Realize that a standard Minecraft server doesn't work with Minecraft on IOS devices. That took about 30 minutes of playing around.

 3. Find out that you need (PocketMine)[https://www.pocketmine.net/]. Repeat to download Pocketmine Container from Docket Hub.

 4. Get that running on Boot2Docker (needed when using a Mac). Map ports, etc.

 5. Get frustrated because IOS device isn't connected to server, what's wrong?

 6. Create a web app on Container to run a network test, that's working, what's wrong?

 7. Find out that the latest IOS minecraft client isn't compatible with released version of Pocketmine. Need to get the developer version of Pocketmine. Download the developer version to existing container.

 8. All is working now! Have fun for a few hours with my kids playing around with mods (they're called plugins in pocketmine), loading new worlds, etc. Getting a bunch of experience in Linux (did I mention I haven't played with Linux in almost 20 years since college).

 9. Ready for the challenge to deploy to Google Container Engine (GKE). the "k" is Kubernetes.

 10. This is a really long story with a bunch of try, fail, try, fail, try, fail...but lots of good experience, here are all the things that went wrong:

  * The original Container had the wrong pocketmine version, even though I updated it locally. But the update was persistent to the mounted disk, not the Container itself. So I had to create a new image and upload that to GKE. Good experience in basically forking an image.
  * My Replication-Controller and Service label/selectors were mistyped. So that was painful to figure out, but a great way to learn how to troubleshoot.
  * Minecraft runs on a UDP port. All the sample workshops use TCP, with the --load-balancer=true normally creating a public IP. So figuring out what to put instead (hint it's NodePort), and THEN creating an external IP. But why, when a node has it's own external port, but then truly understanding you're mapping an external port to a Service, not a node(s).
  * Accidentally swapping the wrong TCP and UDP ports. Minecraft doesn't need a TCP port, but I added a TCP website to my Container to help testing. This may be a great idea for other containers, to have a simple way to test. But stupidly all my configurations swapped the TCP and UDP port numbers.
  * Firewall hell, but great experience. Using NodePort creates a proxied port to your Service. So ensure the external port is what you put in the firewall. It makes complete sense now, but when struggling with UDP, Ports, Services, and Firewalls, it was confusing.
  * Ensuring a saved state by mounting an external disk. That actually wasn't as hard as I thought it would be.

In the end, it was super cool to mainly fail Containers and Pods and to see the RC automatically bringing them back up. It even happened so fast that the connection from the client (iPad running minecraft) to the server never went down! What happens if a node goes down, and who auto-recovers...the cluster with GKE, or GCE since nodes are just VMs? I've got to try that tonight...

So what are some lessons learned for me:

 * A Cluster represents a single unit. For now I can see Illumineto running a single Cluster.

 * A Container represents a logical application.

 * A Pod represents many Containers (i.e. applications) working together. A pod is really equal to a traditional VM in concept, that may have a web server (container), and a DB (container).

 * A Replication Controller (RC) ensures that your pods are running.

 * A Service is a really cool concept that for me, and is like a phone operator/smart switch board. There will be services to interface from the frontend public to map traffic to frontend Containers. Those containers are in pods, that are managed by RCs. Services also help containers and pods talk to other containers and pods. A frontend set of containers or pods talk to backend sets of pods and containers.

So where does all this run on? It runs of VMs called nodes, see below...

 * A Cluster has 1 or more nodes...for now GKE only supports up to 100 nodes. But a node can be up to 8 CPUs with 30 GB of memory, which in theory can support 1,000+ containers. A node is a managed VM for GKE, and looks like a VM in Google Compute Engine (GCE). There is ABSOLUTELY no relationship between a node and the concepts of Services, Replication-Controllers (RC), Pods, and Containers (..i think). This I really struggled with. While Services, RCs Pods, and Containers abstractly run on nodes, they span any nodes, or in the case of pods and containers may run on one or more nodes dynamically moving between nodes as necessary. So if you allocate 100 nodes for a cluster, consider the entirety of the nodes to represent one HUGE computer. All your containers run in that single HUGE computer. The only relationship I think is that a Pod cannot span multiple nodes. So it's important to consider that at most in GKE you'll have a node with 8 CPU and 30 GB of memory - which should be plenty.

A very long story short, but a successful weekend.

Now onto getting a MEAN stack configured to run in GKE, so we can make progress this week to having an v1 stack running with demonstrable code
